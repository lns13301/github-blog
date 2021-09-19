---
layout: post
title: 우아한테크코스 level 3 팀 프로젝트 회고 및 학습 프로필
date: 2021-09-19 21:10:00 +0900
description: babble 학습 프로필, 회고
img: woowacourse.png
tags: spring study
---

# 와일더의 학습 프로필 🃏

레벨 3 팀 프로젝트를 진행하면서 학습한 내용입니다.

## [Spring] WebSocket - 5

### 내용

우리팀 프로젝트는 매칭 기반 서비스를 제공하기 때문에 핵심기능에 채팅이 뽑혔다. 하지만 HTTP 는 채팅을 구현함에 있어서 명확한 한계가 보였다.

1. 무상태성(Stateless) : 클라이언트의 상태를 기억하지 않기 때문에 클라이언트에게 갱신된 정보를 알려줄 수 없다.
2. 비연결성(Connectionless) : 1개의 요청으로부터 응답하면 연결이 끊어진다.

### HTTP/1.1

HTTP/1.1 부터는 이미 연결되어 있는 TCP 연결을 재사용하는 Keep-Alive 기능을 Default 지원한다. Handshake 과정이 생략되므로 성능 향상 및 시간 단축을 기대할 수 있다. 정의된 시간 내에 Access 가 이루어진다면 연결된 상태를 계속 유지할 수 있다. 정의된 시간이 종료되면 세션 연결이 끊긴다. 서버 자원은 무한정하지 않기 때문에 Keep Alive Timeout 을 설정한다. 단, 모든 요청마다 연결을 유지해야 하기 때문에 대량 접속 시 큰 성능 저하가 발생할 수 있다.

### 해결책

1. Polling : 무식한 방법으로, 일정 시간마다 계속해서 클라이언트가 서버에게 Request 를 보내는 방식, 실시간 적용이 안됨, 보낼 데이터가 없어도 계속해서 데이터를 줘야해서 리소스 낭비
2. Long Polling(HTTP/1.1) : Polling 과 비슷하지만 일단 Request 를 보내고 time out 전까지 무한정 기다리는 방식, 실시간 통신이 가능하지만 단기간에 활발한 데이터 전송이 이루어진다면 Polling 방식보다 훨씬 많은 데이터를 보내게 된다.
3. HTTP Streaming(HTTP/1.1) : 요청에 대한 응답이 완료하지 않은 상태에서 데이터를 Response 하는 방식이다. 빈번하게 서버의 변화가 생길 경우 유리한 방식이다. 오랫동안 연결상태를 지속할 경우 유효성 관리 등의 부담이 발생한다. 클라이언트가 서버로 데이터를 송신하기 어렵다.
4. WebSocket(HTTP/1.1) : 양방향 통신(데이터 송수신을 동시에 처리할 수 있는 통신 방법)이다. 여러 단말 기기의 웹 환경에서 연속된 데이터를  빠르게 교환할 수 있다.

그중에서 HTTP의 근본적인 문제를 해결하기 위해 만들어진 프로토콜인 WebSocket 을 채택했다.

<b>장점</b>

- HTTP request 메세지를 변형없이 사용할 수 있기 때문에 80, 443 포트에서 동작 가능하다.
- 일반 TCP 와 다르게 HTTP 프로토콜을 사용하기 때문에 추가적인 방화벽 설정이 필요없다.
- 클라이언트의 상태를 기억할 수 있다.
- 일반 HTTP 통신과정에서 발생하는 HTTP 와 TCP 연결에 필요한 비용을 줄일 수 있다.
- 불필요한 헤더가 사리지기 때문에 통신량을 줄일 수 있다.
- 웹 표준이다.

<b>단점</b>

- 오래된 브라우저는 지원하지 않는다.
- HTML5 이전의 기술로 구현된 서비스에서는 Socket.io, SockJS 를 사용해야 한다. (실시간 통신을 도와주는 기술)
- 서버와 클라이언트 간의 소켓 연결 자체가 지원을 소비하기 때문에 트래픽이 많은 서버 같은 경우 CPU 에 부담이 될 수 있다.
- 클라이언트의 상태를 기억하는 추가적인 구현이 필요하다. (웹 소켓은 문자열을 주고 받기만 해줄 뿐)

### STOMP(Simple Text Oriented Messaging Protocol)

웹 소켓은 통신 프로토콜이기 때문에 특정 subscriber(user) 에게만 메세지를 보내는 방법을 담당하는 Stomp(Spring 에서 제공)를 사용했다. Session 을 직접 관리할 필요가 없고 직렬화, 역직렬화를 제공해줘서 반환값을 쉽게 DTO 로 변환할 수 있었다. 메세지를 라우팅할 경로를 쉽게 지정할 수 있다.

### 통신 과정

서버 측에서 socket( ) -> bind( ) -> listen( ) 과정을 진행하면 클라이언트 측에서 socket( ) -> connect( ) 요청이 이루어지면 서버측에서 accept( ) 하게되고 이어서 서버와 클라이언트간에 send( ), recv( ) 가 이루어진다. 그리고 연결을 종료할 때 클라이언트 측에서 close( ) 과정을 통해 연결을 해제한다.

- socket( )
  - 소켓의 옵션을 지정하고 생산한다.
  - 연결 대상에 대한 정보가 포함되지 않기 때문에 <b>재사용</b>할 수 있다.
- bind( )
  - 어떤 Port 번호에 지정될 지 결정한다.
  - 사용할 Port 번호를 운영체제에게 바인딩 요청을 하고, 이미 사용중인 Port 번호라면 에러를 반환받는 과정이다.
- listen( )
  - 클라이언트의 요청을 기다리는 상태가 된다.
  - 오로지 연결 <b>성공/실패(자의적인 소켓 close 포함)로만</b> 이어진다.
  - 연결 성공하더라도 연결 대상의 정보는 가지지 않는다. 처리를 위한 대기 큐에 연결 대상의 정보를 기록만 한다.
- accept( )
  - 대기 큐에 쌓여있는 연결 대상의 정보를 꺼내온다.
  - 연결 대상의 정보를 접수해서 연결을 진행한다.
  - 앞서 진행한 socket-bind-listen 과정을 수행한 소켓을 연결하는 것이 아닌 <b>새로운 자식 소켓을 만들어서 연결</b>한다.
  - 서버 소켓은 전체 통신(socket-bind-listen)을 관장하는 서버 소켓 1개와 실제 연결 대상광 연결 및 소통을 진행하는 N개의 소켓으로 이루어진다. 이 소켓 정보는 디스크립터 테이블에 기록된다.

### Socket vs WebSocket

소켓은 애플리케이션 계층과 전송 계층 사이의 의사소통을 돕는 개념이고, 웹소켓은 TCP/IP 를 통해 실행되는 HTTP 와 유사한 프로토콜을 통해 Application Server 에 연결하는 브라우저에서 실행된다. TCP 소켓에서는 바이트 스트림을 사용하지만 웹소켓에서는 UTF-8 포맷의 스트림만 허용한다.

### 링크

- [소켓 기본 개념 정리](https://babble-dev.tistory.com/4)
- [WebSocket 시작하기](https://babble-dev.tistory.com/2)
- [Spring WebSocket 구현](https://unluckyjung.github.io/spring/2021/08/19/Spring-Websocket-Implement-1/#)
- [HTTP Keep Alive 알아보기](https://goodgid.github.io/HTTP-Keep-Alive/)



## [CI/CD] Github Actions - 5

### 내용

빌드 자동화 및 배포 자동화 기능을 구현하기로 했다. 빌드 자동화는 프로젝트 요구사항이기도 하고 기왕 하는거 배포 자동화까지 하는게 좋지 않냐는 의견이 나와 진행했다.

### CI(Continuous Integration)

지속적인 통합이라는 의미로 새로운 코드 변경 사항이 정기적으로 빌드 및 테스트되어 공유 레포지토리에 통합되므로 여러 명의 개발자가 동시에 애플리케이션 개발과 관련된 코드 작업을 할 경우 발생할 수 있는 충돌 문제를 해결할 수 있다.

### CD(Continuous Deployment)

지속적인 서비스 제공(Delivery) 혹은 배포(Deployment)를 의미하며 두 용어는 상호 교환적으로 사용된다. 두 가지 의미 모두 파이프라인의 추가 단계에 대한 자동화를 뜻하지만 때로는 얼마나 많은 자동화가 이루어지고 있는지를 설명하기 위해 별도로 사용되기도 한다.

애플리케이션에 적용한 변경 사항이 버그 테스트를 거친 후 레포지토리에 자동으로 업로드 되는 것을 뜻하며 운영팀은 이 레포지토리에서 애플리케이션을 실시간 프로덕션 환경으로 배포할 수 있다.

### CI 툴 선택

1. Travis : Github 연동, YML 파일을 통한 쉬운 설정, 독립적인 job, 다양한 레퍼런스, 서버를 대신 운영해주는 등의 장점이 있다.

2. Jenkins : 직접 호스팅을 해야하기 때문에 모든 부분을 관리해야함, 많은 사용자들과 레퍼런스 및 플러그인이 존재하는데 이는 플러그인의 지옥으로 연결된다. 규모가 작은 프로젝트도 설정해야할 부분이 많기 때문에 리소스 낭비가 커질 수 있다.

3. Gitlab : UI 가 쉽고 간편하며 GitLab 과 연동이 쉽다. 모든 job 은 독립적이며 Runner 가 Docker(친화적) 기반이라는 장점이 있다. 모든 job 에 대해 artifact 를 정의하고 업로드 다운로드를 해야하는 번거로움이 있으며 제공되는 플러그인 종류가 적는 단점이 있다.

4. Github Actions

   우리팀은 CI/CD 를 경험해본 사람이 없기 때문에 일반적으로 많이 사용하는 젠킨스(플러그인 지옥)를 사용하는 것에 대한 의견도 나왔지만, 완만한 학습곡선과 익숙한 Github UI 활용이라는 점 때문에 Github Actions 를 최종 선택했다. GitLab 과 비교했을 때 아직은 다양한 문제점이 있었지만 우리가 당장 겪을 문제는 아니라고 생각했고, 직접 문제점을 겪어 보는 것도 좋은 경험이 될 것이라고 판단했다.

### 문제점(Github Actions)

- 저장소가 동일 조직 내에 있는 경우에도 다른 개인 저장소에서 작업에 액세스 할 수 없다. (CI)
- UI에서 개별 워크플로우 실행을 삭제할 수 없다. (CI)
- 워크 플로우에서 단일 작업만 다시 실행할 수 없다. (CD)
- 스테이징 및 프로덕션 환경에 대해 서로 다른 워크플로우를 쉽게 트리할 수 없다. (CD)
- 저장소의 GITHUB_TOKEN 을 사용하여 다른 워크 플로우에서 새 워크 플로우를 트리할 수 없다. (CD)
- YAML 앵커 및 별칭에 대한 지원이 부족하다. (CD)

### 사용법

처음 CI/CD 를 접하는 상황에서 Github Actions 를 선택한 점은 상당히 좋았다.

- Github 레포지토리에서 Actions 탭에 들어간다.
- New workflow 를 선택한다.
- 빌드 환경에 맞는 yml 양식을 선택한다. (Java with Gradle)
- Set up this workflow 를 선택한다.
- 양식에 맞게 `[FILE_NAME].yml`을 작성한다.
- 레포지토리의 `.github/workflows/ 경로에 파일이 생성되고 최초 CI 가 이루어진다.

Github Actions 측에서 제공하는 서버로 빌드/배포 과정을 진행하는 방식으로는 완성된 빌드 파일을 보낼 수 있는 방법이 없었다. 등록되지 않은 외부 IP 를 통해 AWS EC2 SSH 접근이 불가능한 상황이기 때문이다. 제약사항을 풀 수 없는 상황이라 이 부분을 쉽게 해결할 수 없었다. 그래서 github actions 를 self-hosted runner(<b>사용자가 보유한 컴퓨팅 자원으로 빌드를 수행할 수 있는 행위</b>) 방식을 활용해서 해결했다. 별도로 생성한 빌드서버에서 운영 서버로 ssh 연결이 가능하도록 구상했다.

CI/CD 용 EC2 인스턴스(Public IP)를 별도로 생성하고 이곳에 self-hosted runner 가 CI/CD 작업중 저장소의 Actions 탭으로 진행상황과 결과를 지속적으로 전달하고 저장소 Actions 탭에서 CI/CD 작업을 직접 요청할 수도 있기 때문에 인터넷 연결이 필요하다.

### Self-hosted runner 설치 및 연결

- Github 레포지토리의 Settings 에 들어간다.
- 화면 좌측의 메뉴중에서 Actions -> Runners 를 선택한다.
- `Add Runner`를 선택해서 Runner 를 추가한다.
- Self-hosted runner 가 동작할 운영환경을 설정하고 해당 운영환경의 터미널로 접속 및 actions runner 압축 파일을 다운로드 후 압축을 해제한다.
- CI/CD 가 진행되기 원하는 저장소 쪽에서 발급된 토큰 등록을 통해서 actions runner 와 저장소간 연결을 진행한다.
- Additional labels 설정을 통해 Runner 의 추가 라벨을 설정한다. runner 를 식별하기 위해 이름대신 사용하므로 신중하게 지정한다.
- 연결이 성공적으로 완료되면 `./run.sh` 명령어 입력시 actions runner 가 실행되고 CI/CD 작업 요청을 기다리는 대기상태로 진행된 것을 확인할 수 있다. (`nohup ./run.sh &` 의 명령어로 항상 runner 가 동작하도록 설정한다.)

### 양식 예시

```yml
name: Backend test release CI with gradle

on:
  push:
    branches: [ release ] // 해당 브랜치에서 `push`가 발생할 때 Github Actions 가 작업을 수행한다.
    paths:
      - 'back/**'
// 모든 jobs 는 독립적이기 때문에(needs 로 의존성을 부여한다 하더라도) sonarqube(빌드한 파일에 대한 테스트 검증이 필요하기 때문에) 와 빌드를 한 job 에서 수행해야 했다.
jobs:
  deploy-build:
    runs-on: deploy // self-hosted runner 에 기입했던 라벨을 입력해줘야 해당 runner 를 식별하고 작업을 진행한다. 그렇지 않으면 default runner(Github Actions 측에서 제공) 가 작업을 수행한다.

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 11
      uses: actions/setup-java@v2
      with:
        java-version: '11'
        distribution: 'adopt'

    - name: gradlew 권한 변경
      working-directory: ./back/babble
      run: chmod +x gradlew

    - name: 빌드 진행
      working-directory: ./back/babble
      run: ./gradlew build

    - name: 소나큐브 빌드 진행
      working-directory: ./back/babble
      run: ./gradlew sonarqube -Dsonar.projectKey=babble-sonarqube -Dsonar.host.url=${{ secrets.SONARQUBE_SERVER_URL }} -Dsonar.login=${{ secrets.SONARQUBE_TOKEN }}

    - name: SCP 명령을 이용해 테스트용 서버로 jar 파일 전송
      working-directory: ./back/babble
      run: scp ./build/libs/babble-0.0.1-SNAPSHOT.jar test:/home/ubuntu/was

    - name: SSH 명령을 통해 테스트용 서버 접속 후 jar 파일 실행
      run: ssh test "cd was; /home/ubuntu/was/deploy.sh;"
```



## 링크



### 링크

- [CI/CD(지속적 통합/지속적 제공): 개념, 방법, 장점, 구현 과정](https://www.redhat.com/ko/topics/devops/what-is-ci-cd)
- [Github actions self-hosted runner를 EC2에서 동작시키기](https://www.notion.so/Github-actions-self-hosted-runner-EC2-385c02b6aaea441a9543efe43915343c)
- [[CI/CD] Jenkins vs GitLabCI vs Travis](https://owin2828.github.io/devlog/2020/01/09/cicd-1.html)
- [GitLab CI/CD vs GitHub Action 비교하기](https://insight.infograb.net/blog/2021/05/31/gitlab-ci-vs-github-action/)



## [Spring] Logback Logging - 5

### 내용

### 로깅을 하는 이유?

로깅이란 시스템이 동작할 때 시스템의 상태 및 동작 정보를 시간 경과에 따라 기록하는 것을 의미한다. 로깅을 통해 개발자는 개발 과정 혹은 개발 후에 발생할 수 있는 예상치 못한 애플리케이션의 문제를 진달할 수 있고, 다양한 정보를 수집할 수 있다. 사용자 로그의 경우 분석 데이터로 활용할 수 있다. 하지만 로깅을 하는 단계에서 적절한 수준의 로그 기록 기준을 잡지 못하면 방대한 양의 로그 파일이 생성되는 문제를 겪거나, 의미 있는 로그를 쌓지 못하는 경우가 발생할 수 있다. 결국 **효율적으로 로깅을 하는 방법을 이해하는 것**이 중요하다.

### 스프링에서 로깅을 하는 방법

초기의 스프링은 JCL(Jakarta Commons Logging)을 사용해서 로깅을 구현했다. 요즘에는 대표적으로 **Log4j** 와 **Logback** 으로 스프링 부트의 로그 구현체를 사용한다. Log4j는 가장 오래된 프레임워크이며 Apache 의 Java 기반 Logging Framework 다. xml, properties 파일로 로깅 환경을 구성하고, 콘솔 및 파일 출력의 형태로 로깅을 할 수 있게 도와준다. 로그 레벨의 경우는 6단계로 구성되어있다. Logback 은 log4j 이후에 출시된 Java 기반 Logging Framework 중 하나로 가장 널리 사용되고 있다. SLF4j 의 구현체이며 Spring Boot 환경이라면 별도의 dependency 추가 없이 기본적으로 포함되어 있다.

### Logging 구현체 선택하기

JCL 을 사용하면 기본적인 인터페이스인 Log 와 Log 객체 생성을 담당하는 LogFactory 만 구현하면 언제든지 로깅 구현체 교체가 가능하니 선택은 자유롭다. 이번 프로젝트를 진행할 때는 SLF4J 의 구현체 Logback 을 사용해서 로깅을 구현했다. SLF4J 는 JCL 의 가진 문제를 해결하기 위해 클래스 로더 대신에 컴파일 시점에서 구현체를 선택하도록 변경시키기 위해 도입된 것이다. Logback 은 log4j 에 비해 향상된 필터링 정책, 기능, 로그 레벨 변경 등에 대해 서버를 재시작할 필요 없이 자동 리로딩을 지원한다는 장점이 있다. 일반적으로 많이 사용되고 있기 때문에 팀원간 회의에서 Logback 이 채택되었다.

### 링크

- [Logback 으로 쉽고 편리하게 로그 관리를 해볼까요? ⚙️](https://woowacourse.github.io/tecoble/post/2021-08-07-logback-tutorial/)



## [AWS] Cloudwatch - 5

### 내용

이전 데모데이에서 로그 파일을 만들었다. 하지만 이 로그파일을 조회하기 위해서는 EC2 인스턴스의 로그 파일 경로에 접근해서 파일을 열어보는 방법밖에 없었다. 이 상황을 개선할 방안으로 AWS Cloudwatch 를 통한 지표 및 로그 수집기능을 활용했다.



### 링크

- [Cloudwatch 적용하기](https://www.notion.so/Web-server-CloudWatch-6967f622661a43d6bb16ea7eb88fd14c)



## [ELK] Elasticsearch Logstash Kibana - 2

로깅을 통해 개발자는 개발 과정 혹은 개발 후에 발생할 수 있는 예상치 못한 애플리케이션의 문제를 진달할 수 있고, 다양한 정보를 수집할 수 있다. 이렇듯 중요한 로깅을 어떤 로깅 수집 기술을 통해 확인하면 좋을지 생각해보자

### ELK?

분석 및 저장 기능을 담당하는 ElasticSearch, 수집 기능을 하는 Logstash, 이를 시각화하는 도구인 Kibana 의 앞글자를 딴 단어다. 접근성과 용이성이 좋아 최근에 많이 사용되는 Log 및 데이터 분석 도구다.

### Elasticsearch (로그 저장 및 검색)

텍스트, 숫자, 위치 기반 정보, 정형 및 비정형 데이터 등 모든 유형의 데이터를 위한 무료 검색 및 분석 엔진이다. 분산형과 개방형을 특징으로 한다. Elastic Stack 의 핵심 구성 요소다. Elasticsearch 로 흘러들어온 원시 데이터를 서로 관련있는 문서끼리 색인하여 복잡한 쿼리에 대해 요약 <b>검색</b>을 할 수 있도록 제공한다. 관계형 데이터베이스가 document 중심이라면 Elasticsearch 는 텍스트 중심이라고 볼 수 있다. 관계형 데이터베이스를 베이스로 검색에 필요한 부분을 Elasticsearch 로 진행하는 것이 보통이다.

### Logstash (로그 수집 엔진)

실시간 파이프라인 기능을 가진 오픈소스 데이터 수집 엔진이다. 서로 다른 소스의 데이터를 탄력적으로 통합하고 사용자가 선택한 목적지로 데이터를 정규화할 수 있다. 수평 확장이 가능한 데이터 처리 파이프라인을 다양한 입력, 필터, 출력을 믹스매치하고 조정하면서 파이프라인에서 조화롭게 운용 가능하다.

### Kibana (로그 시각화 및 관리)

ElasticSearch 에서 색인된 데이터를 검색하고 <b>시각화 하는 기능</b>을 제공하는 Elastic Stack 을 기반으로 구축된 프론트엔드 애플리케이션이다. 

### Filebeat

로그 데이터를 전달하고 중앙화하기 위한 경량의 Producer 다. 서버에 에이전트로 설치되며, 지정한 로그 파일 또는 위치를 모니터링하고 로그 이벤트를 수집한 다음 인덱싱을 위해 Elasticsearch 또는 Logstash 로 전달한다.

### 장점

- Elastic 회사에서 제공하는 <b>오픈 소스</b>다.
- ELK 자체로는 무료로 사용가능하다.
- 오픈소스를 설치하는 것만으로 구축이 완료된다.
- 추가적인 개발 과정없고, 사용이 쉬워 접근성이 뛰어나다.
- 실시간에 가깝게 데이터를 처리할 수 있다.
- E, L, K 에 해당하는 세 가지 모듈을 각각 해당하는 기능만 담당할 수 있다면 얼마든지 모듈교체가 가능하다.

ELK 가 어떤 것이고 어떤 방식으로 일련의 과정이 처리되는지 간단하게 살펴봤다.

### ELK 적용

실습에 사용된 PC 환경은 AWS 의 Ubuntu 기반의 EC2 인스턴스이며 Java 8 이 설치되어 있는 환경에서 작업을 진행한다.

1. 아래 네 가지 파일을 설치한다. ([다운로드 페이지](https://www.elastic.co/kr/downloads/))
   - [Elasticsearch]([DEB X86_64](https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.14.0-amd64.deb))
   - [Logstash](https://artifacts.elastic.co/downloads/logstash/logstash-7.14.0-amd64.deb)
   - [Kibana](https://artifacts.elastic.co/downloads/kibana/kibana-7.14.0-amd64.deb)
   - [https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.14.0-amd64.deb](https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.14.0-amd64.deb)



### 링크

- [Elasticsearch 간단 개념](https://sudarlife.tistory.com/entry/Elasticsearch-%EA%B0%84%EB%8B%A8-%EA%B0%9C%EB%85%90-%EC%9E%A5%EB%8B%A8)
- [Elasticsearch란 무엇인가요?](https://www.elastic.co/kr/what-is/elasticsearch)
- [Logstash 소개](https://elastic.co/guide/kr/logstash/current/introduction.html)
- [Kibana는 무엇인가요?](https://www.elastic.co/kr/what-is/kibana)
- [ELK(Elasticsearch, Logstash, Kibana) 란? ELK Stack 이란?](https://captcha.tistory.com/44)
- [[ELK] ELK Stack 이란? 소개, 정의](https://velog.io/@holidenty/ELK-ELK-Stack-%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C)
- [ELK - Filebeat 란?](https://enterone.tistory.com/368)
- [MSA 와 Log - 중앙 집중식 로깅 ELK stack 편](https://bravenamme.github.io/2021/01/28/elk-stack/)



## [AWS] Cloudfront & S3 - 5

### 내용



### 링크

- [Amazon EC2 란 무엇입니까?](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/concepts.html)



## [AWS] Docker - 4

### 내용

클라이언트로부터 요청을 받았을 때 요청에 맞는 정적 파일을 응답해주는 HTTP Web Server 로 활용되기도 하고, Reverse Proxy Server 로 활용하여 WAS 서버의 부하를 줄일 수 있는 로드 밸런서로 활용되기도 하는 경량 웹 서버다.

Event-Driven 구조로 동작하기 때문에 한 개 또는 고정된 프로세스만 생성하여 사용하고, 비동기 방식으로 요청들을 Concurrency 하게 처리할 수 있다. Nginx 는 새로운 요청이 들어오더라도 새로운 프로세스와 쓰레드를 생성하지 않기 때문에 프로세스와 쓰레드 생성 비용이 존재하지 않고, 적은 자원으로도 효율적인 운용이 가능하다. 이러한 장점 덕에 단일 서버에서도 동시에 많은 연결을 처리할 수 있다.

### 링크

- [Nginx란 무엇인가?](https://velog.io/@wijihoon123/Nginx%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)



## [DB] MariaDB - 4



## [WS] Nginx - 4



## 프로젝트 회고

프로젝트를 하고 느낀 부분도 하나의 학습 경험이라고 생각한다.

레벨 2까지는 새로 접해보는 다양한 기술 스택에 대해 겉햝기식 학습을 했다. 그리고 레벨 3을 맞이했다. 프로젝트에서 사용할 기술 스택을 정하는 과정에서 타당한 이유를 찾는 것이 아닌, <b>남들이 많이 쓴다더라</b>, <b>이게 좋다더라</b> 혹은 <b>아는 지식 내에서</b> 생각을 하게 되었고, 시간이 흐른 뒤에 수동적 선택을 했던 아쉬움이 남는다. 물론 프로젝트를 진행하는 과정에서 초기에 정한 기술 스택이나 설정을 변경하는 과정에서 새로운 지식을 습득하고 경험할 수 있었다. 이는 산발적 지식학습에서 국소적 지식학습을 하는 경험이 되었다.

공식 문서를 참고하여 사고를 확장해 나가는 것이 아닌, 검색에만 의존하던 자신을 발견할 수 있었다. 특히, 프로젝트에서 WebSocket 을 적용하기 위해 실습을 진행했는데, 공식문서 외의 기능을 적용하거나 테스트 코드를 작성할 때 벽을 느꼈다. 그리고 이 문제를 막연히 검색을  통해 해결하려고만 했다. 검색으로 마땅한 해결책을 찾을 수 없게 되자 포기해버리는 상황이 나왔다. 하지만, 다른 팀원들은 이 난관에서 짜증을 내고 욕을 해도 시간이 흘러 결국 스스로의 힘으로 해결하는 모습을 보여주었다. 크게 표현은 하지 않았지만, 나에게 굉장한 충격을 주었다. 문제를 해결한 팀원의 모습이 대단해 보인 점과 쉽게 포기해버리는 내 모습에 말이다. 이 사건을 계기로 좀 더 끈질기게 물고 늘어져보자라는 마음가짐을 갖게 되었다.

## [Docs] RestDocs - 5

### 내용

레벨 2 협업미션에서는 Swagger 를 사용하여 문서 자동화를 해봤었다. 그래서 이번 프로젝트를 진행할 때 RestDocs 를 통해 문서화를 해보고 싶은 욕구가 강했다. 다행히도 다른 팀원도 대부분 Swagger 경험이 있었기 때문에 RestDocs 문서화를 해보고 싶어했다. 또한 RestDocs 의 장점 중 하나인 테스트를 기반으로 문서화가 진행되는 부분에 있어서 정밀한 테스트를 통해 프로덕션을 제공할 수 있을 것이라는 기대감 때문에 RestDocs 가 끌렸다.

### 링크



## [Git] 깃 브랜치 전략 - 5

### 내용

- master : 제품으로 <b>출시</b>될 수 있 브랜치
- develop : <b>다음</b> 출시 <b>버전</b>을 <b>개발</b>하는 브랜치
- feature : <b>기능을 개발</b>하는 브랜치
- release : 이번 <b>출시 버전을 준비</b>하는 브랜치
- hotfix : <b>출시 버전</b>에서 발생한 <b>버그를 수정</b>하는 브랜치

### 트러블 슈팅

1. Git-flow 기반으로 우리 프로젝트에 맞게 변형하자!
2. 현재 개발 단계에서는 QA 단계가 없기 때문에 굳이 release 브랜치의 필요성을 못느낀다.
3. <b>"로컬에서는 잘되는데..."</b>
4. 배포 후 QA 의 필요성을 느끼고 release 브랜치를 추가했다.

### 팀 규칙

`main` : 제품을 최종적으로 배포하는 브랜치(release브랜치로부터 merge만 받는 브랜치)

- 배포에 사용된다.

`develop` : 아직 배포되지 않은 공용 브랜치.

- feature 브랜치로부터 merge를 받는다. 개발 중에 버그를 발견하면 이 브랜치에 직접 commit한다.

`feature` : 새로운 기능 개발을 하는 브랜치.

- 반드시 develop 로부터 시작되고, develop 브랜치에 머지한다.
- **feature/기능이름 /** `feature/fortune-role`

`hotfix` :  다음 배포 전까지 급하게 고쳐야되는 버그를 이 브랜치에서 처리한다.

- 배포 후 심각한 버그 수정이 필요한경우, 버그 수정을 진행한뒤 main, develop 브랜치에 머지 한다.
- hotfix**/기능이름 /** `hotfix/team-intro`

`realese` : 배포 후 QA를 이 브랜치에서 수행한다.

- **로컬에서는 잘되는데..** 를 방지.
- 백엔드 쪽에서 이곳으로 배포를 진행하고, 배포된 서버를 가지고 QA 를 진행한다.
- QA 이후 문제가없다고 판단되면 main으로 머지를 진행

`PR 포맷`

- feature → develop 브랜치는 squash merging `#123`
- develop → release 브랜치는 merge commits
- 이유 : 각자 feature에서 무슨 작업을 어떤 단위로 했는지 묶어서 보기 위해 develop → release 브랜치까지 squash merging을 사용하면 conflict를 해결할 수 있는 방법이 없다.



### 링크

- [우린 Git-flow를 사용하고 있어요](https://techblog.woowahan.com/2553/)

