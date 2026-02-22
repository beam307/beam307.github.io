---
layout: post
title: '[Node.js & JavaScript] 모듈 시스템 총정리 2026 (CommonJS, ESM, UMD)'
author: chanhee.kim
date: 2026-01-30 18:38
tags: [javascript, nodejs, frontend]
image: /files/covers/blog.jpg
description: "최신 자바스크립트 모듈 시스템의 표준인 ES Modules(ESM)와 CommonJS, UMD의 특징과 사용법, 차이점을 완벽하게 정리했습니다."
keywords: "모듈 시스템 총정리 2026 (CommonJS, ESM, UMD), 자바스크립트, node.js, 프론트엔드, 개발 블로그"
---

자바스크립트 모듈 시스템의 진화는 언어의 성장 과정을 보여줍니다. 
초기의 무질서한 스크립트 로딩부터 시작하여 CommonJS, 그리고 현재의 ES Modules까지. 
2026년 기준으로 각 모듈 시스템을 정리해보겠습니다.

## 1. 모듈 시스템의 필요성

초기 자바스크립트는 `<script>` 태그로 로드되었고, 
모든 변수가 **전역 스코프(Global Scope)**를 공유했습니다. 이로 인해:
- 변수명 충돌 위험
- 의존성 관리 어려움
- 라이브러리 간 간섭

이러한 문제를 해결하기 위해 
**파일 단위 스코프 격리**와 **선택적 기능 로드**가 필요했고, 
다양한 모듈 시스템이 등장했습니다.

---

## 2. 주요 모듈 시스템의 역사

### CommonJS (CJS) - Node.js 표준
- **등장**: 2009년
- **환경**: Node.js (서버 사이드)
- **특징**: 동기적 로딩, 런타임에서 모듈 로드
- **문법**: `require()`, `module.exports`, `exports`

### AMD (Asynchronous Module Definition)
- **환경**: 브라우저
- **특징**: 비동기 로딩 (RequireJS)
- **현황**: 2026년 기준 거의 사용되지 않음

### UMD (Universal Module Definition)
- **환경**: Node.js + 브라우저
- **특징**: CJS와 AMD를 모두 지원하는 디자인 패턴
- **주용도**: 라이브러리 배포

### ES Modules (ESM) - *현재 표준*
- **등장**: ES6 (2015)
- **환경**: 모던 브라우저 + Node.js 12+
- **특징**: 정적 분석, 트리 쉐이킹 지원, 비동기 로딩
- **현황**: 2026년 현재 모든 최신 프로젝트의 표준

---

## 3. CommonJS (CJS) - 레거시 시스템

### CJS 기본 문법

#### Named Exports
```javascript
// math.js
const add = (a, b) => a + b;
const subtract = (a, b) => a - b;

// exports 객체에 속성 추가
exports.add = add;
exports.subtract = subtract;

// 또는 module.exports 사용
module.exports = {
    add,
    subtract
};
```

#### Import (Require)
```javascript
// main.js
const math = require('./math');
console.log(math.add(1, 2)); // 3

// 구조 분해
const { add, subtract } = require('./math');
```

#### Default Export
```javascript
// calculator.js
module.exports = function calculate(a, b) {
    return a + b;
};

// main.js
const calculate = require('./calculator');
console.log(calculate(1, 2)); // 3
```

### CJS의 특징
- ✅ Node.js 기본 지원
- ✅ 동기적 로딩 (서버에 최적화)
- ❌ 동적 분석 불가 (트리 쉐이킹 불가)
- ❌ 브라우저 네이티브 미지원

---

## 4. ES Modules (ESM) - *현재 표준*

2026년 기준으로 모든 최신 프로젝트에서 사용하는 표준 모듈 시스템입니다.

### Node.js에서 ESM 활성화

#### 방법 1: package.json에서 설정
```json
{
  "type": "module",
  "name": "my-project",
  "version": "1.0.0"
}
```

#### 방법 2: .mjs 확장자 사용
```javascript
// math.mjs
export const add = (a, b) => a + b;
```

### ESM 문법

#### Named Exports
```javascript
// math.js
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;
export const multiply = (a, b) => a * b;
```

#### Default Export
```javascript
// calculator.js
export default function calculate(a, b) {
    return a + b;
}
```

#### Mixed Exports (Default + Named)
```javascript
// utils.js
export default function defaultFunc() {
    return 'default';
}

export const namedFunc = () => 'named';
export const CONSTANT = 42;
```

#### Import 문법
```javascript
// main.js

// Default import
import calculate from './calculator.js';

// Named imports
import { add, subtract } from './math.js';

// Mixed imports
import defaultFunc, { namedFunc, CONSTANT } from './utils.js';

// Alias (별칭)
import { add as addition } from './math.js';

// 전체 import (네임스페이스)
import * as math from './math.js';
console.log(math.add(1, 2)); // 3

// Dynamic import (런타임에 모듈 로드)
const module = await import('./math.js');
console.log(module.add(5, 3)); // 8
```

### ESM의 핵심 특징

#### 1. Static Analysis (정적 분석)
```javascript
// ESM은 빌드 시간에 의존성을 분석할 수 있음
// 따라서 트리 쉐이킹이 가능
import { add } from './math.js'; // 사용하는 것만 번들에 포함
```

#### 2. Live Bindings
```javascript
// counter.js
export let count = 0;
export function increment() {
    count++;
}

// main.js
import { count, increment } from './counter.js';
console.log(count); // 0
increment();
console.log(count); // 1 (변경사항이 실시간 반영됨)
```

#### 3. 비동기 로딩
```javascript
// 조건부 모듈 로드
if (process.env.NODE_ENV === 'development') {
    const debugModule = await import('./debug.js');
}
```

---

## 5. UMD (Universal Module Definition)

여러 환경을 지원해야 하는 라이브러리를 만들 때 사용합니다.

```javascript
// myLibrary.js
(function(global, factory) {
    // Node.js/CommonJS
    if (typeof module === 'object' && module.exports) {
        module.exports = factory();
    }
    // AMD
    else if (typeof define === 'function' && define.amd) {
        define(factory);
    }
    // 브라우저 전역
    else {
        global.MyLibrary = factory();
    }
})(typeof self !== 'undefined' ? self : this, function() {
    return {
        add: (a, b) => a + b,
        subtract: (a, b) => a - b
    };
});
```

### UMD 사용 예
```javascript
// Node.js
const myLib = require('./myLibrary.js');

// 브라우저
<script src="myLibrary.js"></script>
<script>
    const result = MyLibrary.add(1, 2);
</script>

// AMD/RequireJS
require(['myLibrary'], function(myLib) {
    console.log(myLib.add(1, 2));
});
```

**현황**: 2026년 기준 ESM으로의 전환 완료. 레거시 지원이 필요한 경우에만 사용.

---

## 6. 비교 표

| 특성 | CommonJS | ESM | UMD |
| :--- | :---: | :---: | :---: |
| **로딩 방식** | 동기 | 비동기 | 하이브리드 |
| **분석** | 동적 | 정적 | 정적 |
| **트리 쉐이킹** | ❌ | ✅ | △ |
| **브라우저** | ❌ | ✅ | ✅ |
| **Node.js** | ✅ (기본) | ✅ (12+) | ✅ |
| **Live Binding** | ❌ | ✅ | ❌ |
| **사용난이도** | 쉬움 | 쉬움 | 어려움 |

---

## 7. 2026년 권장사항

### ✅ 새 프로젝트는 ESM 사용
- React, Vue, Angular 모두 ESM 기본
- Vite, Webpack 모두 ESM 중심
- Node.js 18+ 모든 프로젝트에서 ESM 권장

```javascript
// package.json
{
  "type": "module",
  "engines": {
    "node": ">=18.0.0"
  }
}
```

### ✅ CommonJS가 필요한 경우
- 레거시 Node.js 프로젝트 유지보수
- ESM 완벽 지원 전 구형 환경

```javascript
// ESM 프로젝트에서 CJS 모듈 사용
import { createRequire } from 'module';
const require = createRequire(import.meta.url);
const legacy = require('./legacy.cjs');
```

### ✅ 라이브러리 배포 시 고려사항
- **Dual Package**: ESM + CJS 동시 지원
- **package.json exports 필드 활용**

```json
{
  "exports": {
    ".": {
      "import": "./dist/index.mjs",
      "require": "./dist/index.cjs"
    }
  }
}
```

---

## 결론

2026년의 자바스크립트 모듈 생태계는 **ESM으로 완전히 통합**됨 
CommonJS는 역사적 중요성이 있지만, 새로운 프로젝트에서는 ES Modules를 표준으로 사용해야 함
레거시 시스템 유지보수가 필요한 경우에만 CommonJS를 고려.
