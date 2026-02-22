---
layout: post
title: "React useEffect 실수 TOP3 정리 + useEffectEvent로 의존성/스테일 클로저 해결하기"
description: "React useEffect를 실무에서 헷갈리게 만드는 대표 실수 3가지(불필요한 Effect, 의존성 배열 오류, cleanup 누락)와 개선 방법을 정리합니다. 추가로 React useEffectEvent를 활용해 최신 state/props를 안전하게 읽는 패턴까지 예제로 설명합니다."
keywords: "React useEffect 실수 TOP3 정리 + useEffectEvent로 의존성/스테일 클로저 해결하기, react, 프론트엔드, 웹 개발, 개발 블로그"
author: chanhee.kim
date: 2026-02-08 16:33
tags: [react, frontend, web-development]
image: /files/covers/cover-dev-12.jpg
---

## useEffect 한 줄 정의: “외부 시스템과 동기화”
React 공식 문서의 핵심 관점은 단순합니다.

- `useEffect`는 **React 바깥(외부 시스템)** 과 컴포넌트를 **동기화**할 때 쓰는 “탈출구(escape hatch)”다.
- 외부 시스템이 없다면, **Effect가 필요 없을 가능성이 높다.**

(공식 문서: [You Might Not Need an Effect](https://react.dev/learn/you-might-not-need-an-effect))

---

# 실무에서 자주 하는 useEffect 실수 TOP3

## 1) 불필요한 useEffect 사용 (derived state / 렌더 계산을 effect로 처리)
가장 흔한 패턴이 “상태를 또 다른 상태로 동기화”하는 코드입니다.

### ❌ 안 좋은 예: state를 effect로 ‘복제’
~~~jsx
const [first, setFirst] = useState("");
const [last, setLast] = useState("");
const [fullName, setFullName] = useState("");

useEffect(() => {
  setFullName(`${first} ${last}`);
}, [first, last]);
~~~

이 경우 외부 시스템이 없고, 단순 계산이라서 **렌더링 중 계산**이 더 적합합니다.  
불필요한 Effect는 코드 흐름을 복잡하게 만들고 버그(렌더 타이밍/의존성)를 늘립니다.
간단한 state는 가독성이 괜찮지만 프로젝트가 복잡할 시 가독성은 급격히 떨어지고 사이드이펙트가 늘어나 디버깅하기가 어렵습니다.

> “렌더링을 위한 데이터 변환”은 Effect가 아니라 **렌더 계산**으로 해결하는 게 우선입니다.

(공식 문서: [Updating state based on props or state](https://react.dev/learn/you-might-not-need-an-effect#updating-state-based-on-props-or-state))

---

## 2) 의존성 배열(deps) 실수 (과도한 재실행 / 누락 / 객체·함수 deps)
의존성 배열 관련 실수는 크게 2가지로 나뉩니다.

### (A) 객체/함수 deps로 인해 effect가 “쓸데없이 자주” 재실행
컴포넌트 내부에서 만든 객체/함수는 렌더마다 새로 만들어질 수 있어, deps에 넣으면 effect가 불필요하게 반복됩니다.

~~~jsx
useEffect(() => {
  // ...
}, [options]); // options가 렌더마다 새 객체면 매번 실행될 수 있음
~~~

✅ 개선 방향(공식 문서 권장 요지)
- 불필요한 객체/함수 의존성 제거
- 외부로 빼거나, 필요한 “원시 값”만 deps에 넣기
- “반응형(reactive) 로직”과 “비반응형(non-reactive) 로직” 분리 고려

(공식 문서: `useEffect` [Caching expensive calculations](https://react.dev/learn/you-might-not-need-an-effect#caching-expensive-calculations))

### (B) deps 누락으로 인한 stale closure(스테일 클로저) / 의도치 않은 동작
deps를 “비워서([])” 고정하거나, 필요한 값을 빠뜨리면
Effect 내부에서 **오래된 state/props** 를 참조하는 문제가 생길 수 있습니다.

이 지점에서 `useEffectEvent`가 매우 유용합니다. (아래에서 자세히)

---

## 3) cleanup 누락 (특히 Strict Mode에서 더 잘 드러남)
구독/이벤트/타이머처럼 “설치(setup)”를 했다면, **반드시 해제(cleanup)** 해야 합니다.

### 대표 케이스
- `setInterval` / `clearInterval`
- `addEventListener` / `removeEventListener`
- 소켓 연결 / 해제
- 서드파티 위젯 초기화 / 제거

~~~jsx
useEffect(() => {
  const id = setInterval(() => {
    // ...
  }, 1000);

  return () => clearInterval(id);
}, []);
~~~

또한 React Strict Mode(개발 모드)에서는
**setup + cleanup을 한 번 더 실행**해서 cleanup이 제대로 “대칭”인지 점검합니다.  
cleanup이 빠져 있으면 개발 환경에서 문제(중복 구독/중복 타이머)가 더 빨리 튀어나옵니다.

(공식 문서: `useEffect` [Sharing logic between event handlers](https://react.dev/learn/you-might-not-need-an-effect#sharing-logic-between-event-handlers))

---

# useEffectEvent 추가: “최신 state/props는 읽고 싶지만, effect를 재실행하진 않게”
문서: https://react.dev/reference/react/useEffectEvent

## useEffectEvent란?
`useEffectEvent`는 Effect 내부에서 사용하는 로직 중,
**반응형(reactive)으로 다루고 싶지 않은 부분**을 분리해
“Effect Event”라는 함수로 만들 수 있게 해주는 Hook입니다.

공식 문서가 강조하는 대표 목적은:
- **Effect 안에서 최신 props/state 읽기**
- 하지만 그 값 때문에 **Effect 자체가 다시 실행되게 만들지 않기**

(공식 문서: [useEffectEvent](https://react.dev/reference/react/useEffectEvent))

---

## 언제 유용한가? (실전 시나리오)
- “roomId가 바뀔 때만” 서버 연결을 다시 하고 싶다
- 그런데 연결 과정에서 “최신 알림 설정/유저 정보” 같은 값도 쓰고 싶다
- 그 값들을 deps에 넣으면 effect가 너무 자주 재실행됨 → 원치 않는 재연결/재구독

이런 상황에서 `useEffectEvent`로 “최신 값 읽기”를 분리하면,
deps는 깔끔하게 유지하면서 stale closure를 줄일 수 있습니다.

---

## 예시 패턴: reactive deps는 최소화 + 최신 값은 Effect Event로 읽기
(아래 예시는 “형태”를 보여주는 샘플입니다)

~~~jsx
import { useEffect, useState } from "react";
import { useEffectEvent } from "react";

function ChatRoom({ roomId, soundEnabled }) {
  // ✅ 최신 값을 읽되, 이것 때문에 effect가 재실행되진 않도록 분리
  const onMessage = useEffectEvent((message) => {
    if (soundEnabled) {
      // 최신 soundEnabled를 사용
      playSound();
    }
    showToast(message);
  });

  useEffect(() => {
    const connection = createConnection(roomId);

    connection.on("message", (msg) => {
      onMessage(msg); // 최신 값을 안전하게 사용
    });

    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ roomId만 바뀔 때 재연결
}
~~~

핵심 포인트:
- Effect는 “외부 시스템과의 동기화(연결/해제)”만 담당
- “최신 옵션/상태 읽기”는 `useEffectEvent`로 분리
- deps가 과도하게 커지지 않아서 의도한 타이밍만 재실행됨

---

# 체크리스트: useEffect 쓰기 전에 한번 생각 해보기

1. **외부 시스템 동기화인가?** (DOM API/구독/네트워크/타이머/서드파티 위젯)
2. 단순 계산/파생 값이면 **렌더에서 계산**할 수 없는가?
3. 유저 클릭/입력 등 “이벤트”라면 **이벤트 핸들러**에서 처리하는 게 맞지 않은가?
4. setup을 했다면 **cleanup이 대칭**으로 존재하는가?
5. deps에 **객체/함수**가 들어가서 불필요한 재실행을 만들고 있지 않은가?
6. deps 누락으로 **stale closure** 위험이 있지 않은가?
7. “최신 값은 필요하지만 재실행은 싫다”면 **useEffectEvent로 분리**할 수 있는가?

---

## 요약
- `useEffect`는 “외부 시스템 동기화”를 위한 도구다.
- 실수 TOP3는 보통 **불필요한 Effect**, **deps 실수**, **cleanup 누락**에서 터진다.
- deps가 커지거나 stale closure가 고민이라면, **useEffectEvent로 비반응형 로직을 분리**해서
  “재실행 타이밍”과 “최신 값 읽기”를 동시에 만족시킬 수 있다.

---

## 공식 문서
- useEffectEvent: https://react.dev/reference/react/useEffectEvent
- useEffect: https://react.dev/reference/react/useEffect
- You Might Not Need an Effect: https://react.dev/learn/you-might-not-need-an-effect
