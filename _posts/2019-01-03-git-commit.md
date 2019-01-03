---
layout: post
title: '[git] git - commit에 대해서'
author: chanhee.kim
date: 2019-01-03 16:26
tags: [git]
image: /files/covers/blog.jpg
---

#### git - commit

커밋은 git저장소에 파일에 대한 스냅샷을 기록 하는 것입니다.  폴더 전체를 복사하여 붙여넣는것과 유사하지만
훨씬 유용합니다.
<br>
git은 커밋을 가볍게 유지하고자 하기때문에 폴더전체를 복사하지 않습니다. 이전버전과 다음버전의 변경내역을 저장합니다.
<br>
한마디로 커밋은 프로젝트의 스냅샷들로 생각하면됩니다.

#### git - commit 예제

<img src="{{ site.baseurl }}/assets/images/git/commit1.PNG"><br>
최초 1회 커밋한 상태입니다. 여기서 한번더 커밋을 하면...
``` linux
$ git commit
```
<img src="{{ site.baseurl }}/assets/images/git/commit2.PNG"><br>
여기서 한번더 커밋을 하면..
<img src="{{ site.baseurl }}/assets/images/git/commit3.PNG"><br>

이렇게 커밋은 브랜치(master)가 현재 커밋된 저장소(C3)을 가르키고 어떤기반으로 변경된것인지 이전(부모)커밋(C2 or C1)된 저장소를 가르킵니다.
