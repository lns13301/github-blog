---
layout: post
title: 이펙티브 자바 Item 2 - 생성자에 매개변수가 많다면 빌더를 고려하라
date: 2021-03-07 22:30:00 +0900
description: 빌더 패턴 학습 내용
img: effective_java.png
tags: java study
---

# 생성자에 매개변수가 많다면 빌더를 고려하라

## 선택적 매개변수가 많을 때 해결하는 방법

1. 점층적 생성자 패턴

   - 필수 매개변수를 만드는 생성자부터 선택적 매개변수를 만드는 생성자를 하나씩 늘려가는 방법

   - 예시

     ```java
     	public class GameStat {
         
         private final String nickname; // 필수
         private final int healthPoint; // 필수
         private final int manaPoint; // 선택
         private final float power; // 선택
         
         public GameStat(String nickname, int healthPoint) {
           this.nickname = nickname;
           this.healthPoint = healthPoint;
         }
         
         public GameStat(String nickname, int healthPoint, int manaPoint) {
           this.nickname = nickname;
           this.healthPoint = healthPoint;
           this.manaPoint = manaPoint;
         }
         
         public GameStat(String nickname, int healthPoint, int manaPoint, int power) {
           this.nickname = nickname;
           this.healthPoint = healthPoint;
           this.manaPoint = manaPoint;
           this.power = power;
         }
       }
     ```

     - 위의 방법처럼 점층적 생성자 패턴은 매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵다.
     - 실수로 매개변수의 순서를 바꿔 건네주더라도 컴파일러는 알아채지 못하고, 결국 런타임에 엉뚱한 동작을 하게 된다.

2. 자바빈즈 패턴(JavaBeans pattern)

   - 매개 변수가 없는 생성자로 객체를 만든 후, 세터(setter) 메서드들을 호출해 원하는 매개변수의 값을 설정하는 방식이다.

   - 예시

     ```java
     public class GameStat {
         
         private final String nickname; // 필수
         private final int healthPoint; // 필수
         private final int manaPoint; // 선택
         private final float power; // 선택
         
         public GameStat() {
         }
       	
       	public void setNickname(String nickname) {
           this.nickname = nickname;
         }
       
       	public void setHealthPoint(int healthPoint) {
           this.healthPoint = healthPoint;
         }
       
       	public void setManaPoint(int manaPoint) {
           this.manaPoint = manaPoint;
         }
       
       	public void setPower(float power) {
           this.power = power;
         }
     }
     
     public class Main {
       public static void main(String[] args) {
         GameStat gameState = new GameStat();
         
         gameStat.setNickname("닉네임");
         gameStat.setHealthPoint(100);
         gameStat.setManaPoint(50);
         gameStat.setPower(5.0f);
       }
     }
     ```

     - 위와 같은 자바빈즈는 자신만의 심각한 단점을 지니고 있다.
     - 자바빈즈 패턴에서는 객체 하나를 만들려면 메서드를 여러 개 호출해야 하고, 객제가 완전히 생성되기 전까지는 일관성(consistency)이 무너진 상태에 놓이게 된다.
     - 일관성이 무너지는 문제 때문에 자바빈즈 패턴에서는 클래스를 불변([Item17](https://lns13301.github.io/github-blog/))으로 만들 수 없으며, 스레드 안전성을 얻으려면 프로그래머가 추가 작업을 해줘야만 한다.

3. 빌더 패턴(Builder pattern)

   - 점층적 생성자 패턴의 안전성과 자바빈즈 패턴의 가독성을 겸비한 빌더 패턴을 사용하여 위의 문제를 해결할 수 있다.

     - 예시

       ```java
       public class GameStat {
           private final String nickname;
           private final int healthPoint;
           private final int manaPoint;
           private final float power;
       
           public static class Builder {
               // 필드 매개변수
               private String nickname;
               private int healthPoint;
       
               // 선택 매개변수 (기본값으로 초기화한다.)
               private int manaPoint = 0;
               private float power = 0f;
       
               public Builder(final String nickname, final int healthPoint) {
                   this.nickname = nickname;
                   this.healthPoint = healthPoint;
               }
       
               public Builder manaPoint(final int manaPoint) {
                   this.manaPoint = manaPoint;
                   return this;
               }
       
               public Builder power(final float power) {
                   this.power = power;
                   return this;
               }
       
               public GameStat build() {
                   return new GameStat(this);
               }
           }
       
           private GameStat(Builder builder) {
               nickname = builder.nickname;
               healthPoint = builder.healthPoint;
               manaPoint = builder.manaPoint;
               power = builder.power;
           }
       }
       
       public class Main {
           public static void main(String[] args) {
               GameStat gameStat = new GameStat.Builder("닉네임", 100)
                       .manaPoint(50)
                       .power(5.0f)
                       .build();
           }
       }
       ```

       - 이 클라이언트 코드는 쓰기 쉽고, 무엇보다도 읽기 쉽다.
       - 빌더 패턴은 명명된 선택적 매개변수(named optional parameters)를 흉내 낸 것이다.

## 빌더 패턴은 계층적으로 설계된 클래스와 함께 쓰기에 좋다.

- 해당 내용은 심화된 내용이므로 추후에 다시 작성하도록 하겠다.