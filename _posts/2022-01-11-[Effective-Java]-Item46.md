---
layout: post
title: "[Effective Java] Item46. 스트림에서는 부작용 없는 함수를 사용하라"
description: 
categories: self
---

> 아이템 46. 스트림에서는 부작용 없는 함수를 사용하라

-----

Author: PK

```java
Map<String, Long> freq = new HashMap<>();
try (Stream<String> words = new Scanner(file).tokens()) {
    words.forEach(word -> {
        freq.merge(word.toLowerCase(), 1L, Long::sum);
    });
}
```
위의 코드는 스트림 코드를 가장한 반복적 코드다.<br>
`forEach` 메서드가 스트림이 수행한 연산 결과를 보여주는 일 이상을 한다면 코드를 다시 한 번 살펴볼 필요가 있다.
```java
Map<String, Long> freq = new HashMap<>();
try (Stream<String> words = new Scanner(file).tokens()) {
    freq = words.collect(groupingBy(String::toLowerCase, counting()));
}
```
위의 코드가 스트림을 훨씬 잘 사용한 코드다.<br>
스트림의 `forEach` 종단 연산은 가장 '덜' 스트림다운 연산이다. 대놓고 반복적이라서 병렬화할 수도 없다.<br>
연산 결과를 출력하거나, 기존 컬렉션에 추가하는 용도로만 사용하자.

### Collector
`java.util.stream.Collectors` 클래스는 스트림을 이용하여 다양한 형태의 컬렉션을 만들 수 있도록 해준다.<br>
`toList`, `toSet`, `toMap`, `groupingBy`, `joining` 과 같은 수집 팩터리를 사용하면 데이터를 다양한 형태의 자료구조로 담을 수 있다.
