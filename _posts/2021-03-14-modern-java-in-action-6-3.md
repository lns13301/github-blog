---
layout: post
title: 모던 자바 인 액션 6장 3
date: 2021-03-14 19:40:00 +0900
description: 모던 자바 인 액션을 읽고 정리
img: modern_java.png
tags: java study
---

## Collector 인터페이스

리듀싱 연산(즉, 컬렉터)을 어떻게 구현할지 제공하는 메서드 집합으로 구성된다.

- Collector 인터페이스의 시그니처와 다섯 개의 메서드 정의

  ```java
  public interface Collector<T, A, R> {
  	Supplier<A> supplier();
    BiConsumer<A, T> accumulator();
    Function<A, R> finisher();
    BinaryOperator<A> combiner();
    Set<Characteristics> characteristics();
  }
  ```
  - T는 수집될 스트림 항목의 제네릭 형식이다.
  - A는 누적자, 즉 수집 과정에서 중간 결과를 누적하는 객체의 형식이다.
  - R은 수집 연산 결과 객체의 형식(항상 그런것은 아니지만 대개 컬렉션 형식)이다.

- Stream<T> 요소를 List<T> 로 수집하는 ToListCollector<T> 라는 클래스

  ```java
  public class ToListCollector<T> implement Collector<T, List<T>, List<T>>
  ```

## Collector 안 메서드 살펴보기

### supplier 메서드 : 새로운 결과 컨테이너 만들기

- 빈 결과로 이루어진 Supplier를 반환해야 한다.

- 수집 과정에서 빈 누적자 인스턴스를 만드는 파라미터가 없는 함수다.

- ToListCollector 에서 supplier는 다음처럼 빈 리스트를 반환한다.

  ```java
  public Supplier<T> supplier() {
  	return () -> new ArrayList<T>();
  } // 방법 1
  public Supplier<T> supplier() {
  	return ArrayList::new;
  } // 방법 2
  ```

### accumulator 메서드 : 결과 컨테이너에 요소 추가하기

- 리듀싱 연산을 수행하는 함수를 반환한다.

- 스트림에서 n 번째 요소를 탐색할 때 두 인수, 즉 누적자(스트림의 첫 n-1개 항목을 수집한 상태)와 n번째 요소를 함수에 적용한다.

- 반환값은 void

- ToListCollector 에서 accumulator 가 반환하는 함수는 이미 탐색한 항목을 포함하는 리스트에 현재 항목을 추가하는 연산을 수행한다.

  ```java
  public BiConsumer<List<T>, T> accumulator() {
  	return (list, item) -> list.add(item);
  } // 방법 1
  public BiConsumner<List<T>, T> accumulator() {
  	return List::add;
  } // 방법 2
  ```

### finisher apothem : 최종 변환값을 결과 컨테이너로 적용하기

- 스트림 탐색을 끝내고 누적자 객체를 최종 결과로 반환하면서 누적 과정을 끝낼 때 호출할 함수를 반환해야 한다.

- 때로는 ToListCollector 처럼 누적자 객체가 이미 최종 결과인 상황도 있다.

- 이런 때는 변환 과정이 필요하지 않으므로 항등 함수를 반환한다.

  ```java
  public Function<List<T>, List<T> finisher() {
  	return Function.identity();
  }
  ```

### combiner 메서드 : 두 결과 컨테이너 병합

- 스트림의 서로 다른 서브파트를 병렬로 처리할 때 누적자가 이 결과를 어떻게 처리할지 정의한다.

- toList 의 combiner 는 비교적 쉽게 구현할 수 있다.

  ```java
  public BinaryOperator<List<T>> combiner() {
  	return (list1, list2) -> {
  		list1.add(list2);
  		return list1;
  	}
  }
  ```

  - combiner 메서드를 이용하면 스트림의 리듀싱을 병렬로 수행할 수 있다.

### Characteristics 메서드

- 컬렉터의 연산을 정의하는 Characteristics 형식의 불변 집합을 반환한다.
- 스트림을 병렬로 리듀스할 것인지 그리고 병렬로 리듀스한다면 어떤 최적화를 선택해야 할지 힌트를 제공한다.
  - UNORDERED : 리듀싱 결과는 스트림 요소의 방문 순서나 누적 순서에 영향을 받지 안흔ㄴ다.
  - CONCURRENT : 다중 스레드에서 accumulator 함수를 동시에 호출할 수 있으며 이 컬렉터는 스트림의 병렬 리듀싱을 수행할 수 있다. 컬렉터의 플래그에 UNORDERED 를 함께 설정하지 않았다면 데이터 소스가 정렬되어 있지 않은 상황에서만 병렬 리듀싱을 수행할 수 있다.
  - IDENTITY_FINISH : finisher 메서드가 반환하는 함수는 단순히 identity 를 적용할 뿐이므로 이를 생략할 수 있다. 따라서 리듀싱 과정의 최종 결과로 누적자 객체를 바로 사용할 수 있다. 또한 누적자 A를 결과 R로 안전하게 형변환할 수 있다.