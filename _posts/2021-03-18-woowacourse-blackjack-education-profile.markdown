---
layout: post
title: 우아한 테크 코스 level 1-3 Blackjack 미션 회고 및 학습 프로필
date: 2021-03-18 23:00:00 +0900
description: Blackjack 학습 프로필, 회고
img: woowacourse.png
tags: java study
---

페어 프로그래밍을 '우기'와 진행하였다.
서로 뿜어내는 성향이 다른 느낌에서 페어를 진행하였다. 나는 업된 분위기라면 우기는 차분한 분위기였다.
매번 새로운 페어와 새로운 경험을 할 수 있어서 유익한 시간을 보낼 수 있다.

# Level 1-3 : Blackjack
  
# 와일더의 학습 프로필 🃏

# [TDD] 테스트 주도 개발 - 1
## 내용
- 기능을 구현하기 앞서 필요한 기능에 대한 테스트 코드 설계를 하고 그에 맞게 구현을 진행

# [OOP] 상속, 인터페이스 - 4
## 내용
- Dealer 와 Player 의 중복되는 기능을 추상 클래스로 처리함

# [JDK] Enum 으로 상태 관리하기 - 3
## 내용
- 카드의 문양, 숫자를 Enum 으로 관리
- 블랙잭 승패를 Enum 으로 관리 (같은 Win이라도 BlackjackWin과 Win으로 구분)

# [JDK] 스트림 API - 1
## 내용
- 다양한 메소드에 적용하여 코드 가독성을 높임

## [자료구조] Stack vs Deque - 4

### 내용

- Stack 클래스는 List 컬렉션 클래스의 Vector 를 상속 받는다.

  - Vector 를 상속받는 다른 Stack 클래스를 새로 만들경우 LIFO 원칙이 아닌 중간에 데이터가 삽입되거나 삭제될 수 있다.
  - Vector 를 상속받아서 사용하면서 생기는 치명적인 단점으로, Vector 가 멀티스레드 프로그래밍에 맞춰졌기 때문에 get(), set() 역할의 모든 메서드에 synchronized 키워드가 붙어있어 성능을 저하시킨다.

- Java SE 6부터 ArrayDeque 클래스가 지원되며 스택과 큐 메모리 구조를 모두 구현하는데 가장 적합한 클래스다.

- Vector 대신 ArrayList 를 사용하는게 좋으며 멀티스레드에서도 ArrayList 를 사용하면 된다.

  ```java
  ArrayList<T> arrayList = new ArrayList<>(Collections.synchronizedList());
  ```

### 링크

[자바에서 Vector와 Stack 컬렉션이 쓰이지 않는 이유?](https://aahc.tistory.com/8)

[[JAVA] 스택(Stack) vs 덱(Deque)](https://chucoding.tistory.com/50)

## [JDK] Stream API (무한 스트림) - 2

### 내용

- 기존에 구현 해두었던 draw() 를 리팩토링 해보기 위해 시도

  ```java
  public Cards draw(Deck deck, int drawCount) {
  	return Stream.generate(deck::draw)
                  .limit(drawCount)
                  .collect(Collectors.collectingAndThen(Collectors.toList(), Cards::new));
  }
  ```

  - 위와 같은 형태로 구현하면 다른 방식으로 좀 더 유연하게 draw() 를 구현할 수 있어 보였다.
  - 하지만 지금까지 진행해온 프로젝트 구조상 위와 같은 기능으로 draw() 를 구현 했더니 Bust 상황일 때 새로운 Cards 를 받아버리는 문제가 발생해서 적용하지 못했다.

### 링크

[Java8의 Stream 생성 방법 및 예제](https://codechacha.com/ko/stream-creation/)

## [GoF 디자인 패턴] 정적 팩토리 메서드와 캐싱 - 3

### 내용

- Result 정보를 반환 시에 정적 팩토리 메서드를 통해서 정보를 확인하도록 하였다.
- Card 52 장의 정보를 캐싱해두는 시도를 했으나 이미 CardFactory 에서 카드를 제조, 셔플을 하기에 적용하기 애매해서 적용하지 못했다.

### 링크

[이펙티브 자바 item1. 생성자 대신 정적 팩터리 메서드를 고려하라](https://lns13301.github.io/github-blog/effective_java_item1/)

[반복적으로 사용되는 인스턴스 캐싱하기](https://woowacourse.github.io/javable/post/2020-06-24-caching-instance/)

## [OCP] 디미터 법칙 - 2

- 한 줄에 한 점만 사용한다.

  ```java
  public class Main {
  	public static void main(String[] args) {
  		Player player = new Player();
  		List<Card> cards = player.getCards(); // 한 번의 메서드로 바로 List<Card> 를 가져온다.
  	}
  }
  
  public class Player {
  	private final Cards cards;
  	
  	public List<Card> getCards() {
  		return cards.getCards();
  	}
  }
  
  public class Cards {
  	private final List<Cards> cards;
  	
  	public List<Cards> getCards() {
  		return cards;
  	}
  }
  ```

### 링크

[디미터의 법칙 (The Law of Demeter)](https://johngrib.github.io/wiki/law-of-demeter/)

## [OOP] VO - 1

### 내용

-  BetMoney 를 배팅한 금액을 의미하는 VO 로 사용하였다.
