---
layout: post
title: 🧶 Java 에서 스레드 풀(Thread Pool) 을 사용해 보자
date: 2021-09-25 22:20:00 +0900
description: 우아한테크코스 블로그 근로
img: thread-pool.jpg
tags: java study
---

프로세스 내에서 스레드의 생성 및 수거가 빈번하게 발생한다면 메모리 할당에 소모되는 비용이 많이 들지 않을까? 이에 대한 해답으로 스레드 풀에 대해 알아보고 Java 에서 어떻게 사용하면 되는지 알아보자.

## 스레드 풀(Thread Pool)

스레드 제어 문제를 해결할 방법으로 스레드 풀을 사용한다. 스레드 풀은 매번 생성 및 수거 요청이 올 때 스레드를 생성하고 수거하는 것이 아닌, 스레드 사용자가 설정해둔 개수만큼 미리 생성해두는 것이다.

### 스레드(Thread)

- 어떤 프로그램 내에서 실행되는 흐름의 단위
- 특히 프로세스 내에서 실행되는 흐름의 단위

### 풀(Pool)

- 필요할 때마다 개체를 할당하고 파괴하는 대신, 사용 준비된 상태로 초기화된 개체 집합

스레드는 동일한 메모리 영역에서 생성 및 관리가 이루어지지만, 생성하거나 수거할 때 커널 오브젝트를 동반하는 리소스이므로 생성 비용이 크게 발생한다. 스레드를 제어할 수 없는 상태에서 스레드를 무차별적으로 생성하면 리소스가 빠르게 소진되는 상황이 발생할 수 있다. 그러면 어떻게 하면 효율적으로 스레드를 제어할 수 있을까?
스레드 풀의 동작 방식은 간단하게 보면 다음과 같다.

1. 병렬 작업의 형태로 동시 코드를 작성한다.
2. 실행을 위해 스레드 풀의 인스턴스에 제출한다.
3. 제출한 인스턴스에서 실행하기 위해 재사용되는 여러 스레드를 제어한다.

스레드 풀을 사용하면 비용적인 측면이나 컨텍스트 스위치가 발생하는 상황에서 딜레이를 줄일 수 있다는 장점이 있다. 그렇다면 단점은 어떤 것이 있을까?

단점은 스레드 풀에 너무 많은 양의 스레드를 만들어둔다면 메모리 낭비가 심해질 수 있다는 점이다. 그 때문에 얼만큼의 스레드가 필요할지 예측하고 할당해서 사용하는 것이 스레드 풀을 현명하게 사용하는 것이라고 할 수 있다.

## Java 에서 스레드 풀 사용하기

`ExecutorService` 인터페이스의 구현 객체를 정적 팩토리 메서드로 제공하는 `Executors` 클래스의 세 가지 메소드 중 하나를 이용하여 스레드 풀을 쉽게 생성할 수 있다. 자세한 메서드 사용법은 [이곳](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/Executors.html)에서 알 수 있다.

`ThreadPoolExecutor` 에서 사용되는 파라미터로는 `corePoolSize(생성할 개수), maximumPoolSize(생성할 최대 개수), keepAliveTime(유지 시간)` 이 있다. 스레드 풀에서 스레드를 생성할 때 `corePoolSize` 의 파라미터만큼 코어 스레드를 생성한다. 그리고 새로운 작업이 들어올 때 모든 코어 스레드가 사용 중이고 내부 큐가 가득 차면 스레드 풀의 최대 크기가 `maximumPoolSize` 만큼 커질 수 있다. 만약 현재 스레드 풀이 `corePoolSize` 보다 많은 스레드를 가지고 있다면, 초과한 스레드에 대해서 `keepAliveTime` 파라미터값보다 오랫동안 할 일이 없으면 제거된다. 이것은 자원의 낭비를 감소 시켜 효율적으로 스레드 풀을 관리할 수 있게 된다.

스레드 풀에 작업요청을 하는 방식은 `execute( )`, `submit( )` 방식이 있다. `execute` 방식은 작업 처리 중에 예외가 발생하면 해당 <b>스레드가 종료</b>되고 스레드 풀에서 제거한 뒤, 새로운 스레드를 생성하여 다른 작업을 처리한다. 또한 <b>처리결과를 반환하지 않는다.</b> 반대로 `submit` 은 작업 처리 중에 예외가 발생하더라도 <b>스레드가 종료되지 않고</b> 다음 작업에 사용된다. 또한 <b>처리 결과를 `Future<?>` 로 반환</b>한다. 따라서 스레드 풀을 사용할 때 `submit` 을 사용하는 점이 더 바람직하다.

테스트 클래스를 하나 생성하여 테스트를 진행한다.

우선 `MyCounter` 클래스를 하나 만든다. 해당 클래스는 `increment` 메서드를 통해 `count` 를 증가시킨 후 1초의 대기시간을 갖는다.

```java
public class MyCounter {
    private int count;

    public void increment() {
        try {
            int temp = count;
            count = temp + 1;
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public int getCount() {
        return count;
    }
}
```

첫 번째 방법은 `Executors.newFixedThreadPool(int nThreads)` 에 대해 알아본다.

파라미터로 제공되는 n 개 만큼 스레드 풀을 생성한다. 보통 일정량의 업무가 발생할 때 사용한다. 다음 예시는 18개의 스레드가 필요한 Task 를 제공하고 5개의 스레드 풀로 처리하는 과정을 확인해 보는 테스트다.

```java
    @DisplayName("FixedThreadPool 을 생성한다.")
    @Test
    void testCounterWithConcurrencyFixed() throws InterruptedException {
        int numberOfThreads = 18;
        ExecutorService service = Executors.newFixedThreadPool(5);
        CountDownLatch latch = new CountDownLatch(numberOfThreads);
        MyCounter counter = new MyCounter();
        iterateThread(numberOfThreads, service, latch, counter);

        assertThat(((ThreadPoolExecutor) service).getPoolSize()).isEqualTo(5);
    }

    private void iterateThread(int numberOfThreads, ExecutorService service, CountDownLatch latch, MyCounter counter) throws InterruptedException {
        for (int i = 0; i < numberOfThreads; i++) {
            service.submit(() -> {
                counter.increment();
                latch.countDown();
                throw new IllegalArgumentException();
            });
        }
        latch.await();
    }
```
![image](https://user-images.githubusercontent.com/49058669/133976949-a4dd1594-0b04-4013-86c5-d2f8fb6c688a.png)

실행 결과를 살펴보면 한 스레드에서 작업을 처리하는 데 1초가 걸리도록 설정이 되어있다. 그 때문에 5개의 작업을 3번, 3개의 작업을 1번 하게 되면서 총 작업 시간은 (3+1)초 + 로직 실행 시간(300ms)이 된다. 따라서 스레드 풀에 생성된 <b>스레드 개수만큼 작업을 처리</b>하는 모습을 확인할 수 있다. 또한, 스레드 풀의 크기가 5개로 유지되는 모습을 확인할 수 있다. 해당 작업에서 Count 가 여러 스레드에서 동시에 호출되는데 이 경우 비동기로 처리되기 때문에 실제 Count 의 결과(`counter.getCount()`)는 100보다 작은 값이 반환되며 실행할 때마다 랜덤한 결과를 반환하는 모습을 확인할 수 있다.

두 번째 방법은 `Executors.newCachedThreadPool()` 에 대해 알아본다.

초기 스레드 개수가 0개로 설정되며 스레드 개수보다 많은 양의 작업의 요청되면 <b>새로운 스레드를 생성하여 작업을 처리</b>한다. 작업이 끝난 스레드가 60초 이상 새로운 작업요청이 없으면 스레드를 종료하고 스레드 풀에서 제거된다. 다음 예시는 18개의 스레드가 필요한 Task 를 제공하고 5개의 스레드 풀로 처리하는 과정을 확인해 보는 테스트다. 위에 생성해둔 코드에 이어서 작성해서 테스트를 진행하면 된다.

```java
    @DisplayName("CachedThreadPool 을 생성한다.")
    @Test
    void testCounterWithConcurrencyCached() throws InterruptedException {
        int numberOfThreads = 18;
        ExecutorService service = Executors.newCachedThreadPool();
        CountDownLatch latch = new CountDownLatch(numberOfThreads);
        MyCounter counter = new MyCounter();
        iterateThread(numberOfThreads, service, latch, counter);

        assertThat(counter.getCount()).isEqualTo(numberOfThreads);
        assertThat(((ThreadPoolExecutor) service).getPoolSize()).isEqualTo(18);
        Thread.sleep(60000); // 60초 후 생성된 스레드가 제거되는지 확인한다.
        assertThat(((ThreadPoolExecutor) service).getPoolSize()).isEqualTo(0);
    }
```
![image](https://user-images.githubusercontent.com/49058669/133979857-aea7e9c2-050c-433c-b445-f83b32d45b4e.png)

세 번째 방법은 `Executors.newScheduledThreadPool(int corePoolSize)` 에 대해 알아본다.

스레드를 <b>일정시간이 흐르고 난 뒤 실행</b>시키도록 하는 스케줄링 스레드 기능이다. 해당 기능을 테스트해 보기 위해서는 테스트 코드가 아닌 메인에서 실행해봐야 한다. 따라서 `Sample` 클래스를 만들고 실습을 해본다. corePoolSize 는 생성할 corePool 의 크기를 지정해주는 부분인데 `Executors.newScheduledThreadPool(0)` 을 하더라도 실행에는 문제가 없어 보인다. 다만 JDK 8 버전 이하에서 발견된 [버그](https://bugs.openjdk.java.net/browse/JDK-8129861)로 단일 코어 가상 머신에서 CPU 를 100% 사용하는 버그가 있기 때문에 파라미터로 1 이상으로 설정한다.

```java
public class Sample {
    public static void main(String[] args) {
        ScheduledExecutorService service = Executors.newScheduledThreadPool(1);

        System.out.println("첫 번째 작업이 실행됩니다.");
        service.schedule(() -> System.out.println("두 번째 작업이 실행됩니다."), 5, TimeUnit.SECONDS);
        System.out.println("세 번째 작업이 실행됩니다.");

        service.shutdown();
    }
}
```
![image](https://user-images.githubusercontent.com/49058669/133980017-e1cc4860-0fc2-4e88-872c-6853887db442.png)
스케줄에서 설정한 5초가 흐른 후 `두 번째 작업이 실행됩니다.` 가 실행되는 모습을 확인할 수 있다.



## 결론

자바에서 사용하게 될 스레드 풀에 대해 간단하게 알아봤다. 얼마만큼의 스레드가 주기적으로 소모될지, 스레드를 추가로 생성할지, 작업 후 반환 값을 받을지에 대한 분석을 토대로 상황에 적합한 Thread 및 ThreadPool 을 적용한다면 멀티 코어 프로그래밍에 큰 도움이 된다고 생각한다. 다만, 적절하지 않게 사용할 경우 오히려 사용하지 않은 것보다 못할 수 있기 때문에 주의해야 한다.



## 참고

- [Class Executor Oracle Docs](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/Executors.html)
- [Introduction to Thread Pools in Java](https://www.baeldung.com/thread-pool-java-and-guava)
- [[Java] Thread Pool(스레드 풀)](https://limkydev.tistory.com/55)
- [Java Thread Pool (자바 쓰레드 풀)](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=2feelus&logNo=220728222140)
