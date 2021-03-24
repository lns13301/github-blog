---
layout: post
title: 모던 자바 인 액션 6장 2
date: 2021-03-13 21:00:00 +0900
description: 모던 자바 인 액션을 읽고 정리
img: modern_java.png
tags: java study
---

## 그룹화된 요소 조작

- 다음과 같은 클래스가 존재한다.

  ```java
  public class Dish {
  
      private final String name;
      private final int calories;
      private final Type type;
  
      public Dish(final String name, final int calories, final Type type) {
          this.name = name;
          this.calories = calories;
          this.type = type;
      }
  
      public String getName() {
          return name;
      }
  
      public int getCalories() {
          return calories;
      }
  
      public Type getType() {
          return type;
      }
  }
  
  public enum Type {
      NORMAL,
      DIET,
      FAT
  }
  
  public class Main {
      public static void main(String[] args) {
          List<Dish> menu = Arrays.asList(
                  new Dish("pork", 800, Type.FAT),
                  new Dish("beef", 700, Type.FAT),
                  new Dish("salmon", 450, Type.NORMAL),
                  new Dish("bean", 120, Type.DIET)
          );
        //이곳에서 작업한다.
      }
  }
  ```

- 500 칼로리가 넘는 음식만 필터링 하기 (null Type 미포함)

  ```java
  Map<Type, List<Dish>> caloricDishesByType = menu.stream()
         .filter(dish -> dish.getCalories() > 500)
         .collect(Collectors.groupingBy(Dish::getType));
  ```

- 500 칼로리가 넘는 음식만 필터링 하기 (null Type 포함)

  ```java
  Map<Type, List<Dish>> caloricDishesByType = menu.stream()
          .collect(Collectors.groupingBy(Dish::getType,
                  Collectors.filtering(dish -> dish.getCalories() > 500,
                          Collectors.toList())));
  ```

- 다수준 그룹화

  ```java
  Map<Type, Map<Type, List<Dish>>> dishesByTypeCaloricLevel = menu.stream()
                  .collect(Collectors.groupingBy(Dish::getType,
                          Collectors.groupingBy(dish -> {
                              if (dish.getCalories() <= 400)
                                  return Type.DIET;
                              else if (dish.getCalories() <= 700)
                                  return Type.NORMAL;
                              else return Type.FAT;
                          })));
  ```

- 서브그룹으로 데이터 수집 종류별 개수 구하기

  ```java
  Map<Type, Long> typesCount = menu.stream()
                  .collect(Collectors.groupingBy(Dish::getType, Collectors.counting()));
  ```

- 컬렉터 결과를 다른 형식에 적용하기, 각 서브 그룹에서 칼로리가 높은 요리 찾기

  ```java
  Map<Type, Dish> mostCaloricByType = menu.stream()
                  .collect(Collectors.groupingBy(Dish::getType,
                          Collectors.collectingAndThen(
                                  Collectors.maxBy(Comparator.comparingInt(Dish::getCalories)),
                                  Optional::get
                          )));
  ```


## 분할

분할 함수라 불리는 프레디케이트를 분류 함수로 사용하는 특수한 그룹화 기능이다.

- Boolean 으로 분리하기

```java
Map<Boolean, List<Dish>> partitionedMenu = menu.stream()
                .collect(Collectors.partitioningBy(dish -> dish.getCalories() <= 400));
```

- 장점

  - 참, 거짓 두 가지 요소의 스트림 리스트를 모두 유지한다는 것

- 참, 거짓 각각의 그룹에서 최대값 찾기

  ```java
  Map<Boolean, Dish> mostCaloricPartitionedByNameLength = menu.stream()
                  .collect(Collectors.partitioningBy(dish -> dish.getName().length() < 5, Collectors.collectingAndThen(Collectors.maxBy(Comparator.comparingInt(Dish::getCalories))
                                  , Optional::get)));
  ```

  

### 숫자를 소수와 비소수로 분할하기

- 정수 n을 인수로 받아서 2에서 n까지의 자연수를 소수와 비소수로 나누는 프로그램

  ```java
  public boolean isPrime(int candidate) {
  	return IntStream.range(2, candidate).noneMatch(i -> candidate % i == 0);
  }
  ```

- 소수의 대상을 주어진 수의 제곱근 이하의 수로 제한하기

  ```java
  public boolean isPrime(int candidate) {
  	int candidateRoot = (int) Math.sqrt((double) candidate);
  	return IntStream.rangeClosed(2, candidateRoot).noneMatch(i -> candidate % i == 0);
  }
  ```

- 소수와 비소수로 분리

  ```java
  public Map<Boolean, List<Integer>> partitionPrimes(int n) {
  	return IntStream.rangeClosed(2, n).boxed().collect(Collectors.partitioningBy(candidate -> isPrime(candidate)));
  }
  ```

  

