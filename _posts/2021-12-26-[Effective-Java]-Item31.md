---
layout: post
title: "[Effective Java] Item31. 한정적 와일드카드를 사용해 API 유연성을 높이라"
description:
categories: self
---

Author: seovalue

> 아이템 31. 한정적 와일드카드를 사용해 API 유연성을 높여라


-----

Author: PK

**`PECS (팩스)`: producer-extends, consumer-super`**<br>
와일드카드를 사용하는 기본 원칙이다.<br>
그런데 반환 타입에는 한정적 와일드카드 타입을 사용하면 안된다.<br>
*유연성을 높여주기는커녕 클라이언트 코드에서도 와일드카드 타입을 써야 하기 때문이다.*<br>

다음은 PECS 공식을 두 번 적용해서 api 를 다듬은 예시다:
```java
// before
public static <E extends Comparable<E>> E max(List<E> list);

// after
public static <E extends Comparable<? super E>> E max(List<? extends E> list);
```
**메서드 선언에 타입 매개변수가 한 번만 나오면 와일드카드로 대체하라**<br>
와일드카드를 사용하는 또 다른 원칙이다.<br>

그런데 와일드카드를 쓰면 컬렉션에 원소를 추가하지 못하는 문제가 생긴다.<br>
이건 다음과 같이 우회할 수 있다:
```java
public static void swap(List<?> list, int i, int j) {
    swapHelper(list, i, j);
}

// 와일드카드 타입을 실제 타입으로 바꿔주는 private 도우미 메서드
private static <E> void swapHelper(List<E> list, int i, int j) {
    list.set(i, list.set(j, list.get(i)));
}
```
