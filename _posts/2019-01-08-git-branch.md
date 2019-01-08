---
layout: post
title: '[git] git - branch'
author: chanhee.kim
date: 2019-01-08 10:20
tags: [git]
image: /files/covers/blog.jpg
---

#### git - branch
깃의 브랜치는 가볍습니다. 특정 커밋에 대한 참조(reference)만 하기때문이죠.
<br>
브랜치를 많이 만들어도 메모리나 디스크공간에 부담이 되지 않기 때문에, 작업을 커다란 브랜치로 만드는것보다 작은 단위로 잘게 나누는 것이 좋습니다.
<br>
브랜치 : "하나의 커밋과 그 부모 커밋들을 포함하는 작업 내역"


#### git - branch 예제

``` linux
$ git branch newImage
```
<img src="{{ site.baseurl }}/assets/images/git/branch1.PNG">
->
<img src="{{ site.baseurl }}/assets/images/git/branch2.PNG">

이렇게 현재 브랜치(master)에서 새로운 브랜치가 새로생기며 커밋C1를 가르키게 됩니다.
<br>
참조사이트
<a href="https://learngitbranching.js.org/">https://learngitbranching.js.org/</a>
