---
layout: post
title: '[JavaScript] 구글차트(Google Charts)로 대시보드 라인 차트 구현하기 (7일 방문·가입자)'
author: chanhee.kim
date: 2017-12-11 11:03
updated_at: 2026-02-16
tags: [javascript, google-charts, data-visualization, dashboard, chart, analytics, frontend]
image: /files/covers/blog.jpg
description: "Google Charts로 7일 방문자/가입자 라인 차트를 그리는 방법을 정리합니다. DataTable 구성, date 축 포맷, 듀얼 축, 반응형 리사이즈, fetch 기반 데이터 바인딩까지 실무 패턴 중심으로 다룹니다."
permalink: /2017/12/11/googleChart/
---

## Google Charts(구글 차트)란?

Google Charts는 **브라우저에서 바로 쓸 수 있는 자바스크립트 시각화 라이브러리**입니다. 별도 빌드 없이 `loader.js`를 불러온 뒤 패키지를 로드하고, `DataTable`에 데이터를 채워 `draw()`로 렌더링합니다. :contentReference[oaicite:0]{index=0}

이 글에서는 “레거시한 방식(동기식 AJAX, 날짜를 억지로 문자열로 바꾸기 등)”은 빼고, **문서 기준으로 지금도 권장되는 방식** 위주로 정리합니다. :contentReference[oaicite:1]{index=1}

---

## 라인 차트 선택: Classic vs Material

Google Charts의 라인 차트는 크게 두 방식이 있습니다.

- **Classic LineChart**: `packages: ['corechart']` + `google.visualization.LineChart`
- **Material Line**: `packages: ['line']` + `google.charts.Line`

이 글에서는 **Material Line(`google.charts.Line`)** 기준으로 구현합니다. (대시보드에서 깔끔한 기본 스타일, 간단한 구성에 유리)

---

## 핵심 개념 (DataTable & 축 타입)

### DataTable 구성 흐름

1. `addColumn(type, label)`로 컬럼 정의
2. `addRows([...])`로 행 데이터 추가
3. `chart.draw(data, options)`로 렌더링

컬럼 타입은 `string`, `number`, `date`, `datetime`, `timeofday` 등을 사용합니다.  
**날짜 축을 쓰려면 첫 번째 컬럼을 `date` 타입으로 두는 게 정석**이고, 표시 형식은 옵션으로 컨트롤합니다.

---

## 프로젝트 적용: 최근 7일 방문자/회원가입 라인 차트

아래 예시는 서버에서 다음과 같이 응답한다고 가정합니다.

- `GET /admin/weekUserVisit` → `[12, 20, 18, 9, 15, 22, 30]`
- `GET /admin/weekUserReg` → `[1, 4, 3, 2, 1, 6, 5]`

> **권장:** 두 API는 반드시 “같은 날짜 순서(과거→오늘 또는 오늘→과거)”로 내려오게 맞추는 게 가장 좋습니다.


## 핵심 개념 4가지

### 1) loader.js는 한 번만 로드
페이지 어디에 두든 상관 없지만 **한 번만** 포함해야 합니다. :contentReference[oaicite:2]{index=2}

### 2) `google.charts.load()`로 패키지 로드
라인 차트는 크게 두 갈래가 있습니다.

- **Classic LineChart**: `packages: ['corechart']` + `google.visualization.LineChart`
- **Material Line (google.charts.Line)**: `packages: ['line']` + `google.charts.Line`

Material이 기본 스타일/가독성에서 개선점이 많습니다. :contentReference[oaicite:3]{index=3}  
(단, 프로젝트 요구에 따라 Classic이 옵션이 더 많은 경우도 있어요.)

### 3) 데이터는 `DataTable`(또는 `arrayToDataTable`)에 넣는다
`addColumn(type, label)` → `addRows(rows)` 순서가 기본입니다. :contentReference[oaicite:4]{index=4}

### 4) 날짜 축은 **문자열이 아니라 `date` 타입**이 정석
날짜/시간 타입은 JS `Date`로 안전하게 구성할 수 있고, 축/툴팁 표시 형식은 옵션으로 포맷팅합니다. :contentReference[oaicite:5]{index=5}

---

## 실전: 7일 방문자/가입자 라인 차트 (fetch + 듀얼 축 + 반응형)

아래 예시는 서버가 다음처럼 응답한다고 가정합니다.

- `GET /admin/weekUserVisit` → `[12, 20, 18, 9, 15, 22, 30]` (오늘 포함 7일, “오래된→최근” 또는 “최근→오래된” 중 하나로 고정)
- `GET /admin/weekUserReg` → `[1, 4, 3, 2, 1, 6, 5]`

> **중요:** 두 API는 “날짜 순서”를 동일하게 맞춰서 내려주는 게 가장 깔끔합니다.


## 구현 코드 (fetch + 듀얼 축 + 반응형)

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


### 실무 팁 (운영/대시보드에서 체감되는 것들)

- **loader.js는 페이지에서 딱 1번만** 로드하세요. (SPA/템플릿에서 중복 로드되면 로딩/콜백이 꼬이는 경우가 있어요)
- **동기식 AJAX(async:false)는 피하세요.** 대시보드 화면이 멈추고, 사용자 체감이 확 떨어집니다. Promise.all + fetch로 동시에 받아오면 깔끔합니다.
- **날짜는 문자열로 우회하지 말고 date로 유지**하세요. 대신 hAxis.format으로 표시만 바꾸면(예: M/d, MM월 dd일) 정렬/툴팁/축 동작이 안정적입니다.
- **타임존/날짜 경계 이슈가 걱정되면 시간을 “정오(12:00)”**로 고정하세요. (자정 주변에서 날짜가 밀리는 버그를 줄이는 데 효과적)
- **반응형은 “리사이즈 시 draw 재호출”이 사실상 필수**입니다. 컨테이너 폭이 바뀌는데 차트가 그대로면 깨져 보입니다. 디바운스로 부담도 줄이세요.
- **축 범위(viewWindow)는 자동에 맡기지 말고 조금 여유를 주세요.** 최대값에 10% 정도 padding을 주면 선이 상단에 딱 붙지 않아 훨씬 보기 좋습니다.
- **서버 응답의 날짜 순서를 API 레벨에서 통일**해두면 프론트가 훨씬 단순해집니다. “과거→오늘”로 고정해두는 걸 추천합니다(차트 rows 매핑이 깔끔).
