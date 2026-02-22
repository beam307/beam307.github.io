---
layout: post
title: "[Next.js] App Router에서 서버/클라 경계 설계 실수 TOP 7 (실무 패턴 + 샘플)"
description: "Next.js App Router에서 Server/Client 경계를 잘못 잡아 생기는 대표 실수 7가지를 정리합니다. use client 남발, 데이터 패칭 위치, Provider 전염, Server Actions, env 노출, hydration mismatch까지 실무 패턴과 샘플로 해결합니다."
keywords: "App Router에서 서버/클라 경계 설계 실수 TOP 7 (실무 패턴 + 샘플), next.js, react, 프론트엔드, 웹 개발, 개발 블로그"
author: chanhee.kim
date: 2026-02-15 11:03
tags: [nextjs, react, frontend, web-development]
image: /files/covers/cover-code-03.jpg
---

## 왜 “서버/클라 경계”가 App Router의 핵심인가?

App Router(Next.js 13+)는 기본이 **Server Component**입니다.  
즉, 컴포넌트가 **어디서 실행되는지(서버/브라우저)**에 따라 가능한 API, 번들 크기, 렌더링 방식이 크게 달라집니다.

실무에서 문제는 보통 이 셋으로 귀결됩니다.

- 클라에서 해야 할 일을 서버에서 하려다 깨짐
- 서버에서 할 수 있는 일을 클라로 끌고 와서 성능/보안 손해
- Provider/상태/이벤트 때문에 경계가 무너짐

아래 TOP 7은 가장 자주 터지는 패턴 + 바로 적용 가능한 해결책입니다.

---

## TOP 1. `use client`를 습관처럼 붙여서 “페이지 전체를 클라”로 만들어버림

### 증상
- 페이지 전체 번들이 커짐(초기 로딩/INP 악화)
- 서버 패칭/캐시/SEO 이점이 사라짐

### 안 좋은 예(샘플)
```tsx
// app/dashboard/page.tsx
'use client';

import {useEffect, useState} from 'react';

export default function DashboardPage() {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch('/api/stats').then(r => r.json()).then(setData);
  }, []);

  return <pre>{JSON.stringify(data, null, 2)}</pre>;
}
```

### 권장 패턴: 서버는 데이터/조합, 클라는 인터랙션만(샘플)
```tsx
// app/dashboard/page.tsx (Server Component)
import DashboardClient from './DashboardClient';

async function getStats() {
  const res = await fetch('https://internal-api.example.com/stats', {
    cache: 'no-store',
  });
  return res.json();
}

export default async function DashboardPage() {
  const stats = await getStats();
  return <DashboardClient initialStats={stats}/>;
}
```

```tsx
// app/dashboard/DashboardClient.tsx (Client Component)
'use client';

import {useState} from 'react';

export default function DashboardClient({initialStats}: { initialStats: any }) {
  const [stats, setStats] = useState(initialStats);

  return (
    <>
      <h1>Dashboard</h1>
      <button onClick={() => setStats({...stats, refreshedAt: Date.now()})}>
        새로고침(샘플)
      </button>
      <pre>{JSON.stringify(stats, null, 2)}</pre>
    </>
  );
}
```

**핵심:** `use client`는 “페이지 전체”가 아니라 **딱 필요한 컴포넌트에만**. 
하지만 꼭 최적화를 할 필요는 없다 작은 페이지/컴포넌트는 `use client`로 빠르게 개발해도 무방

---

## TOP 2. Server Component에 이벤트/상태/브라우저 API를 섞어버림

### 증상
- `onClick` 넣자마자 에러
- `window`, `document`, `localStorage` 접근 시 런타임 에러

### 올바른 분리(샘플)
```tsx
// app/products/page.tsx (Server)
import FilterBar from './FilterBar';

export default async function ProductsPage() {
  const initialFilters = {q: '', onlySale: false};
  return (
    <>
      <h1>Products</h1>
      <FilterBar initialFilters={initialFilters}/>
    </>
  );
}
```

```tsx
// app/products/FilterBar.tsx (Client)
'use client';

import {useState} from 'react';

export default function FilterBar({initialFilters}: { initialFilters: any }) {
  const [filters, setFilters] = useState(initialFilters);

  return (
    <div>
      <input
        value={filters.q}
        onChange={(e) => setFilters({...filters, q: e.target.value})}
        placeholder="검색"
      />
      <label>
        <input
          type="checkbox"
          checked={filters.onlySale}
          onChange={(e) => setFilters({...filters, onlySale: e.target.checked})}
        />
        세일만
      </label>
    </div>
  );
}
```

---

## TOP 3. 데이터 패칭을 전부 클라로 내려서 SSR/캐시 이점을 다 날림

### 증상
- 초기 화면은 스피너만 보임(UX 저하)
- 중복 호출/레이스 컨디션이 늘어남
- SEO/공유 메타에서 빈 화면처럼 보일 수 있음

### 서버에서 패칭(샘플)
```tsx
// app/posts/page.tsx
export default async function PostsPage() {
  const posts = await fetch('https://api.example.com/posts', {
// 상황에 따라 선택:
// cache: 'force-cache',
// next: { revalidate: 60 },
    cache: 'no-store',
  }).then(r => r.json());

  return (
    <ul>
      {posts.map((p: any) => <li key={p.id}>{p.title}</li>)}
    </ul>
  );
}
```

---

## TOP 4. Provider를 Root에 박아서 전부 Client로 “전염”시킴

### 증상
- layout까지 `use client` 필요 → 앱 전체가 클라화
- 번들/JS 실행 비용 증가

### 권장 패턴: Providers를 전용 파일로 분리(샘플)
```tsx
// app/providers.tsx
'use client';

import {ReactNode, useState} from 'react';
import {QueryClient, QueryClientProvider} from '@tanstack/react-query';

export default function Providers({children}: { children: ReactNode }) {
  const [client] = useState(() => new QueryClient());
  return <QueryClientProvider client={client}>{children}</QueryClientProvider>;
}
```

```tsx
// app/layout.tsx (Server)
import Providers from './providers';

export default function RootLayout({children}: { children: React.ReactNode }) {
  return (
    <html lang="ko">
      <body>
        <Providers>{children}</Providers>
      </body>
    </html>
  );
}
```

---

## TOP 5. Server Action을 “만능 API”처럼 쓰다가 UX/경계가 꼬임

### 현실적인 정리
- Server Actions는 특히 **폼 기반 뮤테이션(저장/수정)**에 적합
- 조회(Read)는 서버 패칭/캐시 설계와 조합하는 편이 안정적
- TanStack Query 같은 클라이언트 데이터 라이브러리로도 충분히 커버 가능

### 폼 업데이트(샘플)
```tsx
// app/settings/actions.ts
'use server';

export async function updateProfile(formData: FormData) {
  const nickname = String(formData.get('nickname') ?? '');
// await db.user.update(...)
  return {ok: true};
}
```

```tsx
// app/settings/page.tsx
import {updateProfile} from './actions';

export default function SettingsPage() {
  return (
    <form action={updateProfile}>
      <label>
        닉네임
        <input name="nickname"/>
      </label>
      <button type="submit">저장</button>
    </form>
  );
}
```

---

## TOP 6. `process.env`를 클라에서 써서 비밀값 노출/빌드 문제 발생

### 규칙
- 클라에 노출해도 되는 값만 `NEXT_PUBLIC_` 접두사
- 그 외는 서버에서만 사용(Server Component/Route Handler/Server Action)

### 좋은 예(샘플)
```ts
// 공개 가능
const publicKey = process.env.NEXT_PUBLIC_MAP_KEY;
```

```ts
// 서버에서만
export async function GET() {
  const secret = process.env.API_SECRET;
  return Response.json({ok: true});
}
```

---

## TOP 7. Hydration mismatch를 운빨로 치부하고 땜질만 함

### 대표 원인
- 서버 렌더와 클라 렌더 결과가 달라지는 값
    - `new Date()`, `Math.random()`, locale 포맷 등
- localStorage 기반 테마 같은 “클라 전용 상태”를 즉시 렌더

### 해결 패턴 1: 마운트 후에만 값 반영(샘플)
```tsx
'use client';
import {useEffect, useState} from 'react';

export default function Now() {
  const [now, setNow] = useState<string | null>(null);

  useEffect(() => {
    setNow(new Date().toISOString());
  }, []);

  return <span>{now ?? '...'}</span>;
}
```

### 해결 패턴 2: 서버에서 값 고정해서 내려주기(샘플)
```tsx
// Server Component
export default function Page() {
  const serverNow = new Date().toISOString();
  return <ClientNow serverNow={serverNow}/>;
}
```

```tsx
'use client';
export default function ClientNow({serverNow}: { serverNow: string }) {
  return <span>{serverNow}</span>;
}
```

---

## 실무 팁: 코드리뷰에서 바로 쓰는 체크 질문

- 이 컴포넌트는 정말 Client여야 하나? (이벤트/상태/브라우저 API가 있나?)
- 데이터 패칭을 클라로 내린 이유가 있는가? (SEO/TTFB/중복 호출 고려했나?)
- Provider 범위는 최소인가? (Root까지 전염시키지 않았나?)
- env는 `NEXT_PUBLIC_`만 클라로 나갔나?
- Hydration mismatch 가능성이 있는 값(Date/Random/Locale)은 어디서 계산했나?
- “수정”은 Server Action, “조회”는 서버 패칭으로 분리했나?
