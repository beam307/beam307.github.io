---
layout: post
title: '[spring] RestController'
author: chanhee.kim
date: 2018-02-17 16:54
tags: [spring]
image: /files/covers/blog.jpg
---

##### @RestController

- @RestController 어노테이션은 각 메소드에 @ResponseBody 어노테이션을 추가 할 필요 없이, @Controller와 @ResponseBody의 조합으로 기능한다.
- Spring version 4부터 새로운 @RestController 어노테이션을 사용할 수 있다.

##### 댓글구현 예제(프로젝트파일 참조)
- 페이지 부분은 코드가 길어져 뺏습니다.

- controller.java  컨트롤러 부분 코드
``` java
@RestController
@RequestMapping("/replies")
public class ReplyController {

	@Inject
	private ReplyService replyService;

	@RequestMapping(value = "", method = RequestMethod.POST)
	public ResponseEntity<String> register(@RequestBody ReplyVO vo) { //댓글추가
		ResponseEntity<String> entity = null; // ResponseEntity : 데이터 + http status code
		try {
			replyService.addReply(vo);
			entity = new ResponseEntity<String>("SUCCESS", HttpStatus.OK);
		} catch (Exception e) {
			e.printStackTrace();
			entity = new ResponseEntity<String>(e.getMessage(), HttpStatus.BAD_REQUEST);
		}
		return entity;
	}

  @RequestMapping(value = "/all/{studyNo}", method = RequestMethod.GET)
	public ResponseEntity<List<ReplyVO>> list(@PathVariable("studyNo") Integer studyNo) { // 댓글목록
		ResponseEntity<List<ReplyVO>> entity = null;

		try {
			entity = new ResponseEntity<>(replyService.listReply(studyNo), HttpStatus.OK);
		} catch (Exception e) {
			e.printStackTrace();
			entity = new ResponseEntity<>(HttpStatus.BAD_REQUEST);
		}
		return entity;
	}

	@RequestMapping(value = "/{replyNo}", method = { RequestMethod.PUT, RequestMethod.PATCH })
	public ResponseEntity<String> update(@PathVariable("replyNo") Integer replyNo, @RequestBody ReplyVO vo) { // 댓글수정
		ResponseEntity<String> entity = null;
		try {
			vo.setReplyNo(replyNo);
			replyService.modifyReply(vo);

			entity = new ResponseEntity<String>("SUCCESS", HttpStatus.OK);
		} catch (Exception e) {
			e.printStackTrace();
			entity = new ResponseEntity<String>(e.getMessage(), HttpStatus.BAD_REQUEST);
		}
		return entity;
	}

	@RequestMapping(value = "/{replyNo}", method = RequestMethod.DELETE)
	public ResponseEntity<String> remove(@PathVariable("replyNo") Integer replyNo, @RequestBody ReplyVO vo) { // 댓글삭제
		ResponseEntity<String> entity = null;
		try {
			replyService.removeReply(replyNo, vo.getStudyNo());
			entity = new ResponseEntity<String>("SUCCESS", HttpStatus.OK);
		} catch (Exception e) {
			e.printStackTrace();
			entity = new ResponseEntity<>(e.getMessage(), HttpStatus.BAD_REQUEST);
		}
		return entity;
	}
}
```

- service.java 서비스 부분 코드
```java
@Service
public class ReplyServiceImpl implements ReplyService {

	@Inject
	private ReplyDAO replyDAO;

	@Inject
	private StudyDAO studyDAO;

  @Transactional // 트랜잭션처리
	@Override
	public void addReply(ReplyVO vo) throws Exception { // 댓글추가
		replyDAO.create(vo); // 댓글생성
		studyDAO.updateReplyCnt(vo.getStudyNo(), 1); //게시글 댓글수 up
	}

	@Override
	public List<ReplyVO> listReply(Integer studyNo) throws Exception { // 댓글목록
		return replyDAO.list(studyNo); // 댓글리스트
	}

	@Override
	public void modifyReply(ReplyVO vo) throws Exception { // 댓글수정
		replyDAO.update(vo); // 댓글업데이트
	}

	@Transactional
	@Override
	public void removeReply(Integer replyNo,Integer studyNo) throws Exception { // 댓글삭제
		replyDAO.delete(replyNo); // 댓글삭제
		studyDAO.updateReplyCnt(studyNo, -1); // 게시글 댓글수 down
	}
}
```

- dao.java DAO 부분 코드
```java
@Repository
public class ReplyDAOImpl implements ReplyDAO {

	@Inject
	private SqlSession session;

	private static String namespace = "org.sbang.mapper.ReplyMapper";

	@Override
	public List<ReplyVO> list(Integer studyNo) throws Exception { // 댓글목록
		return session.selectList(namespace + ".list", studyNo);
	}

	@Override
	public void create(ReplyVO vo) throws Exception { // 댓글생성
		session.insert(namespace + ".create", vo);
	}

	@Override
	public void update(ReplyVO vo) throws Exception { // 댓글수정
		session.update(namespace + ".update", vo);
	}

	@Override
	public void delete(Integer replyNo) throws Exception { // 댓글삭제
		session.delete(namespace + ".delete", replyNo);
	}
}
```
- mapper.xml 코드

``` xml
<mapper namespace="org.sbang.mapper.ReplyMapper">
	<select id="list" resultType="ReplyVO"> <!-- 댓글목록 -->
		select * from reply where studyNo = #{studyNo} order by replyNo desc
	</select>

	<insert id="create"> <!-- 댓글생성 -->
		insert into reply (studyNo, replyText, userNo) values (#{studyNo}, #{replyText}, #{userNo})
	</insert>

	<update id="update"> <!-- 댓글수정 -->
		update reply set replyText = #{replyText}, replyUpdateDate = now() where replyNo = #{replyNo}
	</update>

	<delete id="delete"> <!-- 댓글삭제 -->
		delete from reply where replyNo = #{replyNo}
	</delete>
</mapper>
```

- reply.jsp // html은빼고 javascript부분 생성부분만 작성

``` javascript
/* 댓글 추가 */
$("#replyAddBtn").on("click", function() {
	var replytextObj = $("#newReplyText");  // 댓글내용
	var replyer = $('#userNo').val(); // 댓글 글쓴이 번호
	var replyText = replytextObj.val(); // 댓글내용

	if (replyText != "") {
		$.ajax({
			type : 'post',
			url : '/replies/',
			headers : {
				"Content-Type" : "application/json",
				"X-HTTP-Method-Override" : "POST"
			},
			dataType : 'text',
			data : JSON.stringify({
				studyNo : studyNo,
				userNo : replyer,
				replyText : replyText
			}),
			success : function(result) {
				if (result == 'SUCCESS') {
					replyPage = 1;
					getPage("/replies/" + studyNo + "/" + replyPage); // 댓글 리프레쉬 함수
					replytextObj.val(""); // 댓글내용 초기화
				}
			}
		});
	} else {
		alert("댓글을 입력해주세요");
		return;
	}
});
// 이하 수정, 삭제 생략
```
