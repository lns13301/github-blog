---
layout: post
title: jdbc 수업 내용 정리
date: 2021-04-16 20:30:00 +0900
description: 우테코 수업
img: woowacourse.png
tags: java study
---

# JDBC 수업 내용 정리

2021/04/16 우아한테크코스 Level2 브라운 수업

## 스프레드 시트 익명 질문

- <bold>씨유
  - 앞으로 모든 기술을 학습할 때 소스코드를 보고 할 수 없으니, 스프링을 학습하면서 학습하는 방법을 익혀보면 좋을거 같아요.

### Bean으로 등록하는 기준? 어떤 객체를 Bean으로 등록할까?

- 빈을 등록하면 스프링 빈 컨테이너에서 관리를 한다.
- 저장된 빈을 재사용하면서 어플리케이션에 의존을 부여한다.
- Service 에서 Repository 에 의존해서 코드를 사용한다고 할 때, 빈을 사용 한다면 외부에서 Repository 와 관련된 의존을 주입 받아서 사용가능하다.

>스프링이 관리하는 Bean 을 사용하지 않으면 어떤점이 불편한지 겪어보자.

### DTO, POJO, Java Bean 의 차이점?

- <bold>브라운
  - 용어의 늪에 빠지지 말라.
  - 각 용어의 맥락을 파악해라
  - 형태는 같지만 활용도가 다르다.
  - 우테코에서는 와일더 밖에서는 강규한, 위치에 따라 이름이 다르지만 같을 수 있다.

### 도메인은 왜 빈으로 등록하면 안되나요?

- 도메인 객체는 테스트 할 때 띄우기 번거로우니 빈으로 등록하지 말자

  

## Spring MVC 동작

스프링은 엄청나게 추상화가 많이 되어있어서 어떻게 동작 하는지 모르고 사용하는 경우가 많다. (스프링 매직)<br>앞으로 학습할 때, 원리를 이해하면서 학습하면 좋다.

### 스프링 매직

스프링 매직 부분을 학습하기 전에 다양하게 경험 해본 후(배경지식을 쌓은 후) 스프링 매직부분의 동작원리를 학습하는 것을 추천한다.

## 새로운 것을 학습하는 과정

- 과정 1

  - 새로운 것을 학습할 때 새로운 점을 하나 찍는다고 생각한다.
  - 듬성듬성 곳곳에 점이 찍힌다.
  - 심도있게 학습하며 점과점이 서로 연결된다.

- 과정 2

  - 새로운 것을 학습할 때 점을 하나 찍는다.
  - 다음 점을 찍고 그 점까지 연결한다.
  - 해당 과정을 반복한다.

  

- 위의 과정이 결과적으로는 같을 수 있지만, 브라운의 추천 방법은 과정 2이다.

- 과정 2의 방법은 '지식의 확장'이다.

- 동작하는 것을 눈으로 보고, 학습하는 것을 반복하면 지식의 확장을 통해 학습할 수 있다.

- 처음 점찍는 것이 'Getting Start Guide' 등을 보며 시작하는 것이다.

- 새로운 것을 지루하지 않게 학습 하는 방법이다.

## <bold>스프링만 학습하지 말기

- 브라운

  - http 를 모르고 스프링을 학습하는 것은 의미없는 행위라고 생각한다.

  > 브라운의 추천 도서
  >
  > 그림으로 배우는 HTTP & Network Basic<br>HTTP 완벽 가이드

- 포비
  - 나는 저 책을 정독하는 건 미친 짓이라고 본다.
- 검프
  - 공식문서를 보는게 더 좋을 듯 한거 같네요.

## 테스트로 배우는 Spring JDBC

### 배경 지식 강의

- JdbcTemplate으로 쿼리를 날리는구나!
- 키워드(jdbc template)를 알았으니 사용법을 검색하자!
- Jdbc template 의 학습이 끝나고 나면 연관된 것으로 DataSource 를 사용하는 것에 대해서 또 검색해보는 방식으로 흐름을 파악하고 학습을 한다.
- 활용법(학습 테스트로)을 충분히 익힌 후 내부 동작 과정을 공부하는 순으로 학습한다.

### Serial vs Auto Increment

SERIAL 키워드를 이용해서 컬럼의 타입을 정의함으로써, Sequence 타입을 사용할 수 있다.

AUTO INCREMENT 는 primary key 에 주로 사용된다.

- [SERIAL 과 sequence 전략은 다르다. SERIAL은  BIGINT UNSIGNED NOT NULL AUTO_INCREMENT UNIQUE 설정의 별칭이다.](https://stackoverflow.com/questions/20021983/what-is-the-difference-between-serial-and-auto-increment-in-mysql)

### DAO 사이에 JDBC(내부에 Driver) 를 통해 DB 에 접근한다.

- MySQL
  - JDBC 내부 드라이버가 MySQL Driver 로 바뀌고 DB 는 MySQL이 된다.
- Oracle
  - JDBC 내부 드라이버가 Oracle Driver 로 바뀌고 DB 는 Oracle이 된다.

### Application.properties 에 mysql 관련 설정을 추가해주면 자동으로 연결해준다.



### Todo. ORM 에 대해서 정리해보자.
