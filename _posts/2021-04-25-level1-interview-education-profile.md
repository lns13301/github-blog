---
layout: post
title: 자바의 객체 지향 프로그래밍 학습 프로필 (Level1 모의면접)
date: 2021-04-25 20:50:00 +0900
description: 면접 준비용 레벨 1 학습 프로필
img: woowacourse.png
tags: java study
---

# 우아한테크코스 Lv.1 통합 학습 프로필

모의 면접을 준비하기 위해 학습 로그를 기반으로 학습 내용을 정리한다.

우아한테크코스 레벨 1 과정에서는 객체 지향 프로그래밍과 컨벤션에 관련된 학습을 진행했다. 해당 과정을 학습하면서 그 동안 객체 지향이 아닌 절차 지향에 가까운 코드를 설계했고, 객체를 믿고 객체에게 책임을 맡기지 않았다. 추상화 과정을 거치지 않은 코드는 리팩토링에 취약했고, 이해하기 어려운 코드가 되었다. 올바른 설계 방법을 학습하고 객체 지향적인 코드를 구현하는 것에 노력한 과정이다.

## SOLID

### 단일 책임 원칙

- 단일 책임 원칙은 한 클래스에서 하나의 책임만 가진다는 원칙이다.
- 클래스를 변경하는 이유는 단 한 가지여야 한다.
- SOLID 에서 가장 중요한 원칙이면서 가장 어려운 개념이라고 볼 수 있는데, 그 이유는 한 개의 책임에 대한 정의가 명확하지 않고, 책임을 도출하기 위해서는 다양한 경험이 필요하기 때문이다.
- 단일 책임 원칙을 지키지 않았을 때, 한 책임의 변경이 일어나면 다른 책임과 관련된 코드가 변경될 가능성이 높아진다.

### 개방 폐쇄 원칙

- 기능을 변경하거나 확장할 수 있으면서, 그 기능을 사용하는 코드는 수정하지 않는다.
- 추상화와 다형성(상속)을 이용해서 구현한다.
- 인터페이스를 정의하고 해당 인터페이스를 상속받는 클래스를 구현함으로써 기능 추가가 가능하다.
- 그리고 기능 추가가 되어도 기존 코드는 인터페이스를 가지고 있기 때문에 구현한 클래스로 인한 변경이 생기지 않는다.
- 변화가 예상되는 것을 추상화해서 변경의 유연함을 얻도록 해주었기 때문에 가능한 일이다.
- 다운 캐스팅, if-else 블록이 존재하면 개방 폐쇄 원칙이 깨진다.
- 타입 확인 연산자(instanceof)가 사용된다면 해당 코드는 개방 폐쇄 원칙을 지키지 않을 가능성이 크다.

### 리스코프 치환 원칙

- 상위 타입의 객체를 하위 타입의 객체로 치환해도 상위 타입을 사용하는 프로그램은 정상적으로 동작해야 한다.

  > 정사각형을 직사각형의 일부로 보고 치환을 하게 된다면 한 변의 길이 변경이 일어났을 때, 가로 세로의 길이가 동시에 변경되는 문제가 발생한다.

- 개방 폐쇄 원칙을 받쳐주는 다형성에 관한 원칙을 제공한다.

- 리스코프 치환 원칙이 제대로 지켜지지 않으면 다형성에 기반한 개방 폐쇄 원칙 역시 지켜지지 않는다.

### 인터페이스 분리

- 인터페이스는 그 인터페이스를 사용하는 클라이언트를 기준으로 분리해야 한다. (클라이언트 자신이 사용하는 메서드에만 의존해야 한다.)
- 인터페이스를 분리하지 않고 사용하면 특정 기능과 관련된 인터페이스의 변경이 발생할 때, 같은 인터페이스를 구현한 다른 곳에서도 다시 컴파일을 해야하는 상황이 발생한다.
- 따라서 인터페이스 별로 기능 분리를 해야지 변경이 발생할 때, 위와 같은 상황이 발생하지 않는다.

### 의존 역전 원칙

- 고수준 모듈은 저수준 모듈의 구현에 의존해서는 안된다. 저수준 모듈이 고수쥰 모듈에서 정의한 추상 타입에 의존해야 한다.

  

  ### 고수준 모듈

  - 바이트 데이터를 읽어와 암호하고 결과 바이트 데이터를 쓴다.

  

  ### 저수준 모듈

  - 파일에서 바이트 데이터를 읽어온다.
  - AES 알고리즘으로 암호화한다.
  - 파일에 바이트 데이터를 쓴다.

- 예시

  > // 고수준 모듈
  >
  > 쿠폰을 적용해 가격을 할인 받을 수 있다.
  >
  > 쿠폰은 동시에 한 개만 적용 가능하다.
  >
  > 
  >
  > // 저수준 모듈
  >
  > 가격 계산을 쿠폰에 의존하게 된다면 쿠폰마다 가격 계산 모듈이 변경되는 상황을 초래한다.
  >
  > 위와 같은 상황은 프로그램의 변경을 어렵게 만든다.

- 저수준 모듈이 변경되더라도 고수준 모듈은 변경되지 않아야 한다.

- 고수준 모듈의 입장에서 만든 인터페이스를 저수준 모듈이 의존하게 만들어서 저수준 모듈이 고수준 모듈을 의존하게 만든다.

  - 기존에는 고수준 모듈이 저수준 모듈에 의존한다.



## 추상 클래스와 인터페이스

### 추상 클래스와 인터페이스의 차이점

<b>추상 클래스</b>

- 미완성 설계도
- Is-a ~이다.
- 상태(필드)를 가질 수 있다.
- 중복된 코드를 구현해서 사용할 수 있다. 다만 자바 8부터 default 예약어를 통해 인터페이스에서도 구현할 수 있다.
- 상속 받아서 기능을 확장 시키는 것에 초점이 맞춰져 있다.

<b>인터페이스</b>

- 기본 설계도
- Has-a ~할 수 있는
- static final 필드만 가질 수 있다.
- 다중 인터페이스 상속이 가능하다.
- 구현 객체가 같은 동작을 하도록 보장하기 하는 것에 초점이 맞춰져 있다.



## 상속과 컴포지션

<b>상속</b>

- 부모의 변경이 일어날 때 자식에게도 영향을 미치게 한다.
- 상위 클래스의 구현이 하위 클래스에 노출되므로 캡슐화가 깨진다.
- 완벽한 상속관계에서는 상속으로 구현하는 것이 개발의 편의성을 보장하고 강력하다.
  - 확실한 Is-a 관계
  - Api 에 결함이 없으며 하위 클래스에 전파가 되어도 상관 없는 경우

<b>조합</b>

- 기존 클래스의 변화가 미치는 영향이 적어진다.
- 메서드를 호출해서 사용하기 때문에 캡슐화가 유지된다.



## 함수형 인터페이스

### 함수형 인터페이스란?

- 단 하나의 추상 메서드만 가지는 인터페이스 의미한다.

- 자바의 메서드는 일급 시민의 조건을 만족하지 못하기 때문에 자바는 함수형 프로그래밍 언어가 아니다.

  ><b>First Class Citizen (일급 시민, 일급 객체)</b>
  >
  >변수에 값을 할당할 수 있어야 한다.
  >
  >함수의 파라미터로 넘겨줄 수 있어야 한다.
  >
  >함수의 반환 값이 될 수 있어야 한다.
  >
  >함수에 할당된 이름과 상관없이 고유한 구별이 가능하다.

### Function

- 형태는 Function<T, R> 로 T 는 파라미터 타입, R 은 리턴 타입이다. (BiFunction<T, U, R> 매개변수가 T, U 두 개일때 사용)
- 추상 메서드 apply() 를 가진다.
- 함수형 인터페이스를 정의하면 @FunctionalInterface 애너테이션을 붙여두면 컴파일러에서 해당 애너테이션이 붙은 엔티티가 단일 추상 메서드를 갖춘 인터페이스인지 확인 해준다.

### 장점

- 간결한 표현으로 인한 높은 가독성

- 행위도 값으로 사용이 가능함

- 사이드 이펙트(외부의 값이 변경되는)가 발생하지 않음

- 표현식을 바꿀 수 있는 부수효과가 없음 ( == 참조 투명성을 가짐)

  

## VO 와 원시값 포장

### VO 와 원시값 포장의 차이점

<b>VO</b>

- 값을 표현하기 위해 사용한다.
- 불변성이 보장 되어야 한다.
- Equals, HashCode 재정의를 해줘야 한다.
- 동등한 값이 사용될 때 오버헤드를 줄여 메모리의 부담을 줄여준다.

<b>원시값 포장</b>

- 책임을 분리하기 위해 사용한다. (SRP)
- 원시 값의 상태를 관리할 수 있다.
- 데이터와 로직이 합쳐지면서 응집력이 생긴다.
- SRP, OCP 를 만족할 수 있게 한다.



## 불변 객체

- 자바에서 final 은 진정한 의미의 불변이 아니라 재할당만 금지한다.
  - 진정한 의미의 불변은 객체가 생성된 이후에 메모리에 저장된 값을 변경하는 모든 행위에 의해 변경이 일어나지 않아야 한다.
- 래퍼 클래스 혹은 일급 컬렉션을 사용해야 한다.

### 불변 객체를 사용한 이유

- 한 번 객체가 생성되고 나서 변하지 않는다면 그 객체를 신뢰하고 사용할 수 있다.
- 생성자, 접근 메서드에 대한 방어적 복사가 필요없다.
- 멀티 스레드 환경에서 동기화 처리 없이 객체를 공유할 수 있다.



- 오라클 공식 문서에 따르면 "우리가 불변객체를 사용함에 따라 발생되는 메모리 걱정보다 불변객체를 사용해서 얻는 이점이 크다." 라고 한다.
- 불변 객체를 사용하면 메모리 할당이 더 커지지만, 하드웨어의 성능이 비약적으로 상승한 지금은 하드웨어보다 프로그래머가 안정적으로 프로그래밍을 할 수 있는 것에 더 초점을 맞추는 것이 좋다.

### 일급 컬렉션

- 일급 컬렉션을 사용하지 않고 final 예약어만 붙여서 사용하면 add(), remove() 와 같은 기능으로 인해 불변성이 보장되지 않는다.

- 일급 컬렉션을 만들면 불변 컬렉션으로 만들 수 있다.

  - Collection 을 Wraaping 하면서, 그 외 다른 멤버 변수가 없는 상태다.

  - 비지니스에 종속적인 자료구조가 된다.

    - 중복 사용되는 메서드를 없앨 수 있다. (검증, 기능)

    

## 상태패턴

### 상태 패턴이란?

<b>특정 기능을 수행한 후 다음 상태를 반환하는 것</b>

동일한 메서드가 상태에 따라 다르게 동작할 때 사용할 수 있는 패턴이 상태 패턴(State Pattern)이다. 상태 패턴에서는 상태를 별도의 타입으로 분리하고 상태별로 알맞은 하위 타입을 구현한다.

### 상태 패턴을 왜 사용하는가?

상태 패턴을 사용하지 않고 상태를 enum 과 같은 곳에 if, switch 조건 분기를 사용해서 판별하게 된다면 OCP(개방 폐쇄 원칙)을 준수하지 못하게 된다. 상태가 많아질수록 복잡해지는 조건문이 여러 코드에서 중복해서 출현하고 그만큼 코드 변경을 어렵게 만든다. 상태 패턴을 사용하면 새로운 상태가 추가되더라도 콘텍스트 코드가 받는 영향은 최소화된다.


## Getter & Setter

### 객체에 메세지를 보내라

- Getter & Setter 를 사용하는 것은 캡슐화를 해친다.
- 객체 지향 프로그래밍에서는 객체가 해야할 행위를 믿고 맡겨야 한다.

### Getter

- Getter 는 되도록 보여주는 부분(View)에서만 사용하도록 한다.
- 값을 물어보고 반환하는 형식으로 구현한다.

### Setter

- 되도록 값을 생성자에서 지정해준다.

- 직접적으로 값을 변경하지 않는다.

- 행위에 대해 호출하는 형식으로 값을 변경하도록 구현한다.

  ```java
  // 불변이 아닐 때
  public void move() {
  	this.position++;
  }
  
  // 불변일 때
  public void move() {
  	this.position = new Position(this.position + 1);
  }
  ```



## 배열과 컬렉션

### 배열보다 컬렉션을 사용하라

- Java Collection 자료구조(List, Set, Map 등)를 사용하면 데이터를 조작할 때 다양한 api 를 사용할 수 있다.


## 참고

- 개발자가 반드시 정복해야할 객체 지향과 디자인 패턴

- [[Java] Immutable Object(불변객체)](https://velog.io/@conatuseus/Java-Immutable-Object%EB%B6%88%EB%B3%80%EA%B0%9D%EC%B2%B4)

- [불변객체와 일급 컬렉션](https://velog.io/@pbg0205/TIL2021.02.17-h6a5kwgh)

  