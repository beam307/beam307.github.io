---
layout: post
title: '[JavaScript] 구글차트 활용'
author: chanhee.kim
date: 2017-12-11 11:03
tags: [javascript, project]
image: /files/covers/blog.jpg
---

##### 1. 구글차트 활용

구글차트 API 사이트 : https://developers.google.com/chart/ <br>

기본적인 차트 그리기
``` javascript
<script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script>
   <script type="text/javascript">
    google.charts.load('current', {'packages':['line']});
    google.charts.setOnLoadCallback(drawChart);

    function drawChart() {

      var data = new google.visualization.DataTable();
      data.addColumn('number', 'Day');
      data.addColumn('number', 'Guardians of the Galaxy');
      data.addColumn('number', 'The Avengers');
      data.addColumn('number', 'Transformers: Age of Extinction');

      data.addRows([
        [1,  37.8, 80.8, 41.8],
        [2,  30.9, 69.5, 32.4],
        [3,  25.4,   57, 25.7],
        [4,  11.7, 18.8, 10.5],
        [5,  11.9, 17.6, 10.4],
        [6,   8.8, 13.6,  7.7],
        [7,   7.6, 12.3,  9.6],
        [8,  12.3, 29.2, 10.6],
        [9,  16.9, 42.9, 14.8],
        [10, 12.8, 30.9, 11.6],
        [11,  5.3,  7.9,  4.7],
        [12,  6.6,  8.4,  5.2],
        [13,  4.8,  6.3,  3.6],
        [14,  4.2,  6.2,  3.4]
      ]);

      var options = {
        chart: {
          title: 'Box Office Earnings in First Two Weeks of Opening',
          subtitle: 'in millions of dollars (USD)'
        },
        width: 900,
        height: 500
      };

      var chart = new google.charts.Line(document.getElementById('linechart_material'));

      chart.draw(data, google.charts.Line.convertOptions(options));
     }
   </script>
```

각 Column에 데이터를 넣을수 있으며, 첫번째 Column은 그룹 레이블이며 String(이산그래프), number,date, datetime or timeofday(연속그래프) 타입을 넣을수 있다. 두번째 칼럼부터 실제 데이터를 넣는다. 옵션같은 경우는 구글차트 API 를 보고 적절하게 사용하며 된다.

#### 2. 프로젝트 적용

이번에는 1주일동안 방문자수, 가입자수를 통계내는 그래프를 그리도록 하겠다.

``` javascript
  var today = new Date(); // 오늘 날짜
	var before = new Array(); // 이전 날짜 배열

	for(var i = 1 ; i < 7 ; i ++) { // 1주일전까지 날짜 구하기
		before[i] = new Date(today.getTime() - (i * 24 * 60 * 60 * 1000));
    // ex) before[1] = 1일전 , before[2] = 2일전
		before[i] = before[i].getDate() + '일';
	}
	today = today.getDate() + '일'; // 오늘 날짜에서 '일'만

	var userVisitCnt = new Array(7); // 방문자수 배열
	$.ajax({ // 배열에 방문자수 넣기
		type : 'post',
		url : '/admin/weekUserVisit',
		async: false,
		success : function(data) {
			for(var i in data) {
				userVisitCnt[i] = data[i];
			}
		}
	})

	var userRegCnt = new Array(7); // 회원가입수 배열

	$.ajax({ // 배열에 가입자수 넣기
		type : 'post',
		url : '/admin/weekUserReg',
		async: false,
		success : function(data) {
			for(var i in data) {
				userRegCnt[i] = data[i];
			}
		}
	})

	var max1 = 0; // 방문자수 최대
	var max2 = 0; // 가입자수 최대
	var weekUserVisit = 0; // 최신 1주일간 방문자 수
	for(var i = 0; i < 7; i++) { // 1주일간 방문자수 더하기
		weekUserVisit += userVisitCnt[i];
		if(max1 < userVisitCnt[i])
			max1 = userVisitCnt[i];
	}

	var weekUserReg = 0; // 최신 1주일간 방문자 수
	for(var i = 0; i < 7; i++) { // 1주일간 가입자수 더하기
		weekUserReg += userRegCnt[i];
		if(max2 < userRegCnt[i])
			max2 = userRegCnt[i];
	}
```

위에는 1주일간 데이터를 가져오는것이며, 아래는 실제 그래프 그리기이다.

``` javascript
  google.charts.load('current', {'packages': ['line']});
  google.charts.setOnLoadCallback(drawChart1);
  function drawChart1() {

	var data = new google.visualization.DataTable();
	data.addColumn('string', 'Day');
	data.addColumn('number', '방문자 수');
	data.addColumn('number', '회원가입 수');

	data.addRows([
		[before[6], userVisitCnt[6], userRegCnt[6]],
		[before[5], userVisitCnt[5], userRegCnt[5]],
		[before[4], userVisitCnt[4], userRegCnt[4]],
		[before[3], userVisitCnt[3], userRegCnt[3]],
		[before[2], userVisitCnt[2], userRegCnt[2]],
		[before[1], userVisitCnt[1], userRegCnt[1]],
		[today, userVisitCnt[0], userRegCnt[0]]
	]);

	var options = {
  		legend : {position: 'bottom'}, // 범례 위, 아래 지원 X
		chart: {
			title: '사용자 통계',
		subtitle: '일일 방문자 및 회원가입 수'
		},
		series: {
	        0: {targetAxisIndex: 0},
	        1: {targetAxisIndex: 1}
      	},
      	vAxes: {
			0: {viewWindow : {max : max1 + 4}},
			1: {viewWindow : {max : max2 + 4}}
		},
		vAxis:{
			gridlines: {count: 0},

		},
		hAxis: {
			gridlines: {count: 2}
		},
		colors: ['#620017','#0066ff'],
	};

	var chart = new google.charts.Line(document.getElementById('chart1'));

	chart.draw(data, google.charts.Line.convertOptions(options));

```

첫번째칼럼에 날짜임에 불구 하구 String 을 넣는 이유는 연속그래프의 경우 1일 2일 3일 정확히 떨어지는게 아니라 시간까지 계산을 해버리기 때문에 각각 날짜에 따른 방문자수는 맞는지않는다. 그래서 직접 1일전 2일전, 3일전을 계산해서 넣었다.<br>

serise는 차트선을 2개 그릴때 쓰는것이며, 0 : 방문자 그래프, 1 : 가입자수 그래프이다.
또한 가이드라인이 지저분하게 그려지기 때문에 0으로 아예 지웠으며, viewWindow 경우 4미만의 값들은 소숫점이 나오기때문에 0의 값이여도 최대값 4를 주기 위해서 넣었다.
