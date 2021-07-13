---
layout: post
title: 우아한테크코스 level 2-3 지하철 경로 조회 미션 회고 및 학습 프로필
date: 2021-05-24 21:30:00 +0900
description: atdd subway 학습 프로필, 회고
img: woowacourse.png
tags: spring study
---

# 와일더의 학습 프로필 🃏

atdd-subway-path 미션을 하면서 새롭게 학습한 내용을 정리했습니다.

## [Authentication] 인증 - 5

### 내용

식별된 정보로 서비스에 동록된 유저의 신원을 입증하는 과정

- base64Encode 를 통해 로그인 정보를 인코딩한다.
- 요청 헤더에 만들어진 로그인 정보를 담아서 보내준다.
- DB 를 체크하고 실제로 값이 있으면 ok 응답을 한다.

매번 인증을 해야한다. 브라우저의 스토리지를 사용해서 이를 해결한다.

### 링크

- [토니의 인증과 인가](https://www.youtube.com/watch?v=y0xMXlOAfss&t=1136s)

## [Authorization] 인가 - 2

### 내용

인증된 사용자에 대한 자원 접근 권한 확인

<b>Oauth</b>

다른 웹사이트 상의 자신들의 정보에 대해 접근 권한을 부여할 수 있는 공통적인 수단 (개방형 표준)

- 동작 순서
  - 유저 브라우저에서 Oauth 제공자 측으로 로그인을 시도한다.
  - Oauth 에서 유저 브라우저에 아이디가 존재하는지 요청한다.
  - 유저 브라우저에서 권한 부여를 설정해서 Oauth 측에 전송한다.
  - Oauth 에서 유저 브라우저에 code 를 반환한다.
  - 유저 브라우저에서 Query param 에 code 가 있으면 서비스에 요청을 보낸다.
  - 서비스에서 Oauth 측에 액세스 토큰을 요청한다.
  - Oauth 가 서비스에 토큰을 제공하게 되어 권한을 가질 수 있게된다.
  - 유저 브라우저에서 요청을 서비스로 하면 서비스가 Oauth 측에 요청을 보내고 Oauth 는 서비스에 응답하면 서비스가 유저 브라우저에 Oauth 로부터의 응답을 보낸다.

<b>장점</b>

- 사용자
  - 서비스에 ID/PW 를 알려주지 않아도 된다.
  - 원할 때 엑세스 토큰의 권한 취소가 가능하다.
- 서비스
  - 유저의 엑세스 토큰만 가지고 있으면 된다.
  - 사용자의 ID/PW 를 몰라도 허가 받은 API 접근이 가능하다.

### 링크

- [루피의 인증과 인가](https://www.youtube.com/watch?v=JZgD8aPkHSc&t=35s)

## [HTTP] CORS (Cross Origin Resource Sharing) - 5

### 내용

기존 브라우저의 정책은 보안상의 이유로 다른 도메인의 리소스를 가져오는 것이 불가능했다. (Single-Origin-Policy)

이를 해결하기 위해 등장한 표준 기술이 CORS 다. 도메인이 다른 자원에 리소스를 요청할 때 접근 권한을 부여하는 매커니즘이다. 다른 도메인의 자원을 쓰려면 자원의 주인이 허락한 규약을 지켜야하는 그러한 규약을 표준화 한 것이 CORS 다.

- CrossOrigin 애너테이션을 통해 쉽게 해결할 수 있다.

  ```java
  public class PathApiController {
  	@CrossOrigin("http://localhost:8081") // 옵션을 주지 않으면 모든 도메인에 대해 개방된다.
  	@GetMapping("/paths")
  	public ResponseEntity findPath() {
  		...
  	}
  }
  ```

- 세부적으로 자신의 서버에 CrossOrigin 을 Configuration 을 추가해서 하는 방법도 있다.

  ```java
  @Configuration
  public class WebConfig implements WebMvcConfigurer {
      @Override
      public void addCorsMappings(CorsRegistry registry) {
          registry.addMapping("/**").allowedMethods("*").allowedOriginPatterns("*");
      }
  }
  ```

인터셉터를 사용할 경우 CrossOrigin 의 검증이 이루어지기 전에 요청이 반환 될 수 있는데, 이 경우에는 Option 을 주어서 해결할 수 있다.

### 링크

- [Cors란?](https://woowacourse.github.io/javable/post/2020-07-18-cors/)

## [AWS] Load Balancer - 2

### 내용

서버에 가해지는 부하(=로드)를 분산(=밸런싱)해주는 장치 또는 기술을 통칭한다. 클라이언트와 서버풀 사이에 위치하며, 한 대의 서버로 부하가 집중되지 않도록 트래픽을 관리해 각각의 서버가 최적의 퍼포먼스를 보일 수 있도록 한다. 로드밸런싱은 여러 대의 서버를 두고 서비스를 제공하는 분산 처리 시스템에서 필요한 기술이다.

Scale-out 방식으로 성능이 낮은 서버를 두 대 이상 배치할 경우 서버로 트래픽을 균등하게 분산해주는 로드밸런싱이 반드시 필요하다.

<b>L4</b>

- Transport Layer(IP, Port) 레벨에서 로드 밸런싱을 한다.
- TCP, UDP
- IP 주소나 포트번호, MAC 주소, 전송 프로토콜에 따라 트래픽을 나누는 것이 가능하다.

<b>L7</b>

- Application Layer(사용자의 Request) 레벨에서 로드 밸런싱을 한다.
- HTTP, HTTPS, FTP
- HTTP 헤더, 쿠키 등과 같은 사용자의 요청을 기준으로 특정 서버에 트래픽을 분산하는 것이 가능한다.

### 링크

- [로드밸런서의 개념과 특징](https://post.naver.com/viewer/postView.nhn?volumeNo=27046347&memberNo=2521903)
- [로드 밸런서(Load Balancer)란?](https://nesoy.github.io/articles/2018-06/Load-Balancer)

## [Token] JWT (Json Web Token) - 5

### 내용

JSON 객체를 사용하여 가볍고 자가수용적인 방식으로 정보를 안정성있게 전달해주기 위한 토큰이다.

- 다음과 같은 형태로 구성된다.

```java
sdfjsadlkfjaskldf.sdklfjasljfa.asdklfjaslkfj
// 헤더(header).내용(payload).서명(signiture)
```

- 헤더는 사용한 해쉬 알고리즘과 토큰 타입 정보가 들어있다.
- 내용은 json 으로된 데이터가 들어있다.
- 서명은 암호화 키(예를들어 base64UrlEncode)가 들어있다.

JWT 자체는 해독하기 쉽기 때문에 비밀번호 같은 민감한 정보는 담지 않는다. 시크릿 키를 서버 내부에 잘 관리해야 한다. 세션 스토리지와 연결하지 않고 시크릿 키를 이용해서 토큰을 만들어 낸다. 본인이 가진 시크릿 키로 유효성 검사를 진행한다.

<b>문제점</b>

- 해커에게 탈취당하면 access 권한을 관리자로 바꿀 수 있다.
- 만료기간을 정해서 문제를 해결하려고 한다.
- 하지만 짧은 인증기간 때문에 불편한데, 이 때문에 나온 것이 Refresh Token 이다.

최초 로그인 시 액세스 토큰(만료기한 : 30분)과 Refresh 토큰(만료기한 : 2주)을 함께 만들어 Refresh 토큰은 따로 저장소에 저장하고 클라이언트에게 두 정보를 전송한다. 클라이언트가 요청할 때 엑세스 토큰이 만료되었다면 Refresh 토큰을 확인 해보고 올바른 Refresh 토큰이라면 새로운 액세스 토큰을 생성해서 전달한다.

<b>해독 순서</b>

- 이름으로 유저를 찾아낸다.
- 만료시기를 확인한다.
- 권한까지 확인할 수 있다.
- 단, 비밀번호는 담지 않는다. 토큰 자체가 유효성 검사를 통과했기 때문에 비밀번호의 역할을 할 수 있다.

<b>사용 과정</b>

- 브라우저에서 회원정보를 입력한다.
- 서버에서 JWT 를 만든다.
- 만든 JWT 를 브라우저에 보낸다.
- 브라우저는 JWT 를 가지고 서버에 데이터 요청을 한다.
- 서버는 서명을 확인하고 유저 정보를 클라이언트에게 제공한다.

<b>목적</b>

- 세션에 유저 정보를 유지하지 않고 <b>회원인증</b>이 가능해져서 stateless 한 서버를 만들 수 있다.
- 안정성 있는 <b>정보교류</b>

<b>저장방식</b>

- Session or Local
- Cookie

<b>단점</b>

- 사용자 계정을 비활성화하려면 토큰 만료를 기다려야 한다.
- 암호 변경이 이루어지더라도 사전에 인증받은 토큰이 만료되기 전까지 사용이 가능 하다.
- 토큰 만료 시 재인증 해야한다.
- '상태 비 저장' 측면을 위반하지 않고는 토큰을 파기할 수 없다.

### 링크

- [JWT란?](https://velog.io/@geunwoobaek/JWT%EB%9E%80)



## [HTTP] Cookie - 2

### 내용

쿠키는 유저들의 효율적이고 안전한 웹 사용을 보장하기 위하여 사용하는 기술이다. 웹사이트 접속 시 접속자의 개인장치에 다운로드 되고 브라우저에 저장된다. 쿠키는 만료일이 있으며, 자동으로 삭제되는 쿠키도 있고 수동으로 삭제할 수 있는 쿠키도 있다.

Connectionless, Stateless 한 HTTP 특성에 의해 상태 정보를 유지하지 않기 때문에 이를 보완하기 위해 나온 것이 쿠키다.

<b>쿠키의 구성</b>

- Name: 쿠키의 이름
- Value: 쿠키의 내용(일반적으로 raw 값을 노출시키지 않기 위해 보안 처리를 함)
- Domain: 쿠키가 전송될 호스트를 명시하고 명시되지 않았을 때는 현재 문서 위치의 호스트를 기본값으로 잡는다.
- Path: 쿠키 헤더를 전송하기 위해서 요청되는 URL 내에 반드시 존재해야하는 URL 이며, "/" 문자는 디렉티브 구분자로 구분한다.
- Expires / Max-Age: 만료기한을 걸어둔 쿠키의 쿠키 만료 시간을 제공한다.
- Size: 쿠키의 크기

<b>쿠키의 한계</b>

- XSS 공격
- 스니핑 공격
- 공용 PC 에서 쿠키값 유출
- 호스팅 공유 문제
- 쿠키의 개인정보 침해 위험

### 링크

- [쿠키란 무엇인가 / 쿠키를 사용하는 이유](https://kobrekim.com/footer-ko-kr/cookie-policy-ko-kr/what-are-cookies-and-why-we-use-them-ko-kr/)
- [Cookie란?](https://medium.com/@ddinggu/cookie%EB%9E%80-a650c6d2803e)

## [HTTP] Session - 2

### 내용

인증된 사용자의 식별자와 랜덤한 문자열로 세션 아이디를 만들어서 응답의 헤더로 넘겨준다.

세션이 만료되면 해커가 가져가더라도 유요하지 않은 정보가 된다. 세션이 탈취되면 서버에서 삭제하면 이용할 수 없게 된다. 보안상으로 좋다.

여러 개의 서버를 운영할 경우 서버당 하나의 세션을 가지고 있기 때문에 로드밸런스에 의해 다른 서버에 요청을 하게되면 해당 세션에 로그인 정보가 없을 경우 로그인이 실패하게 된다. 이 문제를 세션 스토리지를 통해 해결했다. 세션을 하나로 통합해서 관리하는 것이다. 단, 클라이언트의 로그인 요청이 몰리면 서버가 터진다. 어떤 방법을 쓰더라도 문제가 발생하는 이유는 통신을 할 때 사용하는 HTTP 와 서버가 지향하는 RestAPI 가 '무상태성'을 기초로 하기 때문이다. 패러다임의 충돌이 발생하는 것이다.

> 정보의 요청안에 사용자의 정보를 담아보자 -> 토큰

### 링크



## [Spring] Interceptor - 1

### 내용

특정 URI 로 요청시 controller 로 가는 요청을 가로채는 역할을 한다. 스프링에서 관리되며 스프링 내의 모든 객체에 접근이 가능하다.

인터셉터를 사용하지 않고 로그인 처리를 한다면 모든 요청마다 Controller 에서 Session 을 통해 로그인 정보가 남아 있는지를 확인 해야 한다. 인터셉터를 이용하면 요청에 대해서 Interceptor 를 먼저 수행해서 Session 에 로그인 정보가 있는지 확인해 주게되며, 중복 코드가 줄어들게 된다.

HandlerInterceptor 인터페이스와 HandlerInterceptorAdapter 추상 클래스가 지원된다.

### 링크

- [인터셉터(Interceptor)란? + 인터셉터를 이용한 로그인 처리](https://rongscodinghistory.tistory.com/2)

## [SPRING] @AuthenticationPrincipal - 4

### 내용

인증된 객체를 사용할 경우 사용하는 애너테이션이다.

- 검증용 애너테이션을 만든다.

  ```java
  @Target(ElementType.PARAMETER)
  @Retention(RetentionPolicy.RUNTIME)
  public @interface AuthenticationPrincipal {
      boolean required() default true;
  }
  ```

- 토큰 검증 기능을 구현한다.

  ```java
  public class AuthenticationPrincipalArgumentResolver implements HandlerMethodArgumentResolver {
      private AuthService authService;
  
      public AuthenticationPrincipalArgumentResolver(AuthService authService) {
          this.authService = authService;
      }
  
      @Override
      public boolean supportsParameter(MethodParameter parameter) {
          return parameter.hasParameterAnnotation(AuthenticationPrincipal.class);
      }
  
      @Override
      public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) {
          String token = AuthorizationExtractor.extract((HttpServletRequest) webRequest.getNativeRequest());
          return authService.findMemberByToken(token);
      }
  }
  ```

- Configuration 을 구현한다.

  ```java
  @Configuration
  public class AuthenticationPrincipalConfig implements WebMvcConfigurer {
      private final AuthService authService;
  
      public AuthenticationPrincipalConfig(AuthService authService) {
          this.authService = authService;
      }
  
      @Override
      public void addArgumentResolvers(List argumentResolvers) {
          argumentResolvers.add(createAuthenticationPrincipalArgumentResolver());
      }
  
      @Bean
      public AuthenticationPrincipalArgumentResolver createAuthenticationPrincipalArgumentResolver() {
          return new AuthenticationPrincipalArgumentResolver(authService);
      }
  }
  ```

### 링크

- [2단계 - 회원 관리 기능](https://techcourse.woowahan.com/s/U8wOamCU/ls/bEELCCPw)