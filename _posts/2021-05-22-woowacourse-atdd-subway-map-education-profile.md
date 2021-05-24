---
layout: post
title: 우아한 테크 코스 level 2-2 지하철 노선도 미션 회고 및 학습 프로필
date: 2021-05-22 20:50:00 +0900
description: atdd subway 학습 프로필, 회고
img: woowacourse.png
tags: spring study
---

# 와일더의 학습 프로필 🃏

atdd-subway-map 미션을 하면서 새롭게 학습한 내용을 정리했습니다.

## [TDD] 인수 테스트 (Acceptance Test Driven Development) - 5

### 내용

- 시스템이 실제 운영 환경에서 사용될 준비가 되었는지 최종적으로 확인하는 테스트 단계다.

  - 예상대로 시스템이 동작하는지 확인한다.

- 시스템의 인수를 위해 요구사항을 사용자가 직접 테스트하여 개발이 완료되었음을 증명하는 테스트다.

- 인수 테스트 목적

  >확신, 배포가능성 평가, 준수성 확인, 피드백

### 링크

- [고객이 확인하는 인수 테스트](https://needjarvis.tistory.com/446)



## [TDD] 종단 간 테스트 (End to End Test) - 5

### 내용

- 시스템이 외부 요구사항을 충족하며 전체 시스템을 끝까지 테스트해서 목표를 달성하는지 확인한다.
- 다른 테스트와 다르게 컴포넌트, 아키텍쳐에 관계 없이 시스템이 전체적으로 비지니스 목표를 충족하는지 확인하는데 집중한다.
- 블랙박스 테스트를 진행한다.
  - 내부 설계는 고려하지 않는다.
  - 요구사항 및 기능성에 기반해서 이루어진다.

### 링크

- [DevOps : End-to-End Test 개념, 종류, 방법](https://jjeongil.tistory.com/899)
- [테스팅의 종류](https://ipex.tistory.com/entry/QA%ED%85%8C%EC%8A%A4%ED%8C%85%EC%9D%98-%EC%A2%85%EB%A5%98)



## [TDD] 단위 테스트 (Unit Test) - 2

모듈(프로그램의 기본 단위) 테스트

### 내용

- 구현 단계에서 각 모듈을 개발한 뒤 명세에 맞게 개발이 되었는 지 확인한다.
- 내부 구조를 모두 확인 가능한 '화이트 박스'테스팅을 수행한다.
- 특정 모듈 테스트 수행 시 해당 모듈과 맞물리는 상위/하위 모델이 필요할 수 있다.

### 링크

[[Terms] 단위 테스트, 인수테스트, 통합테스트 란?](https://m.blog.naver.com/tjdus25/221661896783)



## [TDD] 통합 테스트 (Integrate Test) - 2

### 내용

- 단위 테스트가 끝난 모듈을 통합하는 과정에서 발생할 수 있는 버그를 찾는 테스트다.
- 모듈간의 상호작용을 확인하는 것이 주목적이다.

### 링크

- [통합테스트란 무엇인가요?](http://support.suresofttech.com/ko/support/solutions/articles/5000760844-%ED%86%B5%ED%95%A9%ED%85%8C%EC%8A%A4%ED%8A%B8%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80%EC%9A%94-)



## [Spring] @Transactional - 4

### 내용

트랜잭션

- 여러 과정을 하나의 행위로 묶을 때 사용한다.

- 여러 단계를 수행했을 때 하나라도 실패하면 모두 취소한다. (데이터 무결성 보장)

- Service 레이어에서 적용했다.

  ```java
  @RequiredArgsConstructor
  @Transactional(readOnly = true)
  @Service
  public class LineService {
  	...
  }
  ```

### 링크

- [Spring 트랜잭션에 대해 알아보자](https://flowarc.tistory.com/entry/Spring-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90)



## [Spring] @Valid & BindingResult - 4

### 내용

<b>valid</b>

- Api Controller 에서 간편하게 데이터 검증을 하기 위해서 사용했다.

- 별도의 의존성 추가가 필요하다.

  ```java
  dependencies {
  	implementation 'org.springframework.boot:spring-boot-starter-validation'
  }
  ```

- @Range, @NotNull, @Size, @Positive 등의 애너테이션 검증이 가능하다.

  ```java
      @InitBinder("lineRequest") // Custom Validator 를 등록한다.
      private void initBind(WebDataBinder webDataBinder) {
          webDataBinder.addValidators(new LineValidator());
      }
  
      @PostMapping
      public ResponseEntity<LineResponse> createLine(@RequestBody @Valid LineRequest lineRequest, BindingResult bindingResult) {
          if (bindingResult.hasErrors()) {
              throw new InsufficientLineInformationException();
          }
  
          Station upStation = stationService.find(lineRequest.getUpStationId());
          Station downStation = stationService.find(lineRequest.getDownStationId());
  
          Line line = lineService.create(lineRequest, upStation, downStation);
          LineResponse lineResponse = LineResponse.of(line);
          return ResponseEntity.created(URI.create("/lines/" + line.getId())).body(lineResponse);
      }
  ```

  ```java
  public class LineValidator implements Validator {
      @Override
      public boolean supports(Class<?> clazz) {
          return LineRequest.class.isAssignableFrom(clazz);
      }
  
      @Override
      public void validate(Object target, Errors errors) {
          LineRequest lineRequest = (LineRequest) target;
  
          if (Objects.isNull(lineRequest.getUpStationId()) || Objects.isNull(lineRequest.getDownStationId())) {
              throw new InsufficientLineInformationException();
          }
  
          if (lineRequest.getDownStationId().equals(lineRequest.getUpStationId())) {
              errors.rejectValue("downStationId", "duplicatedStation", "중복된 역입니다.");
          }
      }
  }
  ```

<b>BindingResult</b>

- validator 를 상속받는 클래스에서 객체 값을 검증하는 방식이다.
- hibernate-validator 가 아닌 validation-api 라이브러를 사용한다.

### 링크

- [스프링 데이터 검증 @Valid, BindingResult](https://parkwonhui.github.io/spring/2019/04/22/spring-valid-bindingresult.html)
- [유효성 검사](https://www.notion.so/f478a32a8c704aed823f4bd7b6d34a75)



## [JDBC] RowMapper & BeanPropertyRowMapper - 3

### 내용

<b>RowMapper</b>

- SELECT 쿼리로 조회한 결과를 사용자가 원하는 형태로 여러 개의 값을 반환할 수 있도록 구현해주는 객체다.

- queryForObject 를 통해 객체로도 반환이 가능해진다.

  ```java
      private RowMapper getRowMapper() {
          return (rs, rowNum) -> {
              Long id = rs.getLong("id");
              String name = rs.getString("name");
              String color = rs.getString("color");
              return Line.of(id, name, color);
          };
      }
  ```

<b>BeanPropertyRowMapper</b>

- 모든 Bean Property 를 담아주는 RowMapper 를 자동으로 생성해 주는 객체다.

  ```java
      private final RowMapper rowMapper;
  
      public JdbcLineDao() {
          this.rowMapper = new BeanPropertyRowMapper(Line.class);
      }
  ```

- 다만 위의 클래스로 인스턴스를 생성할 경우 Line.class 에 Setter, NoArgsConstructor 가 필요하기 때문에 사용하지 않았다.

### 링크

- [RowMapper에 대해](https://velog.io/@seculoper235/RowMapper%EC%97%90-%EB%8C%80%ED%95%B4)



## [TDD] RestAssured - 5

### 내용

- Spring Framework Test 클래스 중 하나인 MockMvc 와 다르게 직접 의존성을 추가해야 한다.

  ```java
  // build.gradle
  dependencies {
  	testImplementation 'io.rest-assured:rest-assured:3.3.0'
  }
  ```

- @SpringBootTest 로 등록된 Spring Bean 을 전부 로드한 뒤 테스트를 수행하기 때문에 시간이 오래 걸린다.

- Presentation Layer 외의 Bean 을 다수 사용할 때, MockMvc 테스트를 진행하는 것보다 비용보다 적게 소모된다.

- BDD 스타일로 작성할 수 있고 가독성이 좋다. (인수 테스트에서 사용하기 적절함)

- json data 를 쉽고 편하게 검증할 수 있다.

```java
    @DisplayName("노선 수정 - 실패(변경하려는 노선 이름 중복)")
    @Test
    void updateLine_duplicatedName() {
        // given
        String uri = 노선_생성("신분당선", "bg-red-600").extract().header("Location");
        노선_생성("구분당선", "bg-blue-600");
        LineRequest lineRequest = new LineRequest("구분당선", "bg-blue-600");

        // when and then
        RestAssured.given().log().all()
                .body(lineRequest)
                .contentType(ContentType.JSON)
                .when()
                .put(uri)
                .then().log().all()
                .statusCode(HttpStatus.BAD_REQUEST.value())
                .body(equalTo("이미 등록되어 있는 노선 이름입니다."));
    }
```

### 링크

- [MockMvc vs RestAssured](https://dundung.tistory.com/229)

  

## [TDD] Mockmvc - 5

### 내용

- 웹 애플리케이션을 서버에 배포하지 않고도 스프링 MVC 동작을 재현할 수 있는 라이브러리
- 주로 Controller Layer Unit Test (단위 테스트)에 사용된다.
- @SpringBootTest 를 사용하지 않고 @WebMvcTest 를 통해 Presentation Layer Bean 만 불러온다.
- 그 외 Bean 은 Mock 객체 설정을 해줘서 순수한 Controller 로직을 테스트 한다.

```java
    @Test
    @DisplayName("노선 생성 - 실패(노선 중복 이름)")
    void createLine_duplicatedName() throws Exception {
        // given
        lineDao.save(Line.of("1호선", "bg-red-600"));

        Long upStationId = stationDao.save(Station.from("잠실역")).getId();
        Long downStationId = stationDao.save(Station.from("석촌역")).getId();

        final LineRequest lineRequest =
                new LineRequest("1호선", "bg-green-600", upStationId, downStationId, 10);

        // when
        ResultActions result = 노선_생성(lineRequest);

        // then
        result.andDo(print())
                .andExpect(status().isBadRequest())
                .andExpect(content().string("중복되는 라인 정보가 존재합니다."));
    }
```

### 링크

- [MockMvc vs RestAssured](https://dundung.tistory.com/229)



## [Spring] @ExceptionHandler & @ControllerAdvice - 4

### 내용

<b>ExceptionHandler</b>

- @Controller, @RestController 가 적용된 Bean 내에서 발생하는 예외를 잡아서 하나의 메서드에서 처리해주는 기능을 제공한다.

  ```java
      @ExceptionHandler({SubwayException.class //, OtherException.class})
      public ResponseEntity<String> handleBadRequest(Exception e) {
          return ResponseEntity.badRequest().body(e.getMessage());
      }
  ```

- 위와 같이 사용할 수 있으며 애너테이션에 여러 예외 클래스를 추가할 수 있다.

<b>ControllerAdvice</b>

- @ExceptionHandler 가 하나의 클래스에 대한 것이라면, @ControllerAdvice 는 전역에서 발생할 수 있는 예외를 잡아서 처리해준다.

  ```java
  @RestControllerAdvice
  public class SubwayAdvice {
  
      @ExceptionHandler({SubwayException.class})
      public ResponseEntity<String> handleBadRequest(Exception e) {
          return ResponseEntity.badRequest().body(e.getMessage());
      }
  
      @ExceptionHandler({SubwayNotFoundException.class})
      public ResponseEntity<String> handleNotFound(Exception e) {
          return ResponseEntity.status(HttpStatus.NOT_FOUND).body(e.getMessage());
      }
  }
  ```

- 여러 예외 클래스가 추가되면 가독성이 떨어지므로 같은 예외에 대해서는 상속처리를 하여 예외 처리에 대한 가독성을 향샹시켰다.

### 링크

- [@ControllerAdvice, @ExceptionHandler를 이용한 예외처리 분리, 통합하기(Spring에서 예외 관리하는 방법, 실무에서는 어떻게?)](https://jeong-pro.tistory.com/195)

## [JAVA] PojoBean & Bean - 2

### 내용

<b>POJO Classes</b>

- 자바 객체의 표준이며, Java Language Specification 에 의한 어떤 특정한 엄격한(restriction) 경계가 존재하지 않는다.

- <b>가독성</b>과 <b>재사용성</b>을 중요시하게 사용된다.

- POJO 의 3가지 규칙

  > 1. Extend prespecified classes
  > 2. Implement respecified interfaces
  > 3. Contain prespecified annotations

<b>Beans Classes</b>

- POJO 와 달리 엄격한 규칙을 가지고 있다.
- 모든 JavaBeans 는 POJOs 지만 POJOs 는 JavaBeans 가 아니다.
- 모든 필드의 접근제어자는 private 이어야 한다.
- 모든 필드는 getter, setter 를 가져야 하고 getter, setter 로만 접근할 수 있어야 한다.
- 기본 생성자만 있어야 한다.

### 링크

- [[Java] POJO 와 Beans 의 차이점](https://sanghye.tistory.com/13)



## [ORM] 객체 관계 매핑 (Object Relational Mapping) - 4

### 내용

<b>Object Modeling</b>

- OOP 를 기반으로 생성된 시스템을 표현한다.

<b>Relational Modeling</b>

- 서술어와 비슷한 truth statement 로 정보를 표현한다.

<b>ORM 이란?</b>

- 객체와 관계를 연결해 주는 개념이다.
- ORM 을 이용한 개발은 객체와 데이터베이스의 변형에 유연하게 대처할 수 있도록 해준다.
- 관계형 데이터베이스에 제약을 최대한 받지 않으면서 객체를 클래스로 표현하는 것과 같이 관계형 데이터베이스를 객체처럼 쉽게 표현 또는 사용하는 것이다.

### 링크

- [[ORM] ORM이란?](https://m.blog.naver.com/PostView.nhn?blogId=ljc8808&logNo=220461868128&proxyReferer=https:%2F%2Fwww.google.com%2F)
- [[DB] ORM이란](https://gmlwjd9405.github.io/2019/02/01/orm.html)



## [Srping] MediaType & ContentType - 2

### 내용

- 테스트에서 body 의 타입을 지정해주는 부분에서 궁금증이 생겼었다.

  ```java
  RestAssured.given().log().all()
                  .body(params)
                  .contentType(MediaType.APPLICATION_JSON_VALUE) // 1
                  
  ExtractableResponse<Response> response = RestAssured.given().log().all()
                  .body(params)
                  .contentType(ContentType.JSON) // 2
  ```

<b>MediaType</b>

- Request 와 Response 에서 요청을 주고 받을 때 사용하는 MediaType 을 지정하여 이외의  값을 거를 수 있다.

  >This method can be used to parse an Accept or Content-Type header.
  >
  >이 메서드는 Accept 또는 Content-Type 헤더를 구문 분석하는 데 사용할 수 있다.

<b>ContentType</b>

- 명시한 타입으로 보낸다는 뜻이다.

  > This may be used to specify a request or response.
  >
  > 요청 또는 응답을 지정하는 데 사용할 수 있다.

- 명시한 타입으로 받을 때는 Accept 를 사용한다.

### 링크

- [Spring에서의 MediaType](https://pozafly.github.io/spring/MediaType/)
- [What's the difference between mediatype, contenttype and mimetypes?](https://stackoverflow.com/questions/9258108/whats-the-difference-between-mediatype-contenttype-and-mimetype/9277778#9277778)



## [REST] REST API (Representational State Transfer) - 4

### 내용

- 자원의 이름으로 구분하여 해당 자원의 상태(정보)를 주고 받는 모든 것을 의미한다.

- 자원(resource)의 표현(representation)에 의한 상태 전달

- Restful 하게 URI 를 명시한다는 것을 제이슨은 이렇게 말했다.

  >이러한 요청을 하면 어떠한 응답이 오겠다.

- 즉, 예상한 이름으로 정보를 요청하면 예상한 결과가 나오게끔 만드는 것을 의미한다고 생각하면 된다.

  ```java
  @PostMapping("/lines") // 노선에서 Body 에 노선 정보를 담아서 PostMapping 을 하면 추가할 수 있을 것이다.
  @GetMapping("/lines/{lineId}") // 노선에서 아이디를 주고 GetMapping 을 하면 조회를 할 수 있을 것이다.
  @PutMapping("/lines/{lineId}") // 노선에서 아이디를 주고 PutMapping 을 하면 수정할 수 있을 것이다.
  @DeleteMapping("/lines/{lineId}") // 노선에서 아이디를 주고 DeleteMapping 을 하면 삭제할 수 있을 것이다.
  ```

### 링크

- [[Network] REST란? REST API란? RESTful이란?](https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html)



## [Design Pattern] IoC & DI - 4

### 내용

<b>IoC(Inversion of Control)</b>

- GoF 디자인 패턴에서 유래된 것이다.
- EJB 에서 WAS Servlet Container 에서도 사용된 개념이다.
- 개발자가 직접 객체를 생성하는 것이 아닌 @Autowired 를 통해 Spring Container 에서 직접(제어) 객체를 생성하여 해당 객체에 주입해주는 것이다.
- 위와 같은 상황에서 제어가 역전되었다고 말한다.
- 역할과 책임의 분리를 할 수 있다.
- 객체 생명 관리, 흐름 제어를 제 3자에게 위함하는 프로그래밍 모델이다.

<b>의존성 주입(Dependency Injection)</b>

- 어떤 객체가 사용하는 의존 객체를 직접 만들어 사용하는게 아니라 주입 받아서 사용하는 방법이다.

  ```java
  // new 연산자를 사용하여 직접 객체를 생성한다.
  class LineService {
  	private final LineDao lineDao;
  	
  	public LineService(LineDao lineDao) {
  		this.lineDao = lineDao;
  	}
  }
  
  // Line 이 스프링 컨테이너에 관리되고 있는 Bean 일 경우 @Autowired 를 통해 객체를 주입 받을 수 있다.
  class LineService {
    @Autowired
  	private final LineDao lineDao;
  }
  ```

- 장점

  - 재사용성 증가
  - 테스트에 용이
  - 코드 단순화
  - 코드 가독성 증가
  - 종속성 감속
  - 결합도는 낮추고 응집도는 높임
  - 객체간의 의존관계를 설정할 수 있음

- 단순한 제어의 역전, 객체를 주입하여 유연한 프로그래밍을 할 수 있도록 하는 패턴에 더 명확한 이름을 부여하기 위해 DI 라는 용어를 만들었다.

### 링크

- [DI(dependency Injection)](https://velog.io/@woo00oo/DIdependency-Injection)
- [스프링5 DI](https://lns13301.github.io/github-blog/spring5-chapter3/)
- [의존성 분리에 대한 SOLID 관점에서의 장점](https://lns13301.github.io/github-blog/solid/)
- [IoC, DI란 무엇일까?](https://biggwang.github.io/2019/08/31/Spring/IoC,%20DI%EB%9E%80%20%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C/)



## [Java] Reflection - 2

### 내용

- 객체를 통해 클래스의 정보를 분석해 내는 프로그램 기법을 말한다.

- BeanFactory 는 어플리케이션이 실행한 후 객체가 호출 될 당시 객체의 인스턴스를 생성한다.

- 그 때 필요한 기술이 Reflection 이다.

- 자바는 동적으로 객체를 생성하는 기술이 없었기 때문에 동적으로 인스턴스를 생성하는 Reflection 으로 그 역할을 대신한다.

- 즉, 또다른 클래스를 동적로딩 하여 생성자, 멤버 빌드, 멤버 메서드 등을 사용할 수 있도록 한다.

- 미션에 제공된 Reflection 예시

  ```java
      private Station createNewObject(Station station) {
          Field field = ReflectionUtils.findField(Station.class, "id");
          field.setAccessible(true);
          ReflectionUtils.setField(field, station, ++seq);
          return station;
      }
  ```

### 링크

- [Java Reflection 개념 및 사용법](https://gyrfalcon.tistory.com/entry/Java-Reflection)
- [자바 리플렉션(Java Reflection)과 동적 로딩](https://madplay.github.io/post/java-reflection)