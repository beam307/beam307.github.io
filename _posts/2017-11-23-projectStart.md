---
layout: post
title: '스방(study) 프로젝트 시작'
author: chanhee.kim
date: 2017-11-23 10:12
tags: [java,project]
image: /files/covers/blog.jpg
---

###. 컨텐츠 및 페이지 회의

* 기존사이트 자료조사
1. 스터디서치
- 선결제 가능
- 카테고리(지역/시간)
- 신규(마감임박)
2. 스페이스 클라우드
- 메인 2개 (호스트)[등록/소비자]
- 공지사항 (아코디언)
- 카테고리(전체 | 시간 | 월 | 일)
- 해시태그(댓글, 좋아요)
- 필터(구분) -설정 / 체크박스
- 전화걸기 | 길찾기 | 지도
- 댓글기능 -> 방주인page
- 비슷한걸 추천
- 예약신청 -> 결제 -> 승인대기. <선결제 / 후결제>
3. 모꼬지
- 검색 (제목 / 태그)
- 장소 / 카테고리 / 비용

---

### 프로젝트 스토리보드 초안
1.메인
- 로그인, 회원가입, 마이페이지 -> 구글 / 네이버/ 자체(이메일로그인?)
- 공지사항, 마이페이지, 로그아웃, 문의사항(1:1)
- 추천스터디
- 방
- 최신리스트 스터디 3x2썸네일
- 최신리스트 방 3x2썸네일 <br>
 1-1 회원가입
   - 이름(실명)
   - 생년월일 / 성별
   - 전화번호 / 인증
   - 메일

2. 마이페이지
- 정보수정 => 회원탈퇴도
- 자기소개
- 이미지
- 관심분야(지역 / 카테고리)
3. 리스트페이지  => 스터디 / 방
- 분류(대 / 소)
  * 대---> 상단에 메뉴바   
  * 소---> 중단 셀렉트
-  지역(필터) 셀렉트)<br>
~~비용 --> (시크바) (삭제)~~
- 스터디 리스트  정렬: ( 신청자수 / 최신순)
  썸네일(이미지 / 태그), 신규등록, 마감임박 , 추가모집
4. 등록페이지
 -카테고리
 - 지역
 - 인원
 - 비용
 - 일정  -->달력
 - 소개  --> 텍스트
 - 이미지 --> 스터디커버
5. 상세페이지
<br>
- 제목
- 상단 요약 프로필
- 이미지
<br>
- 소개글
- 상세정보
- 지역
- 인원
- 비용
- 일정      --신청버튼/ 취소버튼