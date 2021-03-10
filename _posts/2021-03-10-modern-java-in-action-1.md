---
layout: post
title: 모던 자바 인 액션 1장
date: 2021-03-09 20:40:00 +0900
description: 모던 자바 인 액션을 읽고 정리
img: modern_java.png
tags: java study
---

# 모던 자바 인 액션 1장.

## 스트림

한 번에 한 개씩 만들어지는 연속적인 데이터 항목들의 모임이다.


1. Java 8 에 추가된 Stream API
   - Stream<T> : T 형식으로 구성된 일련의 항목을 의미한다.
   - 조립 라인처럼 어떤 항목을 연속으로 제공하는 어떤 기능이라고 단순하게 생각할 수 있다.
2. 장점
   - 스트림 파이프라인을 이용해서 입력 부분을 여러 CPU 코어에 쉽게 할당할 수 있다는 부가적인 이득을 얻을 수 있다.
   - 스레드라는 복잡한 작업을 사용하지 않으면서도 공짜로 병렬성을 얻을 수 있다.

## 동작 파라미터화

코드 일부를 API 로 전달하는 기능이다.

- 다음과 같이 미리 만들어진 코드가 있다고 보자

  ```java
  public Number {
  	private final int number;
    
    public Number(final int number) {
      this.number = number;
    }
    
    public Number addNumber(int value) {
      return new Number(number + value);
    }
    
    public number getNumber() {
      return number;
    }
  }
  ```

  - 기존 숫자에서 value 만큼을 더한 수로 바꾸는 기능만 존재하는 Number 클래스가 있다.
  - 즉,  수행할 수 있는 기능이 정해져있다.

- 자바 8에서는 메서드를 다른 메서드의 인수로 넘겨주는 기능을 제공한다.

### 동작 파라미터화

- 위에 설명한 기능을 이론적으로 동작 파라미터화라고 부른다.
- 만약 기존 숫자에서 2를 곱하고 더하는 기능이 필요하다고 한다면 multiplyDouble() 라는 메서드를 만들어서 사용할 수 있겠다.
- 하지만 자바 8 이전에는 메서드를 다른 메서드로 전달할 방법이 없었다.
- multiplyDouble() 를 이용해 addNumber 의 동작을 파라미터화 했던 것처럼 스트림 API는 연산의 동작을 파라미터화 할 수 있는 코드를 전달한다는 사상에 기초하기 때문에 동작파라미터화가 중요하다.

```java
public int multiplyDouble(final int value) {
	return value * 2;
}
```

- 위와 같이 만든 메서드를 addNumber 의 파라미터로 사용할 수 있다.

  ```java
  public class Main {
  	public static void Main(String args[]) {
  		Number number = new Number(10);
  		number.addNumber(number.multiplyDouble(5));
  	}
  }
  ```

## 병렬성

병렬성을 공짜로 얻을 수 있다.

- 병렬성을 얻는 대신에 스트림 메서드로 전달하는 코드의 동작 방식을 조금 바꿔야 한다.
- 스트림 메서드로 전달하는 코드는 다른 코드와 동시에 실행하더라도 안전하게 실행될 수 있어야 한다.
  - 공유된 가변데이터에 접근하지 않아야 <b>안전하게 실행</b>할 수 있는 코드가 된다.
  - 이러한 함수를 순수 함수, 부작용 없는 함수, 상태 없는 함수라 부른다.
- 위 문제를 해결하기 위해서 기존처럼 sunchronized 를 이용하여 가변 데이터를 보호한다면 병렬이라는 목적을 무력화 시킨다.
- 공유 되지 않은 가변 데이터 요구사항이란 함수가 정해진 기능만 수행하며 다른 부작용을 일으키지 않음을 의미한다.

## 자바 함수

프로그래밍 언어에서 함수라는 용어는 정적 메서드와 같은 의미로 사용된다. 자바 함수는 이에 더해 수학적인 함수처럼 사용되며 부작용을 일으키지 않는 함수를 의미한다.