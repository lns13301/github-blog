---
layout: post
title: 모던 자바 인 액션 7장
date: 2021-03-21 20:10:00 +0900
description: 모던 자바 인 액션을 읽고 문제 기출하기
img: modern_java.png
tags: java study
---

## 모던 자바 인 액션 7장 문제 출제하기

모던 자바 인 액션 7장 스터디에서 내가 공부하고 출제한 문제

1. 반복작업을 진행할 때, 반복문보다 병렬스트림으로 처리하는것이 효율적이다. (O/X)

2. LongStream.rangeClosed 메서드는 기본형 long을 직접 사용하므로 박싱과 언박싱 오버헤드가 사라진다. (O/X)

3. 처리해야할 요소 수와 처리하는데 드는 비용 중  처리하는게 드는 비용이 클수록 병렬스트림으로 성능 개선 가능성이 높아진다. (O/X)

4. Spliterator 를 사용하려면 어떻게 동작하는지 이해하고 직접 구현해야한다. (O/X)

5. Custom Spliterator 구현하기
  함수형으로 “검색하고자 하는 단어 수” 를 구하는 메서드를 구현해보자.
  아래 기능이 동작하도록 WordCounter 를 만들어보자.

  ```java
  public class Main {
      public static void main(String[] args) { 
          String sentence = "Hi everybody nice to meet you. " +
                          "Good to see you. Tomorrow morning, " +
                          "I'm gonna meet one of my best friend. " +
                          "The most small thing is atomic.";
          String targetWord = "to";
  
          Stream<Character> characterStream = IntStream.range(0, sentence.length())
                  .mapToObj(sentence::charAt);
          System.out.println("단어 수: " + countWords(characterStream, targetWord));
      }
  
      private static int countWords(Stream<Character> stream, String targetWord) {
          return stream.reduce(
                  new WordCounter(0, true, targetWord, 0),
                  WordCounter::accumulator,
                  WordCounter::combine).getCounter();
      }
  }
  ```

### 실행결과

> Task :Main.main()
> 단어 수: 3

- 구현이 어렵다면 아래 클래스를 참고하여 해결해보세요.

```java
public class WordCounter {
    private final int counter;
    private final boolean lastSpace;
    private final String targetWord;
    private final int targetWordIndex;

    public WordCounter(int counter, boolean lastSpace, String targetWord, int targetWordIndex) {
        this.counter = counter;
        this.lastSpace = lastSpace;
        this.targetWord = targetWord;
        this.targetWordIndex = targetWordIndex;
    }

    public WordCounter accumulator(Character c) {
        return Character.isWhitespace(c) ? operateWhiteSpace() : operateWord(c);
    }

    private WordCounter operateWhiteSpace() {
        // Todo 기능 구현
    }

    private WordCounter operateWord(Character c) {
        // Todo 기능 구현
        return this;
    }

    public WordCounter combine(WordCounter wordCounter) {
        return new WordCounter(counter + wordCounter.counter, wordCounter.lastSpace, targetWord, targetWordIndex);
    }

    public int getCounter() {
        return counter;
    }
}
```

- 정답
  1. X, 반복 결과로 박싱된 객체가 만들어지 때문에 박싱-언박싱의 오버헤드가 발생한다, 수행할 수 있는 독립 단위로 나누기가 어렵다.
  2. O
  3. O, 처리 해야할 요소 수가 많다는 것은 1번의 이유로 병렬로 처리하면 좋지 않다.
  4. X, Spliterator 인터페이스가 있어서 직접 구현해야 하는 것은 아니다.
  5. WordCounter 구현

```java
public class WordCounter {
    private final int counter;
    private final boolean lastSpace;
    private final String targetWord;
    private final int targetWordIndex;

    public WordCounter(int counter, boolean lastSpace, String targetWord, int targetWordIndex) {
        this.counter = counter;
        this.lastSpace = lastSpace;
        this.targetWord = targetWord;
        this.targetWordIndex = targetWordIndex;
    }

    public WordCounter accumulator(Character c) {
        return Character.isWhitespace(c) ? operateWhiteSpace() : operateWord(c);
    }

    private WordCounter operateWhiteSpace() {
        return lastSpace ? this : new WordCounter(counter, true, targetWord, 0);
    }

    private WordCounter operateWord(Character c) {
        if (c.equals(targetWord.charAt(targetWordIndex))) {
            if (targetWordIndex == targetWord.length() - 1) {
                return new WordCounter(counter + 1, false, targetWord, 0);
            }
            return new WordCounter(counter, false, targetWord, targetWordIndex + 1);
        }
        if (lastSpace) {
            return new WordCounter(counter, false, targetWord, 0);
        }
        return this;
    }

    public WordCounter combine(WordCounter wordCounter) {
        return new WordCounter(counter + wordCounter.counter, wordCounter.lastSpace, targetWord, targetWordIndex);
    }

    public int getCounter() {
        return counter;
    }
}
```
