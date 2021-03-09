---
layout: post
title: 우아한 테크 코스 자바 기초 강의 상태 패턴
date: 2021-03-09 20:40:00 +0900
description: Lotto 학습 프로필, 회고
img: woowacourse.png
tags: java study
---

# 상태 패턴

상태 패턴(state pattern)은 객체 지향 방식으로 상태 기계를 구현하는 행위 소프트웨어 디자인 패턴이다.

## 예시

- Card 와 관련된 기능은 있다고 가정하고 구현한다.

```java
public interface State {
	State draw(Card card);
	
	State stay();
	
	boolean isFinished();
	
	Cards cards();
	
	double profit(double money);
}

public abstract class Started implements State {
  protected Cards cards;
  
  public Started(final Cards cards) {
    this.card = cards;
  }
}

public abstract class Finished extends Started {
  public Finished(final Cards cards) {
    super(cards);
  }
  
  @Override
  public State draw(final Card card) {
    throw new IllegalArgumentException("[ERROR] 더 이상 카드를 드로우할 수 없습니다.");
  }
  
  @Override
  public State stay() {
    throw new IllegalArgumentException("[ERROR] 스테이 할 수 없습니다.");
  }
  
  @Override
  public boolean isFinished() {
    return true;
  }
  
  @Override
  public double profit(double money) {
    return money * earningRate();
  }
  
  public abstract double earningRate();
}

public abstract class Running extends Started {
  public Running(final Cards cards) {
    super(cards);
  }
  
  @Override
  public boolean isFinished() {
    return false;
  }
  
  @Override
  public double profit(final double money) {
    throw new IllegalArgumentException("[ERROR] 진행 중에는 이익 확인을 할 수 없습니다.")
  }
}

public class Cards {
  private final List<Card> cards;
  
  public Cards(final Card... cards) {
    this(Arrays.asList(value));
  }
  
  public Cards(final List<Card> cards) {
    this.cards = new ArrayList<>(cards);
  }
  
  public void add(final Card card) {
    cards.add(card);
  }
  
  public List<Card> card() {
    return new ArrayList<>(values);
  }
  
  public boolean isBlackjack() {
    return values.size() == 2 && score().isBlackjack();
  }
  
  public boolean isBust() {
    return score().isBust();
  }
  
  public int score() {
    int score = sum();
    final long numberOfAces = numberOfAces();
    
    for (int i = 0; i < numberOfAces; i++) {
      score = score.plusTenIfNotBust();
    }
    
    return score;
  }
  
  private long numberOfAces() {
    return cards.stream()
      .filter(Card::isAce)
      .count();
  }
  
  private int sum() {
    return (int) cards.stream()
      .mapToInt(Card::getScore)
      .sum();
  }
}

// 상태 클래스
public class Bust extends Finished {
	public Bust(final Cards cards) {
		super(cards);
	}
	
	@Override
	public double earningRate() {
		return 0;
	}
}

public class Stay extends Finished {
  public Stay(final Cards cards) {
    super(cards);
  }
  
  @Override
  public double earningRate() {
    return 1;
  }
}

public class Hit extends Running {
  public Hit(final Cards cards) {
    super(cards);
  }
  
  @Override
  public State draw(final Card card) {
    cards.add(card);
    if (cards.isBust()) {
      return new Bust(cards);
    }
    return new Hit(cards);
  }
  
  @Override
  public State stay() {
    return new Stay(cards);
  }
}

public class Blackjack extends Finished {
  public Blackjack(final Cards cards) {
    super(cards);
  }
  
  @Override
  public double earningRate() {
    return 1.5;
  }
}
```

- 상태 인터페이스를 상속 받는 클래스를 통해 상태를 관리할 수 있다.