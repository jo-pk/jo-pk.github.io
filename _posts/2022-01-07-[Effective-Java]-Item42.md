---
layout: post
title: "[Effective Java] Item42. 익명 클래스보다는 람다를 사용하라"
description: 익명 클래스는 옛날 이야기
categories: self
---

> 아이템 42. 익명 클래스보다는 람다를 사용하라

-----

Author: PK

### 익명 클래스
```java
Collections.sort(words, new Comparator<String>() {
    public int compare(String s1, String s2) {
        return Integer.compare(s1.length(), s2.length());
    }
});
```
낡은 기법. 익명 클래스 방식은 코드가 너무 길다는 것이 특징이다. 그래서 함수형 프로그래밍에 적합하지 않았다.

### 함수형 인터페이스
메서드(함수)가 하나인 인터페이스.<br>
람다식을 사용해서 만들 수 있다. 익명 클래스보다 훨씬 간결하다.
```java
Collections.sort(words, (s1, s2) -> Integer.compare(s1.length(), s2.length()));
```
매개변수와 반환값에 대한 타입을 명시하지 않아도 된다. 컴파일러가 문맥을 살펴서 자동으로 추론해준다.<br>
타입 추론 규칙은 자바 언어 명세에서 하나의 chapter를 모두 차지할만큼 복잡해서 그것을 다 아는 프로그래머는 거의 없다.<br>
그리고 다 알 필요도 없다. 명시했을 때 더 명확한 코드를 작성할 수 있다는 생각이 들 때나 컴파일 오류가 났을 때 빼고는 타입 명시는 생략하자.<br>
다음과 같은 방식으로 위의 코드를 더 간결하게 만들 수 있다:
```java
Collections.sort(words, comparingInt(String::length));
```
또는
```java
words.sort(comparingInt(String::length));
```

### 익명 클래스가 쓰일 때
* 추상 클래스의 인스턴스를 만들 때
* 추상 메서드가 여러 개인 인터페이스의 인스턴스를 만들 때
* 함수 객체가 자신을 참조해야 할 때 (this 키워드는 람다 자신이 아닌 바깥 인스턴스를 가리킨다.)
