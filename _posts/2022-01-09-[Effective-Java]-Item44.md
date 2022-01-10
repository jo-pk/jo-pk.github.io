---
layout: post
title: "[Effective Java] Item44. 표준 함수형 인터페이스를 사용하라"
description: 이미 있다면 가져다 쓰자
categories: self
---

> 아이템 44. 표준 함수형 인터페이스를 사용하라

-----

Author: PK

`java.util.function` 패키지에는 다양한 상황에 사용할 수 있는 표준 함수형 인터페이스가 들어있다. (43개)<br>
그러니 잘 살펴보고 그것을 사용하도록 하자.<br>
유용한 디폴트 메서드를 제공하므로 다른 코드와의 상호운용성이 크게 좋아질 것이다.<br>

43개라는 수가 좀 많아보일 수 있지만, 6개만 기억하면 나머지는 유추해서 사용할 수 있다.

| 인터페이스             | 함수 시그니처           | 예                   |
|-------------------|-------------------|---------------------|
| UnaryOperator<T>  | T apply(T t)      | String::toLowerCase |
| BinaryOperator<T> | T apply(T t1, T2) | BigInteger::add     |
| Predicate<T>      | boolean test(T t) | Collection::isEmpty |
| Function<T>       | R apply(T t)      | Arrays::asList      |
| Supplier<T>       | T get()           | Instant::now        |
| Consumer<T>       | void accept(T t)  | System.out::println |

* 기본 인터페이스는 기본 타입인 int, long, double용으로 각 3개씩 변형이 생겨난다.
* 그리고 그 외에도 다양한 타입으로 인한 변형이 생겨서 총 43개가 만들어지는 것이다.
* 기본 함수형 인터페이스에 박싱된 기본 타입을 넣어서 사용하지는 말자. 계산량이 많을 때 성능이 떨어질 수 있다.
* 다음 세 가지 특성 중 하나 이상을 만족하면 전용 함수형 인터페이스를 구현하는 것을 고민해보자:
  * 자주 쓰인다. 그리고 이름 자체가 용도를 명확히 설명한다.
  * 반드시 따라야 할 규약이 있다.
  * 유용한 디폴트 메서드를 제공할 수 있다.

### @FunctionalInterface 어노테이션을 적극 활용하자
* 해당 인터페이스가 람다용으로 만들어졌음을 명시한다.
* 메서드가 두 개 이상일 때 컴파일 오류를 발생시킨다. 즉, 그 용도에서 벗어나지 않도록 잡아준다.
* 따라서 여러 명이 함께 작업할 때 발생할 수 있는 실수를 줄여준다.
