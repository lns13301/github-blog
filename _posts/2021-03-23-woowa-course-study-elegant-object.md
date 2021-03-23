---
layout: post
title: 엘레강트 오브젝트 수업 내용 정리
date: 2021-03-23 11:30:00 +0900
description: 우테코 수업
img: woowacourse.png
tags: java study
---

## 의존성 주입 (Dependency Injection)

- 코드를 구현하면 테스트하기 쉬운 코드가 만들어진다.변화에 유연하게 대응할 수 있다.

- 스프링 프레임워크는 DI 를 강제 하도록 되어있다. 필요성을 모르는 상태에서 DI 를 하게 되는데, 위의 이유 때문이라고 보면된다.

- 순수 자바 프로그래밍을 하게 된다면 DI 를 이해할 수 있다.

  

- 스프링 프레임워크를 사용하지 않은 상태에서 DI 를 경험 해본적이 있습니까?

  - 설명할 수 있어야 한다.
  - 자신만의 DI 철학이 있어야 한다.

- 생성자로 의존성을 넣어 필드로 사용할 수도 있고 메서드로 의존성을 주입할 수도 있다.

```java
private RandomNumbers randomNumbers;
private final List<Car> cars; // 상태 데이터

public void move() {
	List<Number> numbers = randomNumbers.numbers(car.size());
	for (int i = 0; i < numbers.size(); i++) {
		Car car = cars.get(i);
		car.move(numbers.get(i));
	}
}

public void move(RandomNumbers randomNumbers) {
	List<Number> numbers = randomNumbers.numbers(car.size());
	for (int i = 0; i < numbers.size(); i++) {
		Car car = cars.get(i);
		car.move(numbers.get(i));
	}
}
```

- 각각의 장단점이 있다.
- 재사용 되지 않을 경우에는 필드에 생성해두고 사용하는게 용이할 수 있다.
- 의존성 주입이 많은 경우에는 매번 메서드를 호출할 때마다 의존성을 주입 해야하는 점 때문에 생성자에서 주입 받는게 좋을 수 있다.

- (생성자로 생성해둔) 의존성을 위한 Setter 는 괜찮을까?
  - 변경을 해도 상관 없다.
  - 상태 데이터를 가지지 않는 객체는 변경해도 된다!
  - 상태를 가지는 객체는 매번 객체를 만들어야한다.
    - 다수가 사용하는 백엔드 멀티쓰레드 상황에서 문제가 발생하지 않는다.
  - RandomNumbers 의 기능은 상태 데이터를 만드는 행위를 만드는 메서드를 분리해야한다.
  - 왜 필요할까?
    - 필요한 경우들이 있을 수 있다.

## 5개 이하의 public 메서드만 노출하기

10개 정도의 생성자 2개 이하의 필드 5개 이하의 메서드(2개가 좋다)가 이상적인 클래스다.

- 가장 우아하고, 유지보수가 가능하고, 응집력이 높으면서 좋은 코드다.
- Protected 도 포함해서 5개 이하가 좋다.
- 인스턴스 변수를 2개만 가지는게 좋지만 어렵기 때문에 4개 이하로 제한해본다.
  - 캡슐화할 것을 권장한다.
- 클래스를 작게 만들어라
- 단일 책임 원칙을 지킬 수 있게 된다.
- 응집도가 높아진다.

## 불변 객체로 만들어라

사이드 이펙트를 줄여야 한다!

- 함수형 프로그래밍은 사이드 이펙트를 만들지 않아서 좋은 코드지만, 성능이 떨어져서 현장에서 주로 사용하지 않는다.
- 인스턴스가 많아져서 성능이 떨어진다.
- 캐싱 전략을 통해서 성능 문제의 상당부분을 해소할 수 있다!
- 애플리케이션단에서 발생하는 성능 문제는 1%도 안될 것이다.
  - 성능 문제가 발생 후 가변객체로 바꾸면 된다.
- 기존에 만들어진 리스트를 clear() 로 비우고 재할당하는 것은 불변객체의 규칙을 지킨다고 할 수 있다.

```java
private final List<Car> cars;

public Cars movedCars() {
	List<Car> movedCars = new ArrayList<>();
	movedCars.clear();
	return new Cars(movedCars);
}
```



## Public 상수를 사용하지 마세요

- 객체들은 어떤 것도 공유해서는 안된다.
- 독립적이어야 하며, 닫혀 있어야(closed) 한다.
- 상수를 위한 클래스 분리는 지양한다.
  - 머리 쓰기 싫으니까 한 곳에 때려박는 행위
- 상수가 관련된 어떠한 객체에 위치하는 것이 좋다.
- enum 에 대해서도 정확하게 동일한 규칙이 적용된다.
  - enum 을 사용하지 않고 코드를 구현해보자!

