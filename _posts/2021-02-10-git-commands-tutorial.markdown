---
layout: post
title: 깃 명령어 정리
date: 2021-02-12 22:00:00 +0900
description: Git commands
img: git_icon.png
tags: Git Study
---

# 깃 명령어 정리 🖥

우아한 테크 코스 과정에서 사용할 주요한 깃 명령어를 기록하기 위한 포스트

## 시작하기

**1. 작업 시작**

- 현재 위치에서 git 시작하기
	> $ git init

- 원격 저장소에서 복사해와서 git 시작하기
	> $ git clone [원격 저장소 주소]

## 현재 작업 변경

**1. Git Add**

- 특정 위치의 작업 내역 스테이징 영역으로 넘기기
	> $ git add [파일 경로 or 디렉토리 경로]
- 현재 디렉토리와 이하 경로의 모든 변경 내용 스테이징 영역으로 넘기기
	> $ git add .
- 작업 디렉토리 내의 모든 변경 내용을 스테이징 영역으로 넘기기
	> $ git add -A
- 각 변경 사항을 터미널에서 직접 눈으로 확인하면서 스테이징 영역으로 넘기거나 제외하기
	> $ git add -p

**2. Git Remove**

- 로컬 디렉토리, git 저장소에서 모두 삭제
	> $ git rm [삭제할 파일명.확장자]
- 로컬 디렉토리는 건들지 않고 git 에서만 삭제
	> $ git rm --cached [삭제할 파일명.확장자]
- 변경 사항을 커밋하지 않았을 경우 강제 삭제
	> $ git rm -f [삭제할 파일명.확장자]
- 디렉토리 삭제
	> $ git rm -r [삭제할 디렉토리명]

- 위의 작업 후 Commit 해야 적용이 됨
	> $ git commit -m "Commit Message"

**3. Git Move**

- 파일 이름 변경하기
	> $ git mv [원본 이름] [변경할 이름]

**4. Git Restore**

- 작업 트리의 파일을 복구 해준다.
- 파일 복구하기
	> git restore [복구할 파일 이름]
- 스테이지에 수정 내용을 이미 넣었을 때(git add 되었을 때) 다시 빼기
	- git restore --staged [복구할 파일 이름]

- 깃 버전 2.23 이후부터 checkout 을 switch 와 restore 로 기능을 분리했다.
	- 기존의 checkout 은 많은 기능들을 수행해서 사용자가 해당 명령어를 사용하는데 있어 모호할 수 있기 때문에 분리되었다고 한다.
		- 기존 기능 : 브랜치 생성, 이동, 복원 등..
	- 여전히 checkout 을 사용할 수 있지만, 되도록 switch 와 restore 를 사용해 보도록 하자!

**5. Git log**

- 커밋 히스토리 조회하기
	> $ git log
- 특정 브랜치 히스토리 조회하기
	> $ git log [Branch Name]
- 모든 작업 내역을 그래프로 예쁘게 보기
	> $ git log --graph --all --decorate

- 보고 난 후 빠져나오기
	> :q (q를 입력하고 Enter 한다.)

**6. Git Status**

- 스테이지 변경 상태 확인하기
	> git status
- 특정 위치의 스테이지 변경 상태 확인하기
	> git status [확인할 위치]

## 로컬 : Git 기본

**1. Git 커밋**

- 커밋 하기
	> $ git commit
    >> 현재 까지 작업 중이던 저장소의 내용을 변경해서 하나의 커밋으로 저장

- 커밋 내용 변경하기
	> $ git commit --amend
	>> 혼자 작업할 때는 상관 없지만 공동 작업을 할 때는 다른 사람의 작업과 충돌이 날 수 있기 때문에 사용을 지양한다.

- 메시지와 함게 커밋하기 방법 1
	> $ git commit -m "Commit Message"
- 메세지와 함께 커밋하기 방법 2
	> $ git commit -m "Commit Title<br></br>
	> commit message 1<br></br>
	> commit message 2<br></br>
	> commit message 3"

**2. Git 브랜치**

- 브랜치를 엄청 작은 단위로 많이 만들게 되면 메모리나 디스크 공간에 부담이 될까?
	- **No!! 작은 단위로 잘게 나누는 것이 좋다!**
- 쉽게 생각하면 "하나의 커밋과 그 부모 커밋들을 포함하는 작업 내역" 이라고 생각하면 된다.
- 커밋들 사이에 책갈피를 끼워 넣는 것이라고 본다.
	- 커밋 단위로 원하는 위치로 이동이 가능하다.
		> git switch [Commit Hashcode]
	- 브랜치는 특정 위치에서 시작해서 그 위치에서 이후에 작업한 내용의 가장 최신 위치의 커밋의 위치를 나타낸다.

- 브랜치 생성
	> $ git branch [Branch Name]
    >> 브랜치를 생성하는 시점의 커밋을 가리킨다.

- 브랜치 이동 1
	> $ git checkout [Branch Name]
- 브랜치 이동 2
	> $ git switch [Branch Name]

- 브랜치 생성 & 이동 1
	> $ git checkout -b [Branch Name]
- 브랜치 생성 & 이동 2
	> $ git switch -c [Branch Name]

- 깃 버전 2.23 이후부터 checkout 을 switch 와 restore 로 기능을 분리했다.
	- 기존의 checkout 은 많은 기능들을 수행해서 사용자가 해당 명령어를 사용하는데 있어 모호할 수 있기 때문에 분리되었다고 한다.
		- 기존 기능 : 브랜치 생성, 이동, 복원 등..
	- 여전히 checkout 을 사용할 수 있지만, 되도록 switch 와 restore 를 사용해 보도록 하자!

- 브랜치 제거
	> $ git branch -D [Branch Name]

**3. Git 합치기**

- 합치고 싶은 곳의 브랜치로 이동하기
	> $ git switch [Branch Name]
- 합칠 대상이 될 브랜치를 현재 브랜치에 합치기
	> $ git merge [Target Branch Name]

**4. Git 리베이스**

- 커밋들을 모아서 복사한 뒤 다른 곳에 떨궈 놓는 것이다.
- 따로 작업한 브랜치를 현재 브랜치 위에 올려놓게 되면서 실제로는 두 기능을 따로 개발했지만, 마치 순서대로 개발한 것처럼 보이게 된다.

- 합치고 싶은 곳의 브랜치로 이동하기
	> $ git switch [Branch Name]
- 합쳐질 곳의 대상이 될 브랜치를 현재 브랜치에 합치기
	> $ git rebase [Target Branch Name]
- 대상 브랜치의 커밋을 합친 브랜치로 이동시키기
	> $ git switch [Target Branch Name]<br></br>
	> $ git rebase [Branch Name]
- [Target Branch Name] 가 [Branch Name] 의 부모쪽에 있었기 때문에, 단순히 그 브랜치를 더 앞쪽의 커밋을 가리키게 이동하는 것이 전부이다.(같은 위치에 있게 됨)

- git merge 에서는 합칠 대상을 현재 브랜치로 가져왔다면, git rebase 에서는 합쳐질 곳의 브랜치로 현재 브랜치가 합쳐진다.

## 로컬 : Git 다음 단계

**1. Git HEAD 분리하기**

- Git에 있는 특수한 포인터이다.
- 현재 체크아웃된(작업중인) 커밋을 가리킨다.
- 항상 작업트리의 가장 최근 커밋을 가리키며 작업 트리에 변화를 주는 git 명령어의 대부분은 HEAD 를 변경하는 것으로 시작한다.
- 일반 적으로 HEAD 는 브랜치의 이름을 가리키고 있다.

- HEAD 를 브랜치에서 분리해보기
	> $ git switch [Commit Hashcode]

**2. Git 상대 참조 1 (Relative Refs)**

- 연산자 (^) : 참조 이름에 하나씩 추가할 때마다, 명시한 커밋의 부모를 찾는다.

- [Branch Name] 의 부모로 이동
	> $ git switch [Branch Name]^
- [Branch Name] 의 조부모로 이동
	> $ git switch [Branch Name]^^
- 현재 위치의 부모로 이동
	> $ git switch HEAD^

**3. Git 상대 참조 2**

- 연산자 (~) : 참조이름 뒤에 ~와 숫자를 명시해주면 해당 숫자만큼 돌아간다.

- 현재 위치에서 4번 돌아가기
	> $ git switch HEAD~4
- 브랜치를 특정 커밋에서 이동하기
	- 이동할 브랜치를 특정 위치를 기준으로 몇 번 이동
		> $ git branch -f [Branch Name] [기준 브랜치 이름 or HEAD]~[이동 횟수]
	- 이동할 브랜치를 특정 위치로 이동
		> $ git branch -f [Branch Name] [브랜치 이름 or 커밋 해시 코드]

**4. Git 작업 되돌리기**

- Reset
	- 브랜치로 하여금 예전 커밋을 가리키도록 이동하는 방식으로 변경 내용을 되돌린다.
		- "히스토리를 고쳐쓴다."
	- 마치 애초에 커밋하지 않은 것처럼 예전 커밋으로 브랜치를 옮긴다.

	- HEAD 를 기준으로 최근 커밋 1개를 지우기
		> $ git reset HEAD~1

- Revert
	- 여러 사람이 작업하는 리모트 브랜치에서는 사용불가!
		> 히스토리를 고쳐쓰기 때문에 이전 커밋을 지워버리면 다른 사람의 작업 내용을 push 할 때, 부모를 잃어서 conflict 가 발생한다.
		> 이 경우엔 revert 를 사용하여 "커밋을 지운다는 내용을 커밋" 한다.

	- 현재 커밋을 되돌리고 다른 사람들과 공유하기
		> $ git revert HEAD

## 로컬 : Git 코드 이리 저리 옮기기

**1. Git 체리-픽**

- 일련의 커밋들에대한 복사본을 만드는 기능이다.

- 현재 위치에서 특정 커밋들을 복사하여 현재 브랜치에 커밋하기
	> $ git cherry-pick [Commit Hashcode]
- 뒤에 여러 개의 커밋 해시코드를 작성하면 여러 개가 동시에 커밋된다.
	> $ git cherry-pick [Commit Hashcode] [Commit Hashcode] [Commit Hashcode]

**2. Git 인터랙티브 리베이스**

- 원하는 커밋을 모르는 상황에서 사용할 수 있다.
- 리베이스할 일련의 커밋들을 검토할 수 있는 가장 좋은 방법이다.
- 인터렉티브 리베이스가 의미하는 뜻은 rebase 명령어를 사용할 때 -i 옵션을 같이 사용한다는 것이다.
	- -i 옵션 추가 시 git은 리베이스의 목적지가 되는 곳 아래에 복사될 커밋들을 보여주는 UI를 띄운다.
	- 각 커밋을 구분할 수 있는 각각의 해시, 메시지도 보여준다.

- 할 수 있는 것
	- 적용할 커밋들의 순서를 UI를 통해 바꿀 수 있다.
	- 원하지 않는 커밋들을 뺄 수 있다. (pick 을 통해)
	- 커밋을 스쿼시 할 수 있다.
	
~~~
스쿼시(Squash) 커밋
	- 여러 개의 커밋을 하나의 커밋으로 압축 시키는 것이다.
	- 우리가 미션을 진행할 때, 수십~수백 개의 커밋을 만들고 Pull Request 를 요청하여 최종적으로 Merge 가 되면 하나의 커밋만 남는데, 이 때 스쿼시 된 것이다.
	- 프로젝트 기준으로 볼 때는 기능을 하나 만들어 낸 것이기 때문에 히스토리를 남길 필요가 없다.
	- 나중에 찾기도 쉬워진다.
~~~

- 현재 위치에서 HEAD 로부터 4번째 부모 까지의 커밋을 재조정 하기
	> $ git rebase -i HEAD~4  
  
## 원격 : Git Push & Pull

- 자신의 로컬 이외에서 접근할 때에 유용하다.
	- 백업의 역할로 매우 좋다.
- 다른 사람들과 함께 프로젝트를 진행할 수 있다.

**1. Git 클론**

- 원격 저장소의 프로젝트를 로컬로 복사해서 가져오기
	> $ git clone [가져올 저장소의 주소]

**2. Git 원격 브랜치**

- 원격 저장소의 상태를 반영한다.
- 로컬에서의 작업과 공개적으로 되고있는 작업의 차이를 이해하는데 도움을 준다.

- 원격 저장소에서 체크아웃하기
	> $ git switch [원격 저장소 이름(default: origin)]/[브랜치 이름]

- 해당 작업을 한 후 커밋을 하면 원격 저장소는 분리된 HEAD 모드로 만든다.
	- 이 경우 커밋을 추가해도 해당 원격 저장소를 갱신하지 않는다.
	- 해당 원격 저장소가 갱신될때만 갱신된다.

- 원격 저장소 브랜치 제거 방법 1
	> $ git push origin --delete [브랜치 이름]
- 원격 저장소 브랜치 제거 방법 2
	> $ git branch -d [브랜치 이름]<br></br>
	> $ git push origin [브랜치 이름]

**3. Git Fetch**

- 원격 저장소에서 데이터를 가져오는 방법
- 원격 저장소에는 있지만 로컬에는 없는 커밋을 다운로드 받는다.
- 로컬의 원격 브랜치가 가리키는곳을 업데이트 한다.
	- 로컬에서 나타내는 원격 저장소의 상태를 실제 원격 저장소의 상태(fetch 를 진행하는 시점)로 동기화 한다.
- fetch 는 원격 저장소의 최신 내용을 받아오지만 로컬 저장소의 내용을 변경하는 것은 아무 것도 없다!!
	- 받아온 원격 저장소의 최신 내용을 실제 로컬 저장소에 적용 시키는 명령어는 따로 있다.
		> $ git cherry-pick [원격 저장소 이름]/[브랜치 이름]<br></br>
		> $ git rebase [원격 저장소 이름]/[브랜치 이름]<br></br>
		> $ git merge [원격 저장소 이름]/[브랜치 이름]

- 원격 저장소에서 내려받기
	> $ git fetch

**4. Git Pull**

- 원격 저장소로부터 fetch 후 로컬 저장소에 merge 하는 경우가 많다보니 한 번에 작업하기 위해 사용되는 명령어다.

- 원격 저장소에서 내려받기 & 합치기
	> $ git pull

**5. Git 가짜 팀워크**

- 원격 저장소 master 에 간단히 하나의 커밋하기
	> $ git fakeTeamwork

- 원격 저장소 master 에 특정 브랜치의 커밋을 여러개 하기
	> $ git fakeTeamwork [브랜치 이름] [커밋 횟수]

**6. Git Push**

- 로컬에서의 작업 내용을 원격 저장소에 공개하기
	> $ git push
    >> 매개변수 없이 사용하면 push.default 라는 git 의 설정에 따라 결정된다.
- push 를 하고나면 자동적으로 로컬의 원격 저장소도 최신화 된다.

**7. Git 엇갈린 작업 (충돌)**

- 여러 사람과 같이 프로젝트를 진행중일때, 커밋 하고자 하는 작업 내역이 원격 저장소의 최신 커밋 상태에 뒤떨어질 때 push 를 하려고 하면 발생한다.
	- 이 때 push 를 하려고 하면 push 가 되지 않는다.

- 로컬의 작업을 원격 브랜치의 최신 상태를 기반이 되게끔 만들면 해결할 수 있다.
	- 리베이스 등을 이용하면 된다.
- rebase: 작업중인 브랜치에서, 최신화 -> 리베이스 -> Push
	> $ git fetch<br></br>
	> $ git rebase [원격 저장소 이름]/[브랜치 이름]<br></br>
	> $ git push [원격 저장소 이름] [브랜치 이름]
- 줄임 명령어
	> $ git pull --rebase<br></br>
	> $ git push

- merge: 작업중인 브랜치에서, 최신화 -> 합치기 -> Push
	> $ git fetch<br></br>
	> $ git merge [원격 저장소 이름]/[브랜치 이름]<br></br>
	> $ git push [원격 저장소 이름] [브랜치 이름]
- 줄임 명령어
	> $ git pull<br></br>
	> $ git push

**8. Git 원격 저장소 거부**

- 원격 저장소의 main 브랜치에는 직접적인 커밋대신 pull request 가 쓰여야 한드는 규칙이 있다.
	- 이 때, 실수로 브랜치에서 작업 하지않고, main 브랜치에서 직접 커밋을 하면 변경 사항을 push 하지 못한다.
		- 이것이 내가 프리코스 최종 시험에서 겪었던 나를 몹시 당황케한 일이다.

	- 해결책
		- feature([브랜치 이름]) 라는 이름의 다른 브랜치를 생성하고 원격 저장소에 push 한다.
		- 원격 저장소와 동기화 될 수 있도록 로컬 저장소의 main 브랜치를 reset 한다.
			- 하지 않으면 다음에 pull 시도할 때 문제가 발생하거나 다른 협업자들의 커밋이 나의 커밋과 충돌할 수 있다.
		- 위의 과정 명령어(이미 작업 해버린 main 브랜치에서 시작)
			> git branch [브랜치 이름]<br></br>
			> git reset main [원격 저장소 이름]/[브랜치 이름]<br></br>
			> git switch [브랜치 이름]<br></br>
			> git push [원격 저장소 이름] [브랜치 이름]

### 참조

- [Git Add](https://www.daleseo.com/git-add/)
- [Git Remove](https://mygumi.tistory.com/103)
- [Git Checkout (Switch, Restore)](https://blog.outsider.ne.kr/1505)
- [Git Game](https://learngitbranching.js.org/?locale=ko)
- [Git branches](https://www.youtube.com/watch?v=MIGliPrUMGE)
