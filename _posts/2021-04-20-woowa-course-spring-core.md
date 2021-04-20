---
layout: post
title: core 수업 내용 정리
date: 2021-04-20 21:40:00 +0900
description: 우테코 수업
img: woowacourse.png
tags: java study
---

# Spring Core 수업



## ChessService -> ChessDao 의존 추가

- 직접 의존 추가

  ```java
  @Service
  public class ChessService {
  	private ChessDao chessDao;
  	
  	public ChessService() {
  		this.chessDao = new ChessDao();
  	}
  	
  	public String sayHi() {
  		return chessDao.sayHi();
  	}
  }
  ```

- 직접 의존 추가

  ```java
  public class ChessService {
  	private ChessDao chessDao;
  	
  	public ChessService() {
  		DataSource dataSource = new DataSource(...);
  		JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
      this.chessDao = new ChessDao(jdbcTemplate);
  	}
    
    public String sayHi() {
      return chessDao.sayHi();
    }
  }
  ```

- 변경이 일어나면 매번 관리를 해줘야 한다.
  - 스프링 컨테이너 : 내가 관리해 줄게

## (스프링) 컨테이너

- 스프링에서 활용할 객체들을 담아두는 영역

  - 스프링 빈을 담아둔다.
  - 필요할 때마다 컨테이너에 담긴 빈을 꺼내쓴다.

- 의존 주입 받기

  ```java
  public class ChessService {
  	private ChessDao chessDao;
  	private JdbcTemplate jdbcTemplate;
  	
  	public ChessServiece(JdbcTemplate jdbcTemplate) {
  		this.jdbcTemplate = jdbcTemplate;
  	}
  	
  	public String sayHi() {
  		return jdbcTemplate.execute(...);
  	}
  }
  ```

- 관리하고 싶은 객체(비즈니스 객체)를 Configuration Metadata 를 통해서 스프링 컨테이너에 담으면 사용할 준비가 된다.

- 스프링이 객체를 관리하게 하려면

  - 객체 관리 대상 알려주기

    - @Repository

  - 의존성 설정하기

    ```java
    public ChessServiece(JdbcTemplate jdbcTemplate) {
    		this.jdbcTemplate = jdbcTemplate;
    }
    ```

## @ComponentScan

- ComponentScan 클래스 하위에 특정 애너테이션이 붙은 클래스를 스프링 빈으로 지정
  - @Component (해당 애너테이션을 붙이면 빈에서 관리한다는 뜻)
    - @Controller
    - @Service
    - @Repository
    - 위의 상속된 애너테이션은 기능의 차이는 없지만 추가적으로 라이브러리에서 '@Controller 만 찾는다' 등의 행위를 할 때 만 구별된다.
- @ComponentScan 애너테이션을 통해 등록할 빈을 스캔 할 classPath를 지정

## 수정자 주입

A -> B 의존성 주입 방법

- 생성자 주입 (without 스프링 컨테이너)

  ```java
  public class SimpleMovieLister {
  	// the SimpleMovieLister has a dependency on a MovieFinder
  	private MovieFinder movieFinder;
  	
  	// a constructor so that the Spring Container can inject a MovieFinder
  	public SimpleMovieLister(MovieFinder movieFinder) {
      this.movieFinder = movieFinder;
    }
    
    // business logic that actually uses the injected MovieFinder is omitted...
  }
  ```

- 컨테이너 설정 방법 - 의존성 설정 방법

  - 의존성 등록 방법 - 생성자 기반 의존성 주입

    ```java
    public class MovieRecommender {
    	private final CustomerPreference customerPreference;
    	
    	@Autowired // 생성자가 1개만 있으면 @Autowired 생략 가능 (스프링 >= 4.3)
    	public MovieRecommender(CustomerPreference customerPreference) {
    		this.customerPreference = customerPreference;
    	}
    }
    ```

- 스프링 빈이 공유하면 안되는 객체를 의존 할 경우
  - 빈에 등록된 곳의 필드는 공유되기 때문에 주의가 필요하다.

## DTO POJO JAVABEAN 다 비슷해 보이고 설명도 제각각이라 명확한 개념이 안잡혀요

- JAVA BEAN
  - 규약
    - 클래스는 직렬화되어야 한다.
    - 클래스는 기본 생성자를 가지고 있어야 한다.

- POJO ?
  - Spring 에서 관리 하고자 하는 객체를 스프링 설정을 통해서 스프링 Bean 을 만들 때, 그 만들기 이전의 상태를 POJO 라고 한다.
  - 특정 기술이 가미되지 않은 순수한 자바 객체를 뜻한다.
  - POJO 는 Java Bean 이지만, Java Bean 은 POJO 가 아니다.
- EJB ?
  - Enterprise Java Bean
  - Entity Bean or Session Bean 을 구현하면서 작성해야한다.
    - 특정 위치에 종속되는 경우가 많다.
      - 상속 해줘야 하기 때문이다.
    - 다른 프레임워크를 사용할 수 없게 된다.
    - 프로그래밍 모델이 복잡함
    - 객체 지향적이지 못함
    - 특정 환경/기술에 종속적
  - J2EE Development without EJB
    - 스프링의 시작이 된 책
- EJB 대신 POJO

