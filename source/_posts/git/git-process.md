---
title: git-process
date: 2017-11-03 17:02:17
tags:
- Git
categories:
- 기타
- Git
---
## Git Pull Request 프로세스

![git](/images/git/git.png)

원격 remote에 있는 repository를 받아서 프로젝트를 진행하는 경우

```
KYUNAMui-MBP:java-framework kyunam$ git remote -v
origin	https://github.com/xmfpes/java-framework.git (fetch)
origin	https://github.com/xmfpes/java-framework.git (push)
upstream	https://github.com/code-squad/java-framework.git (fetch)
upstream	https://github.com/code-squad/java-framework.git (push)

KYUNAMui-MBP:java-framework kyunam$ git branch -a
master
* xmfpes
remotes/origin/master
remotes/origin/xmfpes
remotes/upstream/master
remotes/upstream/xmfpes
```
fork 이후 위와 같이 branch들이 있다고 가정하자.


## branch로 checkout 한 후, 그 코드에서 새 branch를 생성하자.

코드를 추가할 branch로 이동 한 뒤, 새 branch를 생성해 거기서 작업을 진행하자.

```
git checkout -b new-branch
```

origin/new-branch상에서 커밋과 작업을 완료 한 뒤 PR을 보내야 한다.

## upstream/xmfpes로 Pull request 보내기

Pull Request를 보내고, Merge된 이후에 다시 origin/xmfpes branch로 이동하자.

origin/xmfpes에는 현재 최신화되지 않은 코드를 가지고 있는데 fetch를 통해 연결된 upstream remote를 최신화 하고 upstream/xmfpes에 merge된 코드를 rebase / merge를 통해 origin/xmfpes로 가져와야 한다. 

```
git fetch upstream

git rebase upstream/xmfpes
```

이후에는 처음으로 돌아가 같은 작업을 반복하면서 진행하면 된다.


- 로컬의 작업을 무시하고, github의 내용을 가져와야 하는 경우
```
git reset --hard upstream/xmfpes
```

- github의 작업 내용들을 없애고, local의 내용으로 덮어씌우려는 경우
```
git push --force origin xmfpes
```

## A,B,C 여러명이 동시에 작업을 하는 경우

A가 먼저 작업 완료 후 PR을 보내 master에 merge된 경우라면
B나 C는 작업이 완료되고 바로 PR을 보내지 말고, master의 코드를 merge나 rebase를 통해 받아와서 conflict 문제를 해결하고 PR을 보내야 한다.

## PR를 Merge할때 커밋을 합치려면?

squash 옵션을 이용해서 여러 커밋들 하나로 합쳐서 merge할 수 있다.