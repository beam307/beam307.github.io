---
layout: post
title: 'Capacitor vs React Native 작동 원리 비교'
author: chanhee.kim
date: 2026-02-22 10:30
updated_at: 2026-02-22
tags: [react, react-native, capacitor, mobile, javascript]
image: /files/covers/cover-dev-09.jpg
description: "Capacitor와 React Native의 작동 원리를 비교합니다. WebView+네이티브 브리지 구조와 React Native New Architecture(Fabric, TurboModules, JSI)의 차이, 그리고 JS-네이티브 비동기 통신 메커니즘까지 정리했습니다."
keywords: "Capacitor vs React Native 작동 원리 비교, react, react native, capacitor, 모바일 앱 개발, 자바스크립트, 개발 블로그"
---

저는 실무에서 `Capacitor`를 주로 사용해 왔고, `React Native`도 한 번 실제 프로젝트에 적용해봤습니다.

이 글은 제가 다시 공식 문서와 기술 블로그를 확인하면서, 두 기술의 작동 원리와 JS-Native 비동기 통신 메커니즘을 한 번에 비교해 정리한 내용입니다.

`Capacitor`와 `React Native`는 둘 다 iOS/Android 앱을 만들 수 있지만, 내부 동작 방식은 꽤 다릅니다.


## 한눈에 보는 결론

- 웹 코드를 최대한 재사용하고 싶다면: **Capacitor**
- 네이티브 UI 성능/일관성을 더 중요하게 본다면: **React Native**
- 이미 React 웹팀이 있고 빠르게 앱을 내야 한다면: **Capacitor**
- 장기적으로 모바일 UX를 네이티브 수준으로 밀고 싶다면: **React Native**

## 1. Capacitor의 작동 원리

Ionic 팀 설명에 따르면 Capacitor는 "웹 앱을 네이티브 앱으로 감싸서 실행"하는 런타임 접근입니다.  
즉, 코드를 네이티브로 변환하는 크로스 컴파일러가 아니라, **WebView + 네이티브 브리지** 구조에 가깝습니다.

### 구조

```text
웹 앱(HTML/CSS/JS)
  -> iOS WKWebView / Android WebView
      -> Capacitor Bridge
          -> Native Plugin (Swift / Kotlin / Java)
```

### 빌드 흐름

제가 Capacitor 프로젝트를 세팅할 때는 보통 아래 순서로 진행합니다.

1. `npx cap add ios` / `npx cap add android`로 네이티브 프로젝트를 한 번 생성
2. 웹 앱 빌드 후 `npx cap sync`
3. `sync`는 내부적으로 `copy`(웹 자산 복사) + `update`(플러그인/네이티브 의존성 반영) 수행

### Capacitor의 요약

- UI 렌더링 주체: 브라우저 엔진(WebView)
- 네이티브 기능 접근: 플러그인 브리지 호출(Promise 기반)
- 강점: 기존 웹 코드 재사용성, 빠른 개발 속도
- 한계: 복잡한 애니메이션/리스트에서 WebView 특성의 한계를 만날 수 있음

## 2. React Native의 작동 원리

React Native는 WebView 위에서 DOM을 그리는 방식이 아니라, **네이티브 UI 컴포넌트 트리**를 직접 다루는 방식입니다.

공식 문서 기준으로 **React Native 0.82(2025-10-08)**부터는 Legacy Architecture와 기존 Bridge가 제거되고, New Architecture가 기본이 되었습니다.

### New Architecture 핵심 구성

```text
React 컴포넌트
  -> JS Runtime(Hermes 등)
      -> JSI
          -> TurboModules (Native API)
          -> Fabric (Native Rendering)
```

- `JSI`: JS와 네이티브 사이 통신을 기존 직렬화 브리지보다 직접적으로 처리
- `TurboModules`: 필요 시점 로딩(lazy loading) 가능한 네이티브 모듈 시스템
- `Fabric`: React의 최신 렌더링 모델과 맞춰 네이티브 UI 렌더링을 수행

### React Native의 요약

- UI 렌더링 주체: 네이티브 컴포넌트
- 네이티브 기능 접근: JSI + TurboModules
- 강점: 네이티브에 가까운 UI/성능, 대규모 모바일 앱 확장성
- 한계: 네이티브 빌드/모듈 이해도 요구, 러닝커브 존재

## 3. JS와 Native가 비동기로 통신하는 메커니즘

핵심은 두 프레임워크 모두 `요청 ID`를 기준으로, JS의 Promise와 Native 작업 결과를 다시 연결한다는 점입니다.

### 3-1. Capacitor의 비동기 통신 흐름

```text
JS(Plugin.call)
  -> Bridge에 plugin/method/options 전달
  -> Native Plugin 실행(백그라운드 작업 가능)
  -> 결과와 callbackId로 resolve/reject 반환
  -> WebView JS 런타임에서 Promise 완료
```

실제 흐름을 풀어서 보면 다음 순서입니다.

1. JS에서 `Camera.getPhoto()` 같은 플러그인 메서드를 호출하면 Promise가 생성됩니다.
2. Bridge는 `pluginId`, `methodName`, `options`, `callbackId`를 Native 쪽으로 전달합니다.
3. Native 플러그인이 실제 OS API를 비동기로 수행합니다.
4. 완료 시 `callbackId`를 포함해 성공/실패 결과를 Bridge로 돌려보냅니다.
5. JS 런타임이 해당 Promise를 `resolve/reject`합니다.

즉, WebView 메인 스레드를 오래 점유하지 않도록 Native 작업을 비동기로 처리하고, 완료 시점에만 JS 이벤트 루프로 결과를 되돌립니다.

### 3-2. React Native(New Architecture)의 비동기 통신 흐름

```text
JS(TurboModule method)
  -> JSI 경유 C++/Native 영역 진입
  -> Native에서 작업 큐로 비동기 처리
  -> CallInvoker 등을 통해 JS 런타임으로 완료 콜백 전달
  -> Promise resolve/reject + 필요 시 Fabric으로 UI 반영
```

New Architecture에서는 Legacy Bridge의 JSON 직렬화 왕복 대신 JSI 기반 통신을 사용합니다.

1. JS가 TurboModule 메서드를 호출합니다.
2. JSI를 통해 Native 코드에 진입합니다.
3. 시간이 걸리는 작업은 Native 스레드/큐에서 비동기로 수행합니다.
4. 완료 콜백을 JS 런타임 큐로 다시 보내 Promise를 완료합니다.
5. 결과로 UI 상태가 바뀌면 Fabric 렌더링 파이프라인에서 네이티브 UI 업데이트가 적용됩니다.

참고로 React Native는 일부 가벼운 API는 동기 호출도 가능하지만, 네트워크/파일 I/O/디스크 접근처럼 시간이 걸리는 작업은 비동기로 처리하는 것이 기본입니다. JS 스레드를 막지 않기 위해서입니다.

### 3-3. 공통 원리

- `요청-응답 상관관계`: `callbackId` 또는 내부 요청 핸들로 매칭
- `스레드 분리`: 오래 걸리는 Native 작업은 JS 메인 흐름 밖에서 처리
- `Promise 기반 복귀`: JS 쪽에서는 `await` 패턴으로 동일하게 소비
- `이벤트 통신`: 단발 응답 외에 센서/위치처럼 스트리밍은 listener 패턴 사용

## 4. Capacitor vs React Native 비교

| 비교 항목 | Capacitor | React Native |
|---|---|---|
| UI 렌더링 | WebView 내부 DOM/CSS | 네이티브 UI 컴포넌트 |
| 네이티브 API 호출 | Capacitor Bridge + Plugin | JSI + TurboModules |
| JS-Native 비동기 통신 | callbackId 기반 Promise 완료 | JSI+TurboModule+CallInvoker 기반 Promise 완료 |
| 웹 코드 재사용 | 매우 높음(기존 웹 앱 활용 용이) | 로직 재사용은 좋지만 UI는 별도 구성 |
| UI 성능 특성 | 일반 앱은 충분, 고난도 UI에서 제약 가능 | 고난도 인터랙션/리스트에서 유리 |
| 개발 진입장벽 | 웹 개발자에게 낮음 | 모바일/네이티브 개념 학습 필요 |
| 팀 구성 적합성 | 웹팀 중심 조직 | 모바일 제품 조직(장기 운영)에 유리 |

## 5. 어떤 경우에 무엇을 선택할까?

### Capacitor가 맞는 경우

- 기존 React/Vue/Angular 웹 서비스를 앱으로 빠르게 확장해야 함
- 한 팀이 웹과 앱을 동시에 운영해야 함
- 앱의 핵심 가치가 복잡한 네이티브 인터랙션보다 서비스 확장 속도에 있음

### React Native가 맞는 경우

- 모바일 UX 품질과 퍼포먼스가 제품 경쟁력 핵심임
- 장기적으로 모바일 전용 기능과 고성능 화면이 계속 늘어날 예정임
- 네이티브 모듈 연동/최적화를 감당할 팀 역량이 있음

제 경험으로는, 초기 출시 속도와 웹 자산 재사용이 중요할 때는 Capacitor가 정말 빠르게 성과를 냈고, 화면 복잡도와 인터랙션 품질을 더 밀어야 하는 상황에서는 React Native 접근이 더 설득력 있었습니다.


## 참고 자료

- Ionic Blog: [How Capacitor Works](https://ionic.io/blog/how-capacitor-works-2)
- Capacitor Docs: [JavaScript to Native](https://capacitorjs.com/docs/core-apis/web#javascript-to-native-bridge)
- React Native Docs: [React Native Architecture](https://reactnative.dev/architecture/landing-page), [What is React Native?](https://reactnative.dev/docs/getting-started)
- React Native Blog: [The New Architecture is here](https://reactnative.dev/blog/2024/10/23/the-new-architecture-is-here)
