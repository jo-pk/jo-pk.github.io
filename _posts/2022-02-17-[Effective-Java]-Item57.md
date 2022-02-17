---
layout: post
title: "[Effective Java] Item57. 지역변수의 범위를 최소화하라"
description: 실수는 버그의 어머니
categories: self
---

> 아이템 57. 지역변수의 범위를 최소화하라

-----

Author: PK

저자는 변수 선언을 `가장 처음 쓰일 때 선언`하고, `선언과 동시에 초기화`해야 한다고 말한다.<br>
얼핏 보면 "아 이해했어, 당연하지"라고 보고 그냥 넘어갈 수 있다.<br>
**그러나 이번 아이템에서 중요한 것은 저자가 예시로 보여주는 방법이다.**<br>

### while 문 대신 for 문
`반복 변수의 값을 반복문이 종료된 뒤에도 써야 하는 상황이 아니라면` while 문보다는 for 문을 사용하자.
```java
Iterator<Element> i = collection.iterator();
while(i.hasNext()) {
    doSomething(i.next());
}

// 여기 주목
Iterator<Element> i2 = collection2.iterator();
while(i.hasNext()) {
    doSomething(i.next());
}
```
위의 코드에는 개발자가 의도하지 않은 버그가 있다.<br>
코드를 복사-붙여넣기하며 발생한 문제인데, i2 이터레이터를 선언 및 초기화를 해두고<br>
막상 다른 반복 변수를 사용해서 로직을 처리하는 것이다.<br>

해당 코드는 컴파일 시점에 오류가 발생하지 않으므로 애플리케이션이 배포된 후에 문제를 발견할 가능성이 높다.<br>
고작 한 줄 차이지만, 반복하는 로직과 반복 변수가 선언되는 시점에 차이가 있어서 생기는 허무한 버그다.<br>

```java
for (Iterator<Element> i = c.iterator(); i.hasNext(); ) {
    doSomething(i.next());
}
```
그래서 위와 같이 변수 선언과 반복 로직을 최대한 가까이 두는 것이 좋다.<br>
이러면 반복 변수의 범위를 해당 for 문 안으로 제한한다.<br>
코드의 뒷부분에서 그 변수를 사용하지 못하게 해서 실수를 방지하는 것이다.<br>

```java
for (Element element : collection) {
    doSomething(element);
}
```
물론 위와 같이 향상된 for 문을 사용해서 훨씬 깔끔하고 안전한 코드를 만들 수도 있다.<br>
하지만 이 경우는 컬렉션 안에 있는 모든 객체를 순차적으로 조회해야 한다는 조건이 있다.<br>

범위를 제한하는 것의 또 다른 장점으로는 변수 이름을 재활용할 수 있다는 점이다.<br>
억지스러운 느낌이 들지 모르지만, 막상 개발하다보면 이것만큼 실용적인 장점도 없다.<br>

### 성능을 고려하고 싶어요!
```java
for (int i = 0; i < collection.size(); i++) {
    doSomething(collection.get(i));
}
```
위의 코드는 평범한 for 문이다.<br>
앞서 말한 조언을 듣고 for 문을 활용하여 로직을 반복하고 있다. (향상된 for 문으로 변경할 수도 있지만 일단 그냥 보자)<br>

누군가가 이렇게 말할 수 있다:<br>
"`collection.size()`를 매번 실행하기 싫어요. 그 값을 저장하여 성능을 향상시키고 싶습니다."<br>
그리고 다음과 같이 코드를 변경한다:
```java
int size = collection.size();
for (int i = 0; i < size; i++) {
    doSomething(collection.get(i));
}
```
좋은 생각이다.<br>
그런데 우리는 변수 선언을 필요할 때까지 최대한 미룬다는 아이디어를 활용하여 해당 코드를 더 개선할 수 있다.
```java
for (int i = 0, size = collection.size(); i < size; i++) {
    doSomething(collection.get(i));
}
```
이러면 `size` 라는 변수가 다른 곳에서 부적절하게 사용되는 것을 막을 수 있고,<br>
`collection.size()`를 매번 실행하지 않아서 성능도 챙길 수 있다.<br>
