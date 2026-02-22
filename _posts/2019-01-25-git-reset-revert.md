---
layout: post
title: '[Git] Reset과 Revert 차이와 사용법'
author: chanhee.kim
date: 2019-01-25 10:27
updated_at: 2026-02-16
tags: [git]
image: /files/covers/cover-dev-09.jpg
description: "Git reset과 revert의 차이점과 각각의 사용법을 정리합니다."
keywords: "Reset과 Revert 차이와 사용법, git, 개발 블로그"
---

#### git - 작업 되돌리기
git 에서 작업한 것을 되돌리는 여러가지 방법이 있습니다. 낮은 수준의 일(개별 파일이나 묶음을 스테이징 하는것) 과 높은 수준의 일(실제 변경이 복구되는 방법) 이 있는데 이 중 높은 수준의 일을 알아 보겠습니다.
<br>
git 에서 변경한 내용을 되돌리는 방법은 2가지가 있습니다.<br>
하나는 git reset 또 다른 하나는 git revert 입니다.

#### git - reset 예제
git reset은 브랜치를 예전의 커밋을 가리키도록 이동하는 방식으로 변경 내용을 되돌립니다. 즉 "히스토리를 고쳐쓴다" 라고 말할 수 있습니다.<br>
<img src="{{ site.baseurl }}/assets/images/git/commit2.PNG"><br>
``` linux
$ git reset HEAD~1
```
<img src="{{ site.baseurl }}/assets/images/git/reset1.JPG"><br>
이러면 master 브랜치가 C1으로 옮겨 졌습니다. 이러면 로컬 저장소에는 마치 C2 커밋이 아예 없었던 것과 마찬가지 상태가 됩니다.

#### git - revert 예제
로컬 브랜치의 경우 reset을 쓸 수 있습니다만 다른사람이 작업하는 리모트 브랜치에는 쓸 수 없습니다.
이 되돌린 내용을 다른 사람들과 공유하기 위해서는, git revert를 써야합니다.<br>
<img src="{{ site.baseurl }}/assets/images/git/commit2.PNG"><br>
``` linux
$ git revert HEAD
```
<img src="{{ site.baseurl }}/assets/images/git/reset2.JPG"><br>
이상하게도 현재 커밋 아래에 새로운 커밋이 생겼습니다. C2'라는 새로운 커밋에 변경 내용이 기록 됩니다.<br>
revert를 하면 다른 사람에게도 변경내역을 push 보낼 수 있습니다.
<br>
참조사이트
<a href="https://learngitbranching.js.org/">https://learngitbranching.js.org/</a>
