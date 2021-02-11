---
layout: post
title: 깃 명령어 정리
date: 2021-02-11 22:00:00 +0900
description: Git commands
img: git_icon.png
tags: Git Study
---

# 깃 명령어 정리 🖥
우아한 테크 코스 과정에서 사용할 주요한 깃 명령어를 기록하기 위한 포스트

## 로컬 : Git 기본    

1. Git 커밋
	- 커밋 하기
		- $ git commit
			- 현재까지 작업중이던 저장소의 내용을 변경해서 하나의 커밋으로 저장    
	
	- 메시지와 함게 커밋하기 방법 1
		- $ git commit -m "Commit Message"
	- 메세지와 함께 커밋하기 방법 2
		> $ git commit -m "Commit Title
		> commit message 1
		> commit message 2
		> commit message 3"     

2. Git 브랜치
	- 브랜치를 엄청 작은 단위로 많이 만들게 되면 메모리나 디스크 공간에 부담이 될까?
		- > No!! 작은 단위로 잘게 나누는 것이 좋다!
	- 쉽게 생각하면 "하나의 커밋과 그 부모 커밋들을 포함하는 작업 내역" 이라고 생각하면 된다.  
	- 커밋들 사이에 책갈피를 끼워 넣는 것이라고 본다.
		- 커밋 단위로 원하는 위치로 이동이 가능하다.
			- git switch [Commit Hashcode]
		- 브랜치는 특정 위치에서 시작해서 그 위치에서 이후에 작업한 내용의 가장 최신 위치의 커밋의 위치를 나타낸다.    
	
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
		
	- 브랜치 제거
		- $ git branch -D [Branch Name]
		
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
	
## 원격 : Git Push & Pull    

- 자신의 로컬 이외에서 접근할 때에 유용하다.
	- 백업의 역할로 매우 좋다.
- 다른 사람들과 함께 프로젝트를 진행할 수 있다.    

1. Git 클론
	- 원격 저장소의 프로젝트를 로컬로 복사해서 가져오기
		- $ git clone [가져올 저장소의 주소]    

2. Git 원격 브랜치
	- 원격 저장소의 상태를 반영한다.
	- 로컬에서의 작업과 공개적으로 되고있는 작업의 차이를 이해하는데 도움을 준다.   
	
	- 원격 저장소에서 체크아웃하기
		- $ git switch [원격 저장소 이름(default: origin)]/[브랜치 이름]   
	
	- 해당 작업을 한 후 커밋을 하면 원격 저장소는 분리된 HEAD 모드로 만든다.
		- 이 경우 커밋을 추가해도 해당 원격 저장소를 갱신하지 않는다.
		- 해당 원격 저장소가 갱신될때만 갱신된다.   

	- 원격 저장소 브랜치 제거 방법 1
		- $ git push origin --delete [브랜치 이름]
	- 원격 저장소 브랜치 제거 방법 2
		> $ git branch -d [브랜치 이름]
		> $ git push origin [브랜치 이름] 		
		
3. Git Fetch
	- 원격 저장소에서 데이터를 가져오는 방법
	- 원격 저장소에는 있지만 로컬에는 없는 커밋을 다운로드 받는다.
	- 로컬의 원격 브랜치가 가리키는곳을 업데이트 한다.
		- 로컬에서 나타내는 원격 저장소의 상태를 실제 원격 저장소의 상태(fetch 를 진행하는 시점)로 동기화 한다.
	- fetch 는 원격 저장소의 최신 내용을 받아오지만 로컬 저장소의 내용을 변경하는 것은 아무 것도 없다!!
		- 받아온 원격 저장소의 최신 내용을 실제 로컬 저장소에 적용 시키는 명령어는 따로 있다.
			- $ git cherry-pick [원격 저장소 이름]/[브랜치 이름]
			- $ git rebase [원격 저장소 이름]/[브랜치 이름]
			- $ git merge [원격 저장소 이름]/[브랜치 이름]    
	
	- 원격 저장소에서 내려받기
		- $ git fetch    
		
4. Git Pull
	- 원격 저장소로부터 fetch 후 로컬 저장소에 merge 하는 경우가 많다보니 한 번에 작업하기 위해 사용되는 명령어다.    
	
	- 원격 저장소에서 내려받기 & 합치기
		- $ git pull    

5. Git 가짜 팀워크
	- 원격 저장소 master 에 간단히 하나의 커밋하기
		- $ git fakeTeamwork    
	
	- 원격 저장소 master 에 특정 브랜치의 커밋을 여러개 하기
		- $ git fakeTeamwork [브랜치 이름] [커밋 횟수]    

6. Git Push
	- 로컬에서의 작업 내용을 원격 저장소에 공개하기
		- $ git push
			- 매개변수 없이 사용하면 push.default 라는 git 의 설정에 따라 결정된다.     
	- push 를 하고나면 자동적으로 로컬의 원격 저장소도 최신화 된다.     
	
7. Git 엇갈린 작업 (충돌)
	- 여러 사람과 같이 프로젝트를 진행중일때, 커밋 하고자 하는 작업 내역이 원격 저장소의 최신 커밋 상태에 뒤떨어질 때 push 를 하려고 하면 발생한다.
		- 이 때 push 를 하려고 하면 push 가 되지 않는다.    
		
	- 로컬의 작업을 원격 브랜치의 최신 상태를 기반이 되게끔 만들면 해결할 수 있다.
		- 리베이스 등을 이용하면 된다.
	- rebase: 작업중인 브랜치에서, 최신화 -> 리베이스 -> Push
		> $ git fetch
		> $ git rebase [원격 저장소 이름]/[브랜치 이름]
		> $ git push [원격 저장소 이름] [브랜치 이름]
	- 줄임 명령어
		> $ git pull --rebase     
		> $ git push     
		
	- merge: 작업중인 브랜치에서, 최신화 -> 합치기 -> Push
		> $ git fetch
		> $ git merge [원격 저장소 이름]/[브랜치 이름]
		> $ git push [원격 저장소 이름] [브랜치 이름]
	- 줄임 명령어
		> $ git pull
		> $ git push    
		
8. Git 원격 저장소 거부
	- 원격 저장소의 main 브랜치에는 직접적인 커밋대신 pull request 가 쓰여야 한드는 규칙이 있다.
		- 이 때, 실수로 브랜치에서 작업 하지않고, main 브랜치에서 직접 커밋을 하면 변경 사항을 push 하지 못한다.
			- 이것이 내가 프리코스 최종 시험에서 겪었던 나를 몹시 당황케한 일이다.     
		
		- 해결책
			- feature([브랜치 이름]) 라는 이름의 다른 브랜치를 생성하고 원격 저장소에 push 한다.
			- 원격 저장소와 동기화 될 수 있도록 로컬 저장소의 main 브랜치를 reset 한다.
				- 하지 않으면 다음에 pull 시도할 때 문제가 발생하거나 다른 협업자들의 커밋이 나의 커밋과 충돌할 수 있다.
			- 위의 과정 명령어(이미 작업 해버린 main 브랜치에서 시작)
				> git branch [브랜치 이름]
				> git reset main [원격 저장소 이름]/[브랜치 이름]
				> git switch [브랜치 이름]
				> git push [원격 저장소 이름] [브랜치 이름]     
