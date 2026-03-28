---
layout: post
title: "antd vs shadcn/ui 비교: 어떤 React UI를 선택해야 할까?"
description: "antd와 shadcn/ui를 공식 문서 기준으로 비교합니다. 설치 방식, 커스터마이징, 디자인 일관성, 번들/성능, 팀 적합성까지 한 번에 정리했습니다."
keywords: "antd vs shadcn, antd vs shadcn/ui 비교, ant design shadcn 차이, react ui library 비교, react component library 추천, 프론트엔드 ui 라이브러리"
author: chanhee.kim
date: 2026-03-28 16:07
updated_at: 2026-03-28
tags: [react, frontend, web-development]
image: /files/covers/cover-dev-10.jpg
---

React 프로젝트를 시작할 때 UI 선택지는 많지만, 실무에서 자주 비교되는 조합은 `antd`와 `shadcn/ui`다.

둘 다 React에서 빠르게 화면을 만들 수 있다는 점은 같지만, 접근 방식은 꽤 다르다.  
`antd`는 **완성형 컴포넌트 라이브러리**에 가깝고, `shadcn/ui`는 **내 코드베이스 안으로 가져와 직접 소유하는 UI 세트**에 가깝다.

현재 공식 문서 기준 `antd`는 **6.3.4**, `shadcn/ui`는 `init/add` CLI와 `Radix/Base UI` 선택 구조를 제공하고 있다.

---

## 요약

- 관리자 화면, 대시보드, 폼/테이블 중심 서비스라면: **antd**
- 마케팅 페이지, SaaS 프로덕트, 브랜드 커스터마이징이 중요한 서비스라면: **shadcn/ui**
- 팀이 "일단 빨리 많이 만들어야 한다"면: **antd**
- 팀이 "디자인 시스템을 우리 코드로 오래 가져가야 한다"면: **shadcn/ui**

---

## antd와 shadcn/ui의 핵심 차이

| 항목 | antd | shadcn/ui                       |
|---|---|---------------------------------|
| 기본 성격 | 완성형 React UI 라이브러리 | 코드 배포형 UI 세트                    |
| 설치 방식 | `npm install antd` 후 import | CLI로 필요한 컴포넌트 코드를 프로젝트에 추가      |
| 기본 스타일 | 일관된 Ant Design 스타일 | 미니멀한 기본 스타일, 직접 수정 전제           |
| 커스터마이징 | 토큰/테마 중심 | 코드 직접 수정 중심                     |
| 컴포넌트 수 | 매우 많음 | 필요한 것만 선택 추가                    |
| 러닝커브 | 낮음 | 낮지 않음(Tailwind/CSS 변수 구조 이해 필요) |
| 업데이트 방식 | 라이브러리 버전 업 | 내 코드와 의존성 같이 관리                 |
| 잘 맞는 제품 | 백오피스, 대시보드, CRUD | 제품 UI, 디자인시스템 적용된 서비스       |

---

## 1. 설치와 시작 속도

### antd: 바로 쓸 수 있는 완성형

`antd`는 설치 후 바로 import해서 사용할 수 있다.

```tsx
import { Button, DatePicker, Table } from "antd";
```

공식 소개 페이지 기준으로 `antd`는 엔터프라이즈 웹 애플리케이션용 UI 프레임워크를 지향하고,  
컴포넌트 수가 매우 많다. 실제로 `Form`, `Table`, `DatePicker`, `Modal`, `Drawer`, `Upload`처럼  
실무에서 자주 쓰는 조합이 이미 잘 갖춰져 있다.

즉, **관리자 화면을 빨리 만드는 속도**는 대체로 `antd` 쪽이 더 좋다.

### shadcn/ui: 필요한 것만 가져오는 방식

`shadcn/ui`는 전통적인 "패키지 import" 스타일과 다르다.  
CLI로 필요한 컴포넌트를 내 프로젝트에 추가하는 방식이다.

```bash
pnpm dlx shadcn@latest init
pnpm dlx shadcn@latest add button dialog form
```

공식 문서도 `shadcn/ui`를 **전통적인 컴포넌트 라이브러리라기보다, 코드 배포 방식**으로 설명한다.  
즉, 가져다 쓰는 게 아니라 **내 프로젝트 안에 컴포넌트 파일이 들어온다**.

이 방식은 초반 세팅은 조금 더 필요하지만, 장기적으로는 유연하다.

---

## 2. 커스터마이징은 누가 더 쉬운가?

### antd: 토큰 기반 커스터마이징

`antd`는 `ConfigProvider`와 디자인 토큰 기반 커스터마이징이 강하다.

{% raw %}
```tsx
import { ConfigProvider } from "antd";

export default function App() {
  return (
    <ConfigProvider
      theme={{
        token: {
          colorPrimary: "#0f766e",
          borderRadius: 10,
        },
      }}
    >
      {/* app */}
    </ConfigProvider>
  );
}
```
{% endraw %}

공식 문서 기준으로 `antd` 5+는 CSS-in-JS 기반 테마 시스템을 제공하고,  
6.x에서는 `zeroRuntime` 모드도 지원한다.  
즉, **"브랜드 컬러/둥근 모서리/밀도" 같은 전역 규칙**을 적용하는 데 강하다.

다만 컴포넌트 내부 DOM 구조까지 강하게 바꾸고 싶을 때는 `shadcn/ui`보다 답답할 수 있다.

### shadcn/ui: 코드 직접 수정

`shadcn/ui`의 강점은 여기서 나온다.  
버튼, 다이얼로그, 셀렉트 같은 UI가 **내 코드로 들어오기 때문에 그대로 수정**할 수 있다.

```tsx
// components/ui/button.tsx
export function Button({ className, ...props }: ButtonProps) {
  return (
    <button
      className={cn(
        "inline-flex h-10 items-center rounded-md px-4 text-sm font-medium",
        className
      )}
      {...props}
    />
  );
}
```

공식 문서 기준으로 `shadcn/ui`는 CSS Variables 기반 테마를 권장하고,  
Tailwind CSS를 전제로 스타일링한다.

즉, `shadcn/ui`는 **디자인 시스템을 라이브러리 밖에서 억지로 덮는 구조가 아니라, 아예 코드 자체를 팀 자산으로 가져가는 방식**이다.

결론적으로:

- **전역 테마 수정**은 `antd`가 편하다
- **컴포넌트 구조 자체를 수정**하려면 `shadcn/ui`가 더 편하다

---

## 3. 디자인 일관성과 기본 완성도

### antd는 "정해진 시스템"이 강하다

`antd`는 처음부터 디자인 언어가 강하다.  
버튼, 입력창, 테이블, 모달, 네비게이션이 한 세트처럼 움직인다.

이건 특히 아래 같은 상황에서 유리하다.

- 백오피스 화면이 많다
- 폼과 테이블이 많다
- 디자인보다 기능 우선이다
- 여러 개발자가 동시에 화면을 찍어내야 한다

즉, **디자인 판단 비용을 줄여준다**.

### shadcn/ui는 "좋은 출발점"에 가깝다

`shadcn/ui`는 기본 스타일이 깔끔하지만, `antd`처럼 강한 제품 언어를 밀어붙이진 않는다.  
오히려 그 점이 장점이다.

- 제품 톤앤매너를 직접 만들기 쉽다
- Tailwind/CSS 변수 기반으로 미세 조정이 쉽다
- 마케팅 페이지와 앱 화면을 같은 언어로 묶기 좋다

대신 팀이 디자인 원칙 없이 들어가면, 화면마다 느낌이 달라질 수 있다.

---

## 4. 컴포넌트 범위와 실무 생산성

### antd는 특히 Form/Table 계열이 강하다

`antd`의 가장 큰 실무 장점은 풍부한 컴포넌트다.

- `Form`
- `Table`
- `DatePicker`
- `Tree`
- `Select`
- `Upload`
- `Drawer`
- `Modal`
- `Notification`

이런 조합을 직접 설계하지 않고 빠르게 연결할 수 있다.  
관리자 시스템, 내부 운영 도구, B2B SaaS 백오피스라면 이 장점이 매우 크다.

### shadcn/ui는 필요한 것만 쌓는 방식

`shadcn/ui`도 폼, 다이얼로그, 드롭다운, 시트, 토스트 등 주요 조각은 충분히 제공한다.  
게다가 최근 문서 기준으로 `Radix`와 `Base UI` 선택도 가능하다.

하지만 `antd`처럼 거대한 완성형 세트라기보다,  
**"팀이 필요한 조각을 가져와 조립하는 방식"**에 더 가깝다.

그래서 제품이 아래에 가까울수록 잘 맞는다.

- 커스텀 UI가 많다
- 기본 컴포넌트를 계속 수정하게 된다
- 브랜드 일관성이 중요하다
- 디자인 시스템을 내부 자산으로 키우고 싶다

---

## 5. 번들, 런타임, 성능 관점

이 비교는 "무조건 누가 더 빠르다"로 단순화하기 어렵다.  
대신 **어디서 비용이 생기는가**를 봐야 한다.

### antd

- 장점: 검증된 컴포넌트가 많아 직접 구현 비용이 줄어듦
- 장점: ES modules 기반 tree shaking 지원
- 장점: 6.x에서는 `zeroRuntime` 모드 지원
- 주의점: 무거운 화면에서 많은 고수준 컴포넌트를 한 번에 쓰면 체감 비용이 생길 수 있음

### shadcn/ui

- 장점: 필요한 컴포넌트만 추가 가능
- 장점: 코드 소유권이 내 쪽에 있어 불필요한 추상화 제거가 쉬움
- 장점: Tailwind/CSS 변수 조합으로 스타일 제어가 단순한 편
- 주의점: 성능이 자동으로 좋아지는 것은 아님
- 주의점: 결국 팀이 추가한 코드 품질에 따라 차이가 커짐

즉, 성능만 보고 고르면 안 된다.  
`antd`는 **완성도와 생산성의 비용 구조**,  
`shadcn/ui`는 **유연성과 소유권의 비용 구조**라고 보는 편이 맞다.

---

## 6. 어떤 팀에 무엇이 맞을까?

### antd가 더 잘 맞는 팀

- 운영툴, 관리자, 대시보드 개발이 많다
- 폼/테이블/권한 관리 화면이 많다
- 디자이너 리소스가 제한적이다
- 팀원마다 UI 구현 편차를 줄이고 싶다
- 빠르게 MVP가 아니라 빠르게 "업무용 완성 화면"을 내야 한다

### shadcn/ui가 더 잘 맞는 팀

- 제품 UI를 브랜드에 맞게 세밀하게 만들고 싶다
- Tailwind 기반 개발 문화가 이미 있다
- 컴포넌트를 우리 코드 자산으로 관리하고 싶다
- Next.js/Vite 기반 프론트엔드 제품팀이다
- 장기적으로 사내 디자인 시스템을 키우고 싶다

---

## 7. 내 기준 추천

제가 실무에서 선택한다면 기준은 꽤 단순하다.

### `antd`를 고를 때

- "이번 분기 안에 관리자 화면을 많이 만들어야 한다"
- "Table/Form/DatePicker가 핵심이다"
- "팀 전체가 같은 UI 언어를 빠르게 공유해야 한다"

이 경우는 `antd`가 훨씬 실용적이다.

### `shadcn/ui`를 고를 때

- "디자인 시스템을 우리가 소유해야 한다"
- "컴포넌트를 계속 뜯어고칠 가능성이 높다"
- "브랜드 경험이 제품 경쟁력이다"

이 경우는 `shadcn/ui`가 더 오래 버틴다.

---

## 최종 정리

`antd vs shadcn/ui` 비교는 단순히 "예쁜가?"의 문제가 아니다.  
결국 아래 두 질문에 대한 답이다.

1. 우리는 **완성형 라이브러리**가 필요한가?
2. 아니면 **우리 코드베이스 안에서 디자인 시스템을 직접 키울 것인가?**

빠른 실무 생산성, 풍부한 기본 컴포넌트, 관리자 화면 중심이라면 `antd`.  
브랜드 커스터마이징, 코드 소유권, 제품 UI 확장성을 더 중시한다면 `shadcn/ui`.

둘 중 무엇이 더 좋다고 단정하기보다,  
**"우리 팀의 제품 성격과 유지보수 방식에 더 맞는가"**로 고르는 게 맞다.

---

## 참고 자료

- [Ant Design](https://ant.design/)
- [shadcn/ui](https://ui.shadcn.com/)

