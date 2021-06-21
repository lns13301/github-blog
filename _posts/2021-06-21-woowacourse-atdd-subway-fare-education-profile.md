---
layout: post
title: 우아한 테크 코스 level 2-4 지하철 요금 협업 미션 회고 및 학습 프로필
date: 2021-06-21 22:35:00 +0900
description: atdd subway 학습 프로필, 회고
img: woowacourse.png
tags: spring study
---

# 와일더의 학습 프로필 🃏

지하철 요금 미션을 하면서 새롭게 학습한 내용을 정리했습니다.

## [Spring] Dispatcher Servlet - 3

### 내용

Servlet Container 에서 HTTP 프로토콜을 통해 들어오는 모든 요청을 프레젠테이션 계층의 제일 앞에 두고 중앙집중식으로 처리해 주는 프론트 컨트롤러다.

서블릿을 사용하면 쉽게 웹 요청과 응답을 쉽게 사용할 수 있다. 그로인해 개발자들이 처리(비지니스)로직에 더 집중할 수 있다. Spring MVC Framework 의 유일한 Front Controller 이며 Spring MVC 의 핵심요소다. Spring MVC 의 웹요청 Life Cycle 을 주관한다고 할 수 있다.

### 링크

- [[Spring\]Dispatcher-Servlet이란?](https://mangkyu.tistory.com/18)
- [20180206 DispatcherServlet ? (디스패처 서블릿)](https://pasudo123.tistory.com/39)



## [Docs] Swagger - 3

### 내용

Swagger 란 API 가 가지고 있는 스펙을 명세, 관리할 수 있는 프로젝트다. API 를 테스트 해볼 수 있는 화면을 제공하며, 동작을 테스트하는 용도에 특화되어 있다. 제품코드에 애너테이션을 추가해야한다. 제품코드와 동기화가 안되는 상황이 발생할 수 있다.

협업하거나 만들어진 프로젝트에 대해 유지보수를 할때, 구축되어진 API 서버가 어떤 스펙을 가지고 있는지 파악해야한다. 이러한 스펙을 직접 문서로 작성하게 되면 API 가 변경될 때마다 문서를 수정해야한다. 그래서 유지보수성의 향상을 위해 문서 자동화를 해주는 Swagger 를 사용한다.

제품코드에 애너테이션을 추가해야 하는점과 제품 코드와 동기화가 되지 않을 수도 있는 단점이 있으니 주의해야 한다.

- 의존성 추가 (Gradle)

  ```java
  	implementation 'io.springfox:springfox-swagger2:2.9.2'
  	implementation 'io.springfox:springfox-swagger-ui:2.9.2'
  ```

- 코드 적용

  ```java
  @Configuration
  @EnableSwagger2
  public class SwaggerConfig {
      @Bean
      public Docket api() {
          return new Docket(DocumentationType.SWAGGER_2)
                  .select()
                  .apis(RequestHandlerSelectors.basePackage("wooteco.subway")) // 기준 패키지
                  .paths(PathSelectors.ant("/**")) // api 에 띄울 url 형식
                  .build();
      }
  }
  ```

### 링크

- [Swagger 프로젝트 적용과 어노테이션](https://velog.io/@changyeonyoo/Swagger-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%A0%81%EC%9A%A9%EA%B3%BC-%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98)



## [Docs] Rest Docs - 2

### 내용

JUnit 버전별로 RestDocs 를 사용하기 위한 방법이 다르므로 [공식 문서](https://docs.spring.io/spring-restdocs/docs/current/reference/html5/)에서 찾아보는 것이 좋다. MockMvc, WebTestClient, REST Assured 에 대한 문서화를 제공한다.

<b>장점</b>

- 깔끔 명료한 문서를 만들 수 있기 때문에 문서 제공용으로 사용하기 좋다.
- 라이브러리를 추가하지 않아도 된다.
- 컴파일 시점에서 오류를 잡기 쉽다.
- 제품 코드에 영향이 없다.
- 테스트가 성공해야 문서가 작성된다.

<b>단점</b>

- 동적 테스트 작성이 불편하다.
- 적용하기 어렵다.

### 링크

- [Spring Rest Docs 적용](https://woowabros.github.io/experience/2018/12/28/spring-rest-docs.html)
- [공식 문서](https://docs.spring.io/spring-restdocs/docs/current/reference/html5/)



## [Docker] 도커 - 5

### 내용

<b>Docker Engine</b> 은 애플리케이션을 빌드하고 컨테이너화 하기 위한 오픈 소스 컨테이너화 기술이다. Docker Engine 은 다음을 사용하여 클라이언트~서버 애플리케이션으로 작동한다.

- 오래 실행되는 데몬 프로세스가 있는 서버
- 프로그램이 Docker 데몬과 통신하고 지시하는데 사용할 수 있는 인터페이스를 지정하는 API 다.
- CLI 클라이언트 도커

<b>Docker Hub</b> 는 컨테이너 이미지를 찾고 팀과 공유하기 위해 Docker 에서 제공하는 서비스다. 컨테이너 커뮤니티 개발자, 오픈 소스 프로젝트 및 ISV(Independent Software Vendor) 를 포함한 다양한 콘텐츠 소스가 포함된 세계 최대의 컨테이너 이미지 저장소다.

애플리케이션이 배포되는 환경을 서버에 직접 설치하여 구성할 경우, [snowflake server issue](https://bcho.tistory.com/1224) 에 직면한다. 서비스가 점차 확장되면서 기존에 사용하던 서버와 새로 구축할 서버간의 설정차이가 발생하고, 관리자의 인적요소에도 영향을 받을 수 있다. 그리하여 기존에는 스크립트를 활용한 자동화 방식부터, kickstart 등을 거쳐 OS 를 가상화 방식까지 다양한 형태로 서버를 관리해 왔다.

### Docker(Container) vs VM (vm)

vm은 가상화된 하드웨어(커널) 위에 os 가 올라가는 형태로 거의 완벽하게 host 와 분리가 된다고 볼 수 있다.

Container 는 os 가상화다. os 부분을 가상화해서 올리고 커널을 host와 공유한다. vm 보다 얕은 격리가 된다.

<b>vm 의 장점</b>

- host 를 공유하지 않기 때문에 보안에서 컨테이너보다 더 강하다.
- 하드웨어 가상화를 통해 완벽히 가상화된 os 를 올릴 수 있기 때문에 os 선택이 자유롭다. (Linux 머신에서 Windows 서버를 올릴 수 있다.)

<b>container 의 장점</b>

- host 머신보다는 느리지만 vm 보다 빠르다. vm 은 vm 이 처리한 io 를 결국 host os 의 커널이 다시 받아 자신의 드라이버에 맞게 처리하는 과정에서 병목이 발생한다.
- 도커 환경에서는 개발 환경에서 운영 환경 image 를 만들어 registry 에 배포하고 기존 것과 새로운 것을 교체하기만 하면 바로 배포가 가능하기 때문에 라프이 사이클이 뛰어나다.
- 도커는 원격 저장소를 통해 관리하면서 깃처럼 사용할 수 있어서 쉽게 공유하고 필요할 때 언제 어디서나 컨테이너로 실행 가능하다.

<b>도커의 주요 기능 중 일부</b>

- 모든 클라우드 플랫폼에서 여러 언어 및 프레임 워크로 모든 애플리케이션을 컨테이너화하고 공유할 수 있는 기능
- 완전한 Docker 개발 환경의 간편한 설치 및 설정
- 최신 버전의 Kubernetes 포함
- 최신 상태를 유지하고 보안을 유지하기 위한 자동 업데이트
- Windows 에서 애플리케이션을 빌드하기 위해 Linux 와 windows Server 환경간에 전환하는 기능
- 기본 Windows Hyper-V 가상화를 통한 빠르고 안정적인 성능
- Windows 컴퓨터에서 WSL 2를 통해 Linux 에서 기본적으로 작업할 수 있는 기능
- 파일 변경 알림 및 로컬 호스트 네트워크에서 실행중인 컨테이너에 대한 쉬운 액세스를 포함하여 코드 및 데이터에 대한 볼륨 마운팅
- 지원되는 IDE 를 사용한 컨테이너 내 개발 및 디버깅

<b>도커의 장점</b>

- docker 파일에서 설정한 정보로 버츄얼 컨테이너를 만들기 때문에, 환경에 영향을 받지 않는다.
- 트래픽이 많아지면 도커 컨테이너를 늘려서 서버를 증축하고, 트래픽이 줄어들면 도커 컨테이너를 줄여서 서버를 줄일 수 있다.
- 하나의 서버에서 각기 다른 환경을 설정할 수 있다.
- 컨테이너가 각각 독립되어 있어서 영향을 미치지 않는다.

<b>도커 설치하기</b>

```
$ sudo apt-get update && \
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common && \
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && \
sudo apt-key fingerprint 0EBFCD88 && \
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" && \
sudo apt-get update && \
sudo apt-get install -y docker-ce && \
sudo usermod -aG docker ubuntu && \
sudo curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose && \
sudo chmod +x /usr/local/bin/docker-compose && \
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

도커 컨테이너도 프로세스다.

<b>도커 실행하기</b>

```
docker-compose up -d
```

<b>도커 정지하기</b>

```
docker stop $(docker container ls -q)
```

<b>도커 삭제하기</b>

```
docker-compose rm -v -f
sudo rm -rf db
```

<b>도커 실행중인 컨테이너 확인하기</b>

```
$ docker ps
```

<b>도커 실행중인 컨테이너 종료하기</b>

```
$ docker kill [docker 컨테이너 hash_id]
```

<b>도커 Mysql DB 접속하기</b>

```
~$ docker exec -it local-db bash
:/# mysql -uroot -p
```

<b>도커 이미지</b>

> 이미지는 파일들의 집합이고, 컨테이너는 이 파일들의 집합 위에서 실행된 특별한 프로세스

프로세스의 데이터가 변경되더라도 원본 프로그램 이미지를 변경할 수 없듯, 컨테이너의 데이터가 변경되더라도 컨테이너 이미지의 내용을 변경할 수 없다.

```
// 도커 이미지는 [저장소 이름]/[이미지 이름]:[태그]의 형태로 구성된다.
## Official 이미지는 [저장소 이름]을 붙여주지 않아도 된다.
$ docker pull ubuntu:lastest
```

### 링크

- [Nextstep - 도커 컨테이너 이해하기](https://techcourse.woowahan.com/s/U8wOamCU/ls/sUKqMwuQ)
- [Nextstep - 배포하기](https://techcourse.woowahan.com/s/U8wOamCU/ls/rOuQtU1c)
- [공식 문서](https://docs.docker.com/desktop/)
- [Docker와 VM](https://medium.com/@darkrasid/docker%EC%99%80-vm-d95d60e56fdd)
- [도커(Docker) 입문편](https://www.44bits.io/ko/post/easy-deploy-with-docker)

