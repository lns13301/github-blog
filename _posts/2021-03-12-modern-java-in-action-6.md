---
layout: post
title: 모던 자바 인 액션 6장
date: 2021-03-12 20:20:00 +0900
description: 모던 자바 인 액션을 읽고 정리
img: modern_java.png
tags: java study
---

# 모던 자바 인 액션 6장.

## 스트림으로 데이터 수집

스트림이란 데이터 집합을 멋지게 처리하는 게으른 반복자

- 중간 연산
  - filter, map
- 최종 연산
  - count, findFirst, forEach, reduce

## 리듀싱과 요약

- 다음과 같은 클래스와 리스트가 준비 되어 있다.

  ```java
  public class Dish {
  
      private final String name;
      private final int calories;
  
      public Dish(final String name, final int calories) {
          this.name = name;
          this.calories = calories;
      }
  
      public String getName() {
          return name;
      }
  
      public int getCalories() {
          return calories;
      }
  }
  
  public class Main {
    public static void main(String[] args) {
      List<Dish> menu = new ArrayList<>();
      // 이곳에서 예시 작업을 진행
    }
  }
  ```

- 스트림값에서 최대값과 최소값 검색

  ```java
  Optional<Dish> mostCalories = menu.stream()
                  .collect(Collectors.minBy(Comparator.comparingInt(Dish::getCalories)));
  ```

- 요약 연산 평균값 구하기

  ```java
          double averageCalories = menu.stream()
                  .collect(Collectors.averagingDouble(Dish::getCalories));
  ```

- 요약 연산 데이터 수집

  ```java
  public class Main {
      public static void main(String[] args) {
          List<Dish> menu = Arrays.asList(
                  new Dish("pork", 800),
                  new Dish("beef", 700),
                  new Dish("salmon", 450),
                  new Dish("bean", 120)
          );
          IntSummaryStatistics menuStatistics = menu.stream()
                  .collect(Collectors.summarizingInt(Dish::getCalories));
          System.out.println(menuStatistics);
      }
  }
  
  // 출력 결과
  // IntSummaryStatistics{count=4, sum=2070, min=120, average=517.500000, max=800}
  ```

- 문자열 연결

  ```java
          String shortMenu = menu.stream()
                  .map(Dish::getName) // toString() 메서드가 포함되어 있으면 이 부분 생략 가능
                  .collect(Collectors.joining(","));
          System.out.println(shortMenu);
          
          // 출력 결과
          // pork,beef,salmon,bean
  ```

- 범용 리듀싱 요약 연산

  ```java
  int totalCalories1 = menu.stream()
                  .collect(Collectors
                           .reducing(0, Dish::getCalories, (i, j) -> i + j));
  int totalCalories2 = menu.stream()
                  .map(Dish::getCalories)
                  .reduce(0, Integer::sum);
  // 첫 번째 인수 0은 인수가 없을 때 반환값이다.
  // 두 번째 인수는 칼로리를 정수로 변환할 때 사용하는 함수다.
  // 세 번째 인수는 두 항목을 하나로 더하는 BinaryOperator 다.
  
  /* collect 메서드는 도출하려는 결과를 누적하는 컨테이너를 바꾸도록 설계된 메서드인 반면, reduce 는 두 값을 하나로 도출하는 불변형 연산이라는 점에서 의미론적인 문제가 일어난다.
  가변 컴테이너 관련 작업이면서 병렬성을 확보하려면 collect 메서드로 리듀싱 연산을 구현하는 것이 바람직하다.*/
  ```

  - 하지만 reduce 는 박싱, 언박싱 비용이 들어가서 for-loop 보다 느리다.

  - 그렇기 때문에 IntStream 에서 제공하는 메서드가 있으면 그것을 사용하는 것이 더 유용하다.

    ```java
    int totalCalories3 = menu.stream().mapToInt(Dish::getCalories).sum();
    ```

- (응용) 가장 높은 칼로리 찾기

  ```java
  Optional<Dish> maxCalories = menu.stream()
                  .reduce((d1, d2) -> d1.getCalories() > d2.getCalories() ? d1 : d2);
  ```



## Collect ? Stream API 에서 제공하는 메서드 ? 어떤걸 사용하지?

- 컬렉터를 사용하는 것이 Stream API 에서 제공하는 메서드를 이용하는 것보다 코드가 더 복잡하다.
- 하지만 재사용성과 커스터마이즈 가능성을 제공하는 높은 수준의 추상화와 일반화를 얻을 수 있다.

