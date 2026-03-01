---
layout: post
title: "Angular DI(Dependency Injection)에 모든 것: 기초, 계층형 Injector"
description: "Angular 의존성 주입(DI)을 IoC와 느슨한 결합 관점에서 쉽게 설명하고, providedIn: 'root'와 component providers 차이, useClass/useValue/useFactory, InjectionToken 실무 예제를 한 번에 정리합니다."
keywords: "Angular DI, Angular Dependency Injection, Angular 의존성 주입, Angular Injector, providedIn root, component providers, InjectionToken, useFactory, Angular 서비스 싱글톤, Angular DI 실무"
author: chanhee.kim
date: 2026-03-01 14:40
updated_at: 2026-03-01
tags: [angular, frontend, oop, web-development, javascript]
image: /files/covers/cover-dev-10.jpg
---

# Angular DI, 이제는 "서비스 가져다 쓰기"에서 끝내지 말자

Angular를 쓰다 보면 DI를 이렇게 이해하기 쉽습니다.

- `constructor`에 서비스 넣기

처음엔 이 정도면 충분합니다.
하지만 프로젝트가 커지면 금방 한계가 옵니다.

이 글에서는 DI를 문법이 아니라 **설계 도구**로 설명합니다.

**Angular 의존성 주입(DI)**은 단순한 문법이 아니라, 테스트 용이성과 유지보수성을 동시에 높이는 아키텍처 핵심입니다.

---

## 1. 도입: DI란 무엇인가?

DI(Dependency Injection)는
객체가 필요한 의존성을 직접 만들지 않고,
**외부에서 주입받는 방식**입니다.

핵심은 두 가지입니다.

- **IoC(제어의 역전)**: 객체 생성/생명주기 제어를 프레임워크에 위임
- **Loose Coupling(느슨한 결합)**: 구체 구현과의 결합을 낮춰 교체를 쉽게 만듦

### Bad (DI 미사용)

```typescript
class PaymentComponent {
  // 컴포넌트가 직접 생성
  private paymentService = new PaymentService();

  checkout() {
    this.paymentService.pay();
  }
}
```

문제:

- 테스트에서 Mock으로 바꾸기 어렵습니다.
- 구현 교체 시 컴포넌트까지 수정됩니다.
- 컴포넌트가 UI + 생성 책임까지 떠안습니다.

### Good (DI 사용)

```typescript
@Injectable({ providedIn: 'root' })
export class PaymentService {
  pay() {
    // 결제 로직
  }
}

@Component({
  selector: 'app-payment',
  template: `<button (click)="checkout()">결제</button>`,
})
export class PaymentComponent {
  constructor(private paymentService: PaymentService) {}

  checkout() {
    this.paymentService.pay();
  }
}
```

실무에서 DI가 필수인 이유는 단순합니다.

- **테스트가 쉬워짐**: Mock 주입이 자연스럽다.
- **유지보수가 쉬워짐**: 구현을 갈아껴도 소비자 코드는 안정적이다.
- **협업이 쉬워짐**: 역할이 분리되어 충돌이 줄어든다.

---

## 2. Angular DI의 3가지 핵심 요소

Angular DI는 아래 흐름으로 보면 쉽습니다.

- **Consumer**: 의존성을 필요로 하는 쪽 (컴포넌트/서비스)
- **Dependency**: 주입되는 대상 (서비스/설정 객체)
- **Injector + Provider**: 무엇을 어떻게 만들지 결정하고 전달하는 주체

```typescript
@Injectable({ providedIn: 'root' })
export class UserService {
  getUsers() {
    return [];
  }
}

@Component({
  selector: 'app-user-list',
  template: `<p>Users</p>`,
})
export class UserListComponent {
  // Consumer가 Dependency를 요청
  constructor(private userService: UserService) {}
}
```

동작 순서:

1. Consumer가 토큰(`UserService`)을 요청
2. Injector가 Provider 설정을 확인
3. 인스턴스를 생성 또는 재사용해 전달

---

## 3. [핵심] 계층적 주입(Hierarchical Injectors)

여기서 Angular DI의 실전 감각이 갈립니다.
**어디에 Provider를 두느냐**가 핵심입니다.

### `providedIn: 'root'` vs `@Component({ providers: [...] })`

| 항목 | `providedIn: 'root'` | `@Component({ providers: [...] })` |
|---|---|---|
| 범위 | 애플리케이션 전역 | 해당 컴포넌트 인스턴스 트리 |
| 인스턴스 | 보통 1개(싱글톤) | 컴포넌트마다 독립 인스턴스 |
| 적합한 대상 | 인증, 공통 API, 로깅 | 탭 상태, 위저드 상태, 샌드박스 세션 |
| 주의점 | 전역 상태 오염 가능 | 과도하게 쪼개면 구조가 복잡해짐 |

## 4. 예시

### 4-1. 탭(Tab) 위젯 상태 분리

#### Bad (전역 싱글톤 사용)

```typescript
@Injectable({ providedIn: 'root' })
export class TabStateService {
  activeTab = 'overview';
}
```

페이지에 탭 위젯이 여러 개 있으면
`activeTab`이 서로 영향을 줄 수 있습니다.

#### Good (컴포넌트 레벨 주입)

```typescript
@Injectable()
export class TabStateService {
  activeTab = 'overview';
}

@Component({
  selector: 'app-tab-group',
  providers: [TabStateService],
  template: `
    <button (click)="state.activeTab = 'overview'">Overview</button>
    <button (click)="state.activeTab = 'settings'">Settings</button>
    <p>현재 탭: {{ state.activeTab }}</p>
  `,
})
export class TabGroupComponent {
  constructor(public state: TabStateService) {}
}
```

이제 탭 그룹 인스턴스마다 상태가 분리됩니다.

### 4.2. 샌드박스 패턴

샌드박스 패턴은 쉽게 말해
**"같은 화면의 기능을 서로 독립된 작업실처럼 분리하는 방식"**입니다.

코드 실행기, 폼 빌더, 탭형 편집기에서 특히 중요합니다.
한 화면에 인스턴스가 여러 개 떠도 상태가 섞이지 않아야 하기 때문입니다.

#### Bad (전역 싱글톤 공유)

```typescript
@Injectable({ providedIn: 'root' })
export class SandboxStateService {
  code = '';
  logs: string[] = [];
}
```

이 상태에서 샌드박스가 2개 있으면
왼쪽에서 `reset`했을 때 오른쪽 로그도 같이 지워질 수 있습니다.

#### Good (컴포넌트 단위 분리)

```typescript
@Injectable()
export class SandboxStateService {
  code = '';
  logs: string[] = [];

  run() {
    this.logs.push(`run: ${this.code}`);
  }

  reset() {
    this.code = '';
    this.logs = [];
  }
}

@Component({
  selector: 'app-sandbox',
  providers: [SandboxStateService],
  template: `
    <app-sandbox-editor></app-sandbox-editor>
    <app-sandbox-console></app-sandbox-console>
  `,
})
export class SandboxComponent {}
```

정리하면:

- 같은 `app-sandbox` 내부 자식은 같은 상태를 공유
- 다른 `app-sandbox` 인스턴스와는 완전히 분리
- 인스턴스별 `reset`/`run`이 가능해 디버깅이 쉬워짐

---

## 5. 고급 DI 설정 방법

기본 생성자 주입만으로는 실무 요구를 다 못 맞춥니다.
여기서 `Provider` 옵션을 꺼내 쓰게 됩니다.

### 5-1. `useClass`: 구현 교체가 필요할 때

```typescript
export abstract class Logger {
  abstract log(message: string): void;
}

@Injectable()
export class ConsoleLogger implements Logger {
  log(message: string) {
    console.log('[console]', message);
  }
}

@Injectable()
export class RemoteLogger implements Logger {
  log(message: string) {
    // 서버 전송
  }
}

export const appProviders = [
  { provide: Logger, useClass: ConsoleLogger },
];
```

언제 쓰나:

- 개발/운영 구현 교체
- 레거시 구현에서 새 구현으로 점진 이전

---

### 5-2. `useValue`: 설정값(Config), 상수 주입

```typescript
export interface AppConfig {
  apiBaseUrl: string;
  enableNewCheckout: boolean;
}

export const APP_CONFIG = new InjectionToken<AppConfig>('APP_CONFIG');

const config: AppConfig = {
  apiBaseUrl: 'https://api.example.com',
  enableNewCheckout: true,
};

bootstrapApplication(AppComponent, {
  providers: [{ provide: APP_CONFIG, useValue: config }],
});
```

언제 쓰나:

- 환경별 API URL
- 피처 플래그
- 빌드 타임/런타임 상수

---

### 5-3. `useFactory`: 실행 시점에 값 골라서 주입

쉽게 말하면,
**앱이 실행될 때 현재 상황을 보고 값을 결정한 뒤 주입하는 방식**입니다.

예를 들어:

- 로컬 개발이면 로컬 API 주소
- 스테이징이면 스테이징 API 주소
- 운영이면 운영 API 주소

이처럼 "미리 고정하기 어려운 값"은 `useValue`보다 `useFactory`가 더 맞습니다.

```typescript
export const API_BASE_URL = new InjectionToken<string>('API_BASE_URL');

export function apiBaseUrlFactory(): string {
  return location.hostname.includes('staging')
    ? 'https://staging-api.example.com'
    : 'https://api.example.com';
}

bootstrapApplication(AppComponent, {
  providers: [{ provide: API_BASE_URL, useFactory: apiBaseUrlFactory }],
});
```

동작은 이렇게 이해하면 됩니다.

1. Angular가 `API_BASE_URL`이 필요하다고 판단
2. `useFactory`에 등록된 `apiBaseUrlFactory()`를 실행
3. 함수가 현재 환경을 보고 URL을 계산해 반환
4. 반환된 값을 의존성으로 주입

언제 쓰나:

- 호스트/플랫폼에 따라 값 분기
- 여러 의존성 조합으로 객체 생성

---

### 5-4. `InjectionToken`: 인터페이스/값 주입의 핵심

`interface`는 런타임에 사라집니다.
그래서 Injector 토큰으로 사용할 수 없습니다.

#### Bad (문자열 토큰)

```typescript
providers: [{ provide: 'CONFIG', useValue: config }];
```

#### Good (`InjectionToken`)

```typescript
export const APP_CONFIG = new InjectionToken<AppConfig>('APP_CONFIG');
providers: [{ provide: APP_CONFIG, useValue: config }];
```

장점:

- 타입 안정성
- 토큰 충돌 방지
- 리팩터링 안정성

---

## 6. 정리

- 전역 공유가 필요한가? `providedIn: 'root'`
- 컴포넌트 인스턴스마다 분리돼야 하는가? `@Component.providers`
- 구현을 바꿔 끼워야 하는가? `useClass`
- 상수/설정 주입인가? `useValue + InjectionToken`
- 런타임 계산이 필요한가? `useFactory`
- 테스트에서 Mock 주입이 쉬운 구조인가? `같은 토큰에 Mock Provider를 주입`
