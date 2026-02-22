---
layout: post
title: '[React] react-error-boundary 완벽 정리: ErrorBoundary는 만능이 아니다 (언제 써야 할까?)'
author: chanhee.kim
date: 2026-02-07 14:20
tags: [react, frontend, web-development]
image: /files/covers/blog.jpg
description: "React ErrorBoundary(Error Boundary) 사용법부터 한계(이벤트/비동기/SSR 에러 미포착)까지. 렌더링 에러를 안전하게 처리하는 실전 적용 전략과 예시 코드로 정리합니다."
keywords: "react-error-boundary 완벽 정리: ErrorBoundary는 만능이 아니다 (언제 써야 할까?), react, 프론트엔드, 웹 개발, 개발 블로그"
---

## React ErrorBoundary(Error Boundary)란?

React에서 컴포넌트가 렌더링 중 에러를 던지면, 화면 전체가 깨지거나(화이트 스크린) 앱이 멈춰 보일 수 있습니다.  
**ErrorBoundary**는 이런 상황에서 **하위 컴포넌트 트리에서 발생한 렌더링 에러를 포착**하고, 앱 전체가 죽지 않도록 **대체 UI(fallback UI)** 를 보여주는 “UI 안전장치”입니다.

> 핵심: ErrorBoundary는 **에러 처리 로직**이 아니라, **렌더링 붕괴를 막는 UI 보호막**에 가깝습니다.

---

## ErrorBoundary가 잡아주는 에러 범위 (✅ 되는 것)

ErrorBoundary가 포착할 수 있는 대표 케이스는 아래와 같습니다.

- 컴포넌트 **렌더링(render) 과정**에서 발생한 에러
- 클래스 컴포넌트 기준 **생명주기(lifecycle)** 에서 발생한 에러
- **자식 컴포넌트 트리** 내부에서 발생한 에러

예시:

~~~jsx
<ErrorBoundary>
  <MyComponent />
</ErrorBoundary>
~~~

`MyComponent`가 렌더링 중 예외를 던지면, ErrorBoundary가 fallback UI로 대체 렌더링합니다.

---

## ErrorBoundary는 만능이 아니다 (❌ 안 되는 것)

많이 하는 오해는 “ErrorBoundary로 감싸면 앱 에러가 다 잡힌다”인데, **그렇지 않습니다.**  
ErrorBoundary는 **React 렌더링 사이클 안에서** 발생한 에러만 안전하게 다룰 수 있습니다.

### ErrorBoundary가 잡지 못하는 대표 케이스

1) **이벤트 핸들러 내부 에러**
- `onClick`, `onChange` 등 사용자 액션 핸들러에서 발생한 예외

2) **비동기 코드의 에러**
- `setTimeout`, `Promise`, `async/await` 내부에서 발생한 예외

3) **SSR(서버 사이드 렌더링) 중 에러**

4) **ErrorBoundary 자신에서 발생한 에러**

---

## 왜 이벤트/비동기 에러는 ErrorBoundary로 안 잡힐까?

ErrorBoundary는 **렌더링 중** 발생한 예외를 기준으로 “fallback UI로 교체”하는 방식입니다.

- 렌더링 중 에러: React가 UI 트리를 다시 그리면서 대체 가능 ✅
- 이벤트/비동기 에러: 렌더링이 끝난 뒤 발생 → React가 자동으로 UI를 교체할 컨텍스트가 없음 ❌

즉, **ErrorBoundary는 “렌더링 단계의 안전장치”** 이고,  
**이벤트/비동기 단계의 에러는 별도 전략**이 필요합니다.

---

## React 에러 처리 실전 전략 (ErrorBoundary + 직접 처리)

### 1) 이벤트 핸들러 에러 처리: try/catch로 직접 잡기

~~~js
function handleClick() {
  try {
    // 위험한 로직
  } catch (error) {
    console.error(error);
    // 필요하면 사용자에게 토스트/알림 표시
  }
}
~~~

### 2) 비동기 에러 처리: Promise 체인/async 처리로 잡기

~~~js
async function fetchData() {
  try {
    const res = await fetch("/api/data");
    if (!res.ok) throw new Error("Network error");
    return await res.json();
  } catch (error) {
    console.error(error);
    // 상태로 에러를 관리하거나, 재시도 UI를 제공
  }
}
~~~

> 네트워크 에러/비동기 에러는 대개 ErrorBoundary보다  
> **TanStack Query**이나 **로딩/에러 상태(state) 기반 UI 패턴**으로 처리하는 쪽이 가독성이 좋습니다.

---

## 언제 ErrorBoundary를 써야 할까? (판단표)

| 상황 | ErrorBoundary 사용 |
| --- | --- |
| 렌더링 에러(컴포넌트 JSX 평가 중) | ✅ |
| 생명주기 에러(클래스 컴포넌트 기준) | ✅ |
| 이벤트 핸들러(onClick 등) 에러 | ❌ |
| 비동기(Promise/async/timeout) 에러 | ❌ |
| API 요청 실패/서버 응답 오류 | ❌ (직접 처리 권장) |
| 특정 섹션만 깨져도 되는 UI 보호 | ✅ (강력 추천) |

---

## ErrorBoundary 적용 위치: “전체 감싸기”보다 “구역 감싸기”

실무에서는 App 전체를 1개 ErrorBoundary로 감싸기보다, **깨졌을 때 영향 범위를 줄이고 싶은 구역**에 배치하는 게 효율적입니다.

추천 단위:
- 라우트(페이지) 단위
- 탭/패널 단위
- 위젯/카드 리스트 단위
- 외부 라이브러리 의존도가 높은 영역(차트, 에디터 등)

> 목표: “앱 전체 다운”이 아니라 “부분 장애”로 격리하기

---

## 요약
 
- ErrorBoundary는 **렌더링 에러 전용** 안전장치다.
- 이벤트/비동기/SSR 에러는 **별도 처리**가 필요하다.
- 실무에서는 **페이지/섹션 단위로 적용**해 장애 범위를 줄이는 게 핵심이다.
- ErrorBoundary는 “에러 처리 만능키”가 아니라 “UI 붕괴 방지용 보호막”이다.
