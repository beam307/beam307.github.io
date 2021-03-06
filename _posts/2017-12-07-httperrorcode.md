---
layout: post
title: '[Spring] HTTP ERROR CODE '
author: chanhee.kim
date: 2017-12-07 11:03
tags: [etc]
image: /files/covers/blog.jpg
---

#### 1. 100번대 : 현재 데이터의 처리 중인 상태

  - 100 : 데이터 일부를 서버가 받은 상태

#### 2. 200번대 : 정상적인 응답

  - 200 : 에러 없이 정상 처리
  - 204 : 정상 처리되었으나 서버에서 보내줄 데이터가 없음

#### 3. 300번대 : 다른 URL처리

  - 302 : 요청된 페이지가 새 URL로 변경되었음
  - 304 : 이미 기존의 데이터와 변경된 것이 없음

#### 4. 400번대 : 서버에서 인식 할수 없음

  - 400 : 전송된 Request에 문제가 있어서 서버가 인식할 수 없음
  - 403 : 서버에서 하락되지 않음
  - 404 : URL에 해당하는 자원을 찾을 수 없음
  - 405 : 요청에 지정된 방법을 사용할 수 없음
  - 406 : 전송방식이 허락되지 않음(REST에서 자주 발생)

#### 5. 500번대 : 서버 내부의 문제

  - 500 : 서버에서 처리시 문제가 발생
  - 502 : 게이트웨이나 프록시 상태의 문제(과부하 등)
  - 503 : 일시적인 과부하나 서비스 중단 상태
  - 504 : 지정된 처리시간이 지나서 처리되지 못함
