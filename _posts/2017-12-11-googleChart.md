---
layout: post
title: 'Google Chart JavaScript: 구글 차트로 반응형 대시보드 라인 차트 만들기 (튜토리얼)'
author: chanhee.kim
date: 2017-12-11 11:03
updated_at: 2026-02-17
tags: [google-chart-javascript, javascript-charts, google-visualization, data-visualization, dashboard, chart, analytics, frontend]
image: /files/covers/blog.jpg
description: "Google Chart JavaScript 라이브러리를 사용하여 반응형 라인 차트를 구현하는 완벽 가이드입니다. 최근 7일 방문자/가입자 데이터를 시각화하며, DataTable 구성, 듀얼 Y축, 커스텀 툴팁, fetch 데이터 바인딩 등 실무 예제를 다룹니다."
---

## Google Chart JavaScript란?

**Google Chart JavaScript**(구글 차트)는 데이터 시각화를 위한 강력하고 무료인 웹 기반 라이브러리입니다. 별도의 복잡한 설치 없이 브라우저에서 `loader.js` 스크립트를 로드하는 것만으로 즉시 사용할 수 있어 진입 장벽이 낮습니다.

이 글에서는 **Google Chart JavaScript**를 활용해 실제 서비스 대시보드에서 자주 쓰이는 "최근 7일 방문자 및 가입자 추이" 라인 차트를 구현해봅니다. 레거시 방식은 배제하고, 모던 자바스크립트(ES6+, fetch, async/await)와 반응형 대응까지 고려한 실무 패턴을 소개합니다.

---

## 차트 종류 선택: Classic LineChart vs Material Line

Google Charts의 라인 차트는 크게 두 방식이 있습니다.

- **Classic LineChart**: `packages: ['corechart']` + `google.visualization.LineChart`
- **Material Line**: `packages: ['line']` + `google.charts.Line`

이 글에서는 **Material Line(`google.charts.Line`)** 기준으로 구현합니다. Material 디자인이 적용되어 대시보드에서 더 깔끔하고 가독성이 좋기 때문에 모던 웹 개발에서 추천하는 방식입니다.

---

## Google Chart JavaScript 핵심 가이드

구글 차트를 능숙하게 다루기 위해 꼭 알아야 할 4가지 포인트입니다.

### 1. loader.js는 페이지당 한 번만
`loader.js` 스크립트는 전역 `google` 객체를 생성합니다. SPA(Single Page Application)나 템플릿 환경에서 중복으로 로드되지 않도록 주의해야 합니다.

### 2. 패키지 로드 (`google.charts.load`)
사용하려는 차트 타입에 맞는 패키지를 로드해야 합니다.
- `google.charts.load("current", { packages: ["line"] });`

### 3. 데이터 구조: `DataTable`
차트에 주입할 데이터는 반드시 `DataTable` 객체 형태여야 합니다(또는 `arrayToDataTable` 헬퍼 사용).
`addColumn(type, label)`으로 스키마를 정의하고 `addRows(rows)`로 데이터를 넣는 흐름이 정석입니다.

### 4. 날짜 축(Axis)은 `string`이 아닌 `date` 타입으로
초보자가 가장 많이 하는 실수가 날짜를 문자열("2024-01-01")로 넣는 것입니다. 이렇게 하면 차트가 날짜를 인식하지 못해 간격이 균등하지 않거나 정렬이 꼬입니다.
반드시 **자바스크립트 `Date` 객체**로 데이터를 넣고, 표시는 `format` 옵션으로 제어하세요.

---

## 실전 예제: 7일 방문자/가입자 라인 차트 만들기

실무에서 가장 많이 요구되는 시나리오입니다. 백엔드에서 JSON 데이터를 `fetch`로 받아와, 반응형(Responsive) 라인 차트를 그리는 전체 코드를 작성해 보겠습니다.

### 데이터 시나리오
서버에서 다음과 같은 JSON 데이터를 반환한다고 가정합니다.

- `GET /admin/weekUserVisit` → `[12, 20, 18, 9, 15, 22, 30]` (최근 7일 방문자 수)
- `GET /admin/weekUserReg` → `[1, 4, 3, 2, 1, 6, 5]` (최근 7일 가입자 수)

> **Tip:** 두 API의 데이터 순서(날짜 정렬)가 동일해야 매핑하기 쉽습니다.


## Google Chart JavaScript 전체 코드: fetch + 듀얼 축 + 반응형

### HTML

```html
<!-- 1) loader.js는 한 번만 -->
<script src="https://www.gstatic.com/charts/loader.js"></script>

<div id="user-stats-chart" style="width: 100%; max-width: 960px; height: 420px;"></div>
```

### JavaScript
```js
// Material Line 패키지 로드
google.charts.load("current", { packages: ["line"], language: "ko" });
google.charts.setOnLoadCallback(init);

async function init() {
    const [visit, reg] = await Promise.all([
        fetch("/admin/weekUserVisit").then((r) => r.json()),
        fetch("/admin/weekUserReg").then((r) => r.json()),
    ]);

    // 최근 7일(오늘 포함) 날짜 배열: 과거 → 오늘 순
    const days = buildLast7Days();

    // DataTable 구성
    const data = new google.visualization.DataTable();
    data.addColumn("date", "날짜");
    data.addColumn("number", "방문자 수");
    data.addColumn("number", "회원가입 수");

    const rows = days.map((d, i) => [
        d,
        Number(visit?.[i] ?? 0),
        Number(reg?.[i] ?? 0),
    ]);
    data.addRows(rows);

    // 축 범위 계산 (여유값 포함)
    const maxVisit = Math.max(...rows.map((r) => r[1]));
    const maxReg = Math.max(...rows.map((r) => r[2]));

    const options = {
        chart: {
            title: "사용자 통계",
            subtitle: "최근 7일 방문자 및 회원가입 수",
        },

        legend: { position: "bottom" },

        // 듀얼 축: 방문(0)과 가입(1)을 각각 다른 Y축에 매핑
        series: {
            0: { targetAxisIndex: 0 },
            1: { targetAxisIndex: 1 },
        },
        vAxes: {
            0: { viewWindow: { min: 0, max: niceMax(maxVisit) } },
            1: { viewWindow: { min: 0, max: niceMax(maxReg) } },
        },

        // x축 날짜 포맷 (데이터 타입은 date 유지)
        hAxis: {
            format: "M/d",
            gridlines: { count: 7 },
        },

        // 여백(대시보드에서 답답함 방지)
        chartArea: { left: 56, right: 56, top: 60, bottom: 72 },

        // 디자인 시스템이 있으면 색상 통일 (선택)
        // colors: ["#620017", "#0066ff"],
    };

    const chartEl = document.getElementById("user-stats-chart");
    const chart = new google.charts.Line(chartEl);

    const draw = () => chart.draw(data, google.charts.Line.convertOptions(options));
    draw();

    // 반응형 리사이즈
    window.addEventListener("resize", debounce(draw, 150));
}

function buildLast7Days() {
    const result = [];
    const now = new Date();

    // 날짜 경계 이슈를 줄이기 위해 "정오"로 고정하는 패턴을 자주 씁니다.
    for (let i = 6; i >= 0; i--) {
        const d = new Date(now);
        d.setDate(now.getDate() - i);
        d.setHours(12, 0, 0, 0);
        result.push(d);
    }
    return result;
}

function niceMax(v) {
    // 작은 값에서 소수점/시각적 답답함 방지
    if (!Number.isFinite(v) || v <= 0) return 4;
    if (v < 4) return 4;
    return Math.ceil(v * 1.1);
}

function debounce(fn, delay) {
    let t = null;
    return (...args) => {
        clearTimeout(t);
        t = setTimeout(() => fn(...args), delay);
    };
}
```


## Google Chart JavaScript 성능 최적화 및 주의사항

실전 프로젝트에서 반응형 대시보드를 구축할 때 자주 겪는 이슈와 해결책입니다.

1.  **loader.js 중복 로드 방지**: SPA(React, Vue 등) 환경에서는 `loader.js`가 여러 번 로드되지 않도록 전역 상태를 체크하세요.
2.  **비동기 데이터 처리(Promise.all)**: `async: false` 옵션은 브라우저를 멈추게 합니다. 반드시 `async/await`와 `Promise.all`로 다중 API를 병렬 처리하세요.
3.  **날짜 형식(Date Check)**: 축 데이터는 `string`이 아닌 `Date` 객체여야 합니다. 텍스트로 넣으면 정렬이 깨지거나 불연속적인 그래프가 그려집니다.
4.  **타임존 이슈(UTC vs KST)**: 날짜 경계선 문제(하루 밀리는 현상)를 방지하려면 시간을 **정오(12:00)**로 설정하는 것이 안전합니다.
5.  **반응형 리사이즈(Debounce)**: 브라우저 크기 변경 시 `draw()`를 다시 호출해야 합니다. `debounce`를 적용해 불필요한 연산을 줄이세요.
6.  **축 범위(viewWindow) 커스텀**: 데이터 최대값에 10~20% 여유를 주면 그래프가 답답해 보이지 않습니다 (`niceMax` 함수 참고).
