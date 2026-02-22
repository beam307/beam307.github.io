---
layout: post
title: '[HTTP] HTTP 상태 코드 완벽 정리와 REST API 활용법'
author: chanhee.kim
date: 2017-12-07 11:03
updated_at: 2026-02-16
tags: [rest-api, http, backend, web-development]
image: /files/covers/cover-dev-05.jpg
description: "HTTP 상태 코드를 1xx~5xx까지 분류별로 정리하고, REST API에서 적절한 상태 코드 선택 기준을 제공합니다."
keywords: "HTTP 상태 코드 완벽 정리와 REST API 활용법, rest api, http, 백엔드, 웹 개발, 개발 블로그"
---

## 1. 100번대 : 정보성 응답(Informational)

  - 100 : 데이터 일부를 서버가 받은 상태
  - 101 : 프로토콜 전환 (WebSocket 등)

## 2. 200번대 : 성공(Success)

  - 200 : 에러 없이 정상 처리
  - 201 : 리소스 생성 성공 (POST 요청)
  - 204 : 정상 처리되었으나 서버에서 보내줄 데이터가 없음

## 3. 300번대 : 리다이렉션(Redirection)

  - 301 : 영구 이동 (Permanent Redirect)
  - 302 : 요청된 페이지가 새 URL로 변경되었음
  - 304 : 이미 기존의 데이터와 변경된 것이 없음

## 4. 400번대 : 클라이언트 오류(Client Error)

  - 400 : 전송된 Request에 문제가 있어서 서버가 인식할 수 없음
  - 401 : 인증 필요 (Unauthorized)
  - 403 : 서버에서 하락되지 않음
  - 404 : URL에 해당하는 자원을 찾을 수 없음
  - 405 : 요청에 지정된 방법을 사용할 수 없음
  - 406 : 전송방식이 허락되지 않음(REST에서 자주 발생)
  - 409 : 리소스 충돌 (Conflict)
  - 422 : 검증 실패 (Unprocessable Entity)
  - 429 : 요청 횟수 제한 초과 (Too Many Requests)

## 5. 500번대 : 서버 오류(Server Error)

  - 500 : 서버에서 처리시 문제가 발생
  - 502 : 게이트웨이나 프록시 상태의 문제(과부하 등)
  - 503 : 일시적인 과부하나 서비스 중단 상태
  - 504 : 지정된 처리시간이 지나서 처리되지 못함

---

## 6. REST API에서 자주 쓰는 상태 코드

### CRUD 작업별 권장 코드

| 작업 | HTTP 메서드 | 성공 시 코드 | 설명 |
|:---:|:---:|:---:|:---:|
| 조회(단건) | GET | 200 OK | 리소스 조회 성공 |
| 조회(목록) | GET | 200 OK | 리소스 목록 조회 성공 |
| 생성 | POST | 201 Created | 리소스 생성 성공 (Location 헤더 포함) |
| 수정(전체) | PUT | 200 OK | 리소스 전체 수정 성공 |
| 수정(부분) | PATCH | 200 OK | 리소스 부분 수정 성공 |
| 삭제 | DELETE | 204 No Content | 리소스 삭제 성공 |

### 예외 상황별 권장 코드

| 상황 | 코드 | 설명 |
|:---:|:---:|:---:|
| 로그인 필요 | 401 Unauthorized | 인증 실패 |
| 권한 부족 | 403 Forbidden | 인가 실패 |
| 리소스 없음 | 404 Not Found | 요청한 리소스 없음 |
| 입력 검증 실패 | 400 Bad Request / 422 Unprocessable Entity | 잘못된 요청 |
| 중복 리소스 | 409 Conflict | 이미 존재하는 리소스 |
| 서버 에러 | 500 Internal Server Error | 서버 내부 오류 |

### Spring 예시
```java
@RestController
@RequestMapping("/api/users")
public class UserApiController {
  
  @GetMapping("/{id}")
  public ResponseEntity<UserVO> getUser(@PathVariable Long id) {
    return ResponseEntity.ok(userService.findById(id)); // 200
  }

  @PostMapping
  public ResponseEntity<UserVO> createUser(@RequestBody UserVO user) {
    UserVO created = userService.create(user);
    URI location = URI.create("/api/users/" + created.getId());
    return ResponseEntity.created(location).body(created); // 201
  }

  @DeleteMapping("/{id}")
  public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
    userService.delete(id);
    return ResponseEntity.noContent().build(); // 204
  }
}
```
