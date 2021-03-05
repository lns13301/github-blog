---
layout: post
title: 이펙티브 자바 Item 1: 생성자 대신 정적 팩터리 메서드를 고려하라
date: 2021-03-06 02:30:00 +0900
description: 정적 팩터리 메서드 학습 내용
img: woowacourse.jpg
tags: java study
---

# 생성자 대신 정적 팩터리 메서드를 고려하라

### 클라이언트가 클래스의 인스턴스를 얻는 수단?

1. Public 생성자
2. 정적 팩터리 메서드(static final method)

> 정적 팩터리 메스드의 예시 (Boolean)
>
> ```java
> public static Boolean valueOf(boolean b) {
> 		return b ? Boolean.TRUE : Boolean.FALSE;
> }
> ```

## 정적 팩터리 메서드의 장점

1. 이름을 가질 수 있다.

   - 이름만 잘 지으면 반환될 객체의 특성을 쉽게 묘사할 수 있다.

   - 한 클래스에 시그니처가 같은 생성자가 여러 개 있어도 문제 없이 표현 가능하다.

     

   - 예시

     ```java
     public class Card {
     		
     		private String type;
     		private String denomination;
     		
     		private Card() {
     		}
     		
     		// J 다이아몬드 트럼프 카드를 생성하는 정적 팩터리 메서드
     		public static Card createDiamondJack() {
     				return new Card("Diamond", "Jack")
     		}
     		
     		// 7 클로버 트럼프 카드를 생성하는 정적 팩터리 메서드
     		public static Card createClubSeven() {
     				return new Card("Club", "Seven")
     		}
     }
     ```

     - 위의 예시를 보면 Card.createDiamondJack(); 이라는 정적 팩터리 메서드를 사용하면 누구나 쉽게 "J 다이아몬드" 라는 카드가 생성될 것이라는 것을 유추할 수 있다.

     - 또한 createDiamondJack() 과 createClubSeven 은 둘다 파라미터 없이 사용할 수 있는 메서드(메서드 시그니처가 같음)지만 정적 팩터리 메서드를 통해서 문제 없이 생성할 수 있다.

       

2. 호출될 때마다 인스턴스를 새로 생성하지는 않아도 된다.

   - 이 덕분에 불변 클래스(immutable class; [Item17](https://lns13301.github.io/github-blog/))는 인스턴스를 미리 만들어 놓거나 새로 생성한 인스턴스를 캐싱하여 재활용하는 식으로 불필요한 객체 생성을 피할 수 있다.

     

   - 예시

     ```java
     public class LottoNumber {
     		
     		private static final List<LottoNumber> CACHE = new ArrayList<>();
     
         // 사전에 CACHE 에 로또 번호인 1 ~ 45를 저장해둔다.
     		static {
     				for (int i = 1; i <= 45; i++) {
     						CACHE.add(new LottoNumber(i));
     				}
     		}
       
       	private final int number;
       	
       	// private 생성자로 외부에서 생성자를 사용하여 임의의 번호를 생성하는 것을 막는다.
       	private LottoNumber(final int number) {
           	this.number = number;
         }
       	
       	// 캐싱해둔 로또 번호를 반환하거나 올바르지 않은 숫자를 입력 받으면 예외를 발생시킨다.
       	public static LottoNumber valueOf(final int number) {
             try {
                 return CACHE.get(number - 1);
             } catch (IndexOutOfBoundsException e) {
                 throw new IllegalArgumentException("올바른 로또 번호가 아닙니다.");
             }
         }
     }
     ```

     

3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.

   - 반환할 객체의 클래스를 자유롭게 선택할 수 있게 하는 '엄청난 유연성'을 선물한다.

   - 이 유연성을 응용하면 구현 클래스를 공개하지 않고도 그 객체를 반환할 수 있어 API 를 작게 유지할 수 있다.

   - 인터페이스 기반 프레임워크([Item20](https://lns13301.github.io/github-blog/))를 만드는 핵심 기술이기도 하다.

     

   - 예시

     ```java
     public class Person {
       	
         private final String name;
         private final int age;
     
         protected Person(String name, int age) {
             this.name = name;
             this.age = age;
         }
     		// 정적 팩터리 메서드를 통해 하위 타입 객체를 반환한다.
         public static Person createStudent(String name, int age, String schoolName) {
             return Student.of(name, age, schoolName);
         }
     }
     
     // Person 을 상속 받는 Student 클래스
     public class Student extends Person {
       
         private final String schoolName;
     
         private Student(String name, int age, String schoolName) {
             super(name, age);
             this.schoolName = schoolName;
         }
     
         public static Student of(String name, int age, String schoolName) {
             return new Student(name, age, schoolName);
         }
     }
     ```

     

4. 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.

   - 반환 타입의 하위 타입이기만 하면 어떤 클새의 객체를 반환하든 상관없다.

   - 다음 릴리스에서는 또 다른 클래스의 객체를 반환해도 된다.

     

   - 예시

     ```java
     public class Job {
     
         private final int power;
         private final int intelligence;
     
         protected Job(int power, int intelligence) {
             this.power = power;
             this.intelligence = intelligence;
         }
     
         public static Job getInstance(int power, int intelligence) {
           	// 힘이 10이 넘어가면 Warrior 을 반환한다.
             if (power > 10) {
                 return Warrior.of(power, intelligence);
             }
             // 지능이 10이 넘어가면 Magician 을 반환한다.
             if (intelligence > 10) {
                 return Magician.of(power, intelligence);
             }
             // 위의 조건을 만족하지 못할 때는 Novice 를 반환한다.
             return Novice.of(power, intelligence);
         }
     }
     
     // 아래에는 Job 을 상속받는 하위 클래스들의 예시이다.
     public class Warrior extends Job {
     		private Warrior(int power, int intelligence) {
             super(power, intelligence);
         }
     
         public static Warrior of(int power, int intelligence) {
             return new Warrior(power, intelligence);
         }
     }
     
     public class Magician extends Job {
     		private Magician(int power, int intelligence) {
             super(power, intelligence);
         }
     
         public static Magician of(int power, int intelligence) {
             return new Magician(power, intelligence);
         }
     }
     
     public class Novice extends Job {
     		private Novice(int power, int intelligence) {
             super(power, intelligence);
         }
     
         public static Novice of(int power, int intelligence) {
             return new Novice(power, intelligence);
         }
     }
     
     ```

     

5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

   - 이런 유연함은 서비스 제공자 프레임워크(service provider framework)를 만드는 근간이 된다.

     - 대표적인 서비스 제공자 프레임워크로는 JDBC(Java Database Connectivity)가 있다.

   - 서비스 제공자 프레임워크에서의 제공자는 서비스의 구현체다.

   - 이 구현체들은 클라이언트에 제공하는 역할을 프레임워크가 통제하여, 클라이언트를 구현체로부터 분리해준다.

     

   - 예시

     ```java
     public class Move {
       	// MoveStrategy 가 아직 구현되지 않았지만 반환이 된다.
         public static List<MoveStrategy> instance() {
             return new ArrayList<>();
         }
     }
     
     public interface MoveStrategy {
       	// 아직 구현되지 않았다.
     }
     
     // 구현 클래스를 생성한다.
     public class Walk implements MoveStrategy {
     		public void move() {
             System.out.println("걸어서 움직인다.");
         }
     }
     
     // 구현 클래스를 생성한다.
     public class Run implements MoveStrategy {
     		public void move() {
             System.out.println("달려서 움직인다.");
         }
     }
     
     public class Main {
         public static void main(String[] args) {
             List<MoveStrategy> moveImpls = Move.instance();
     
           	// 생성한 구현 클래스를 List 에 추가한다.
             moveImpls.add(new Walk());
             moveImpls.add(new Run());
         }
     }
     ```

     

## 정적 팩터리 메서드의 단점

1. 상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.

   - 컬렉션 프레임워크의 유틸리티 구현 클래스들은 상속할 수 없다는 이야기다.
   - 이 제약은 상속보다 컴포지션을 사용([Item18](https://lns13301.github.io/github-blog/))하도록 유도하고 불변 타입([Item17](https://lns13301.github.io/github-blog/))으로 만들려면 이 제약을 지켜야 한다는 점에서 오히려 장점으로 받아들일 수도 있다.

2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.

   - 생성자처럼 API 설명에 명확히 드러나지 않는다.

   - 언젠가 자바독이 알아서 처리해주기 전까지 API 문서를 잘 써놓고 메서드 이름도 널리 알려진 규약을 따라 짓는 식으로 문제를 완화해줘야 한다.

     

   - 예시 (정적 팩토리 메서드에 흔히 사용하는 명명 방식들)

     ```java
     // from: 매개변수 하나를 받아서 해당 타입의 인스턴스를 반환하는 형변환 메서드
     Person person = Person.form("사람이름");
     
     // of: 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드
     Student student = Student.of("학생이름", "학교이름");
     
     // valueOf: from 과 of 의 더 자세한 버전
     BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);
     
     // instance 혹은 getInstance: (매개변수를 받는다면) 매개변수로 명시한 인스터스를 반환하지만, 같은 인스턴스임을 보장하지는 않는다.
     Job warrior = Job.getInstance(15, 5);
     
     // create 혹은 newInstance: instance 혹은 getInstance와 같지만, 매번 새로운 인스턴스를 생성해 반환함을 보장한다.
     Object newArray = Array.newInstance(classObject, arrayLen);
     
     // getType: getInstance 와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다. "Type"은 팩터리 메서드가 반환할 객체의 타입이다.
     FileStore fileStore = Files.getFileStore(path);
       
     // newType: newInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다. "Type"은 팩터리 메서드가 반환할 객체의 타입이다.
     BufferedReader bufferedReader = Files.newBufferedReader(path);
     
     // type: getType과 newType의 간결한 버전
     List<Complaint> litany = Collections.list(legacyLitany);
     ```

## 핵심 정리

> 정적 팩터리 메서드와 public 생성자는 각자의 쓰임새가 있으니 상대적인 장단점을 이해하고 사용하는 것이 좋다. 그렇다고 하더라도 정적 팩터리를 사용하는게 유리한 경우가 더 많으므로 무작정 public 생성자를 제공하던 습관이 있다면 고치자.

