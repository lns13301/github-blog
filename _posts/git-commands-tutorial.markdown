---
layout: post
title: 깃 명령어 정리
date: 2021-02-10 21:15:00 +0900
description: Git commands
img: woowacourse.jpg
tags: Git Study
---

# 깃 명령어 정리 🖥
우아한 테크 코스 과정에서 사용할 주요한 깃 명령어를 기록하기 위한 포스트

## Git 기본

1. Git 커밋
	- 커밋 하기
		- $ git commit
			- 현재까지 작업중이던 저장소의 내용을 변경해서 하나의 커밋으로 저장   
	
	- 메시지와 함게 커밋하기
		- $ git commit -m "Commit Message"
		- $ git commit -m "Commit Title
			> - commit message 1
			> - commit message 2
			> - commit message 3"

2. Git 브랜치
	- 브랜치를 엄청 작은 단위로 많이 만들게 되면 메모리나 디스크 공간에 부담이 될까?
		- > No!! 작은 단위로 잘게 나누는 것이 좋다!
	- 쉽게 생각하면 "하나의 커밋과 그 부모 커밋들을 포함하는 작업 내역" 이라고 생각하면 된다.   
	
	- 브랜치 생성
		- $ git branch [Branch Name]
	- 브랜치를 생성하는 시점에서의 커밋을 가리킨다.   
	
	- 브랜치 이동 1
		- $ git checkout [Branch Name]
	- 브랜치 이동 2
		- $ git switch [Branch Name]   
		
	- 브랜치 생성 & 이동 1
		- $ git checkout -b [Branch Name]
	- 브랜치 생성 & 이동 2
		- $ git switch -c [Branch Name]   
		
	- 깃 버전 2.23 이후부터 checkout 을 switch 와 restore 로 기능을 분리했다.
		- 기존의 checkout 은 많은 기능들을 수행해서 사용자가 해당 명령어를 사용하는데 있어 모호할 수 있기 때문에 분리되었다고 한다.
			- 기존 기능 : 브랜치 생성, 이동, 복원 등..
		- 여전히 checkout 을 사용할 수 있지만, 되도록 switch 와 restore 를 사용해 보도록 하자!   
		
3. Git 합치기
	- 합치고 싶은 곳의 브랜치로 이동하기
		- $ git switch [Branch Name]
	- 합칠 대상이 될 브랜치를 현재 브랜치에 합치기
		- $ git merge [Target Branch Name]
	
4. Git 리베이스
	- 커밋들을 모아서 복사한 뒤 다른 곳에 떨궈 놓는 것이다.
	- 따로 작업한 브랜치를 현재 브랜치 위에 올려놓게 되면서 실제로는 두 기능을 따로 개발했지만, 마치 순서대로 개발한 것처럼 보이게 된다.   
	
	- 합치고 싶은 곳의 브랜치로 이동하기
		- $ git switch [Branch Name]
	- 합쳐질 곳의 대상이 될 브랜치를 현재 브랜치에 합치기
		- $ git rebase [Target Branch Name]
	- 대상 브랜치의 커밋을 합친 브랜치로 이동시키기
		- $ git switch [Target Branch Name]
		- $ git rebase [Branch Name]
	- [Target Branch Name] 가 [Branch Name] 의 부모쪽에 있었기 때문에, 단순히 그 브랜치를 더 앞쪽의 커밋을 가리키게 이동하는 것이 전부이다.(같은 위치에 있게 됨)   
	
	- git merge 에서는 합칠 대상을 현재 브랜치로 가져왔다면, git rebase 에서는 합쳐질 곳의 브랜치로 현재 브랜치가 합쳐진다.
	
