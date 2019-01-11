---
layout: post
title: '[git] git - rebase 로 병합하기'
author: chanhee.kim
date: 2019-01-09 15:35
tags: [git]
image: /files/covers/blog.jpg
---

#### git - rebase
브랜치끼리 작업을 접목하는 1번째는 merge, 2번째는 rebase입니다. 리베이스는 커밋들을 모아서 복사한뒤 다른곳에 떨궈 놓는 것입니다.<br>
리베이스(rebase)를 하면 커밋들의 흐름을 한줄로 할 수 있는 장점이 있습니다. 저장소의 커밋로그와 이력이 깨끗해지죠.

<img src="{{ site.baseurl }}/assets/images/git/rebase1.JPG"><br>
현재 bugFix가 선택되어 있습니다. 이 브랜치의 작업을 master브랜치 위로 옮기려고합니다. 그러면 실제로는 master와 bugFix가 따로 개발 했지만 순서대로 개발한것 처럼 보입니다.

```linux
$ git rebase master
```

<img src="{{ site.baseurl }}/assets/images/git/rebase2.JPG"><br>
위와 같이 한줄로 되었습니다. C3커밋은 어딘가에 있고 C3'을 master위에 올려놨습니다.<br>
하지만 master가 그대로인데요. 이동해보겠습니다.

``` linux
$ git checkout master
$ git rebase bugFix
```

<img src="{{ site.baseurl }}/assets/images/git/rebase3.JPG"><br>
master 브랜치가 bugFix의 부모에 있었기 때문에 단순히 master 브랜치를 더 앞쪽의 커밋을 가리키게 이동하는것이 전부입니다.

#### git merge vs rebase
- git-merge : Join two or more development histories together.
- git-rebase : Forward-port local commits to the updated upstream head.
git 매뉴얼에는 위와 같이 정의가 되어있고 


<br>
참조사이트
<a href="https://learngitbranching.js.org/">https://learngitbranching.js.org/</a>
