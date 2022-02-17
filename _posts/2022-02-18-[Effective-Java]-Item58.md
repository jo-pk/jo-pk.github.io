---
layout: post
title: "[Effective Java] Item58. 전통적인 for 문보다는 for-each 문을 사용하라"
description: 왜 이름이 "향상된 for 문"일까...
categories: self
---

> 아이템 48. 스트림 병렬화는 주의해서 적용하라

-----

Author: PK

for 문은 실수가 발생하는 주요 부분 중 하나다.<br>
가독성 측면에서도 안 좋을 수 있는데, 반복 조건이 복잡할 때가 그런 경우다.<br>
```java
for (int i = 0; i < 100; i++) {
    doSomething(i);
}
```
잘 생각해보면 개발자가 채워야 하는 부분이 네 군데나 있다.<br>
어차피 어느 한 컬렉션의 모든 요소에 동일한 로직을 적용할 것이라면 (그리고 그 경우가 99%의 경우다),<br> 
`for-each` 문을 사용하도록 하자. 정식 명칭은 `향상된 for 문(enhanced for loop)`이다.
```java
for (Element element : collection) {
    doSomething(element);
}
```
위의 예시는 컬렉션으로 작성했는데, 배열도 사용가능하다.<br>

### 저는 실수 안하는데요?
실수 안한다고 생각하고 발생하는 것이 바로 실수다.<br>
특히나 `Iterator`를 활용하는 로직을 작성했을 때, 그리고 for 문이 중첩되기 시작할 때 그 위험성이 커진다.
```java
enum Suit { CLUB, DIAMOND, HEART, SPADE }
enum Rank { ACE, DEUCE, THREE, FOUR, FIVE, SIX, SEVEN, EIGHT, NINE, TEN, JACK, QUEEN,, KING }

static Collection<Suit> suits = Arrays.asList(Suit.values());
static Collection<Rank> ranks = Arrays.asList(Rank.values());

List<Card> deck = new ArrayList<>();
for (Iterator<Suit> i = suits.iterator(); i.hasNext(); ) {
    for (Iterator<Rank> j = ranks.iterator(); j.hasNext(); ) {
        deck.add(new Card(i.next(), j.next()));
    }
}
```
위의 코드를 보면 당연히 `각각의 Suit 마다 각각의 Rank 를 불러서 카드 객체를 생성한다`라고 생각할 수도 있다.<br>
하지만 `iterator.next()`라는 메서드는 컬렉션의 다음 요소를 부르기 때문에 `NoSuchElementException`이 발생한다.<br>

```java
for (Suit suit : suits) {
    for (Rank rank : ranks) {
        deck.add(new Card(suit, rank));
    }
}
```
그러니 위와 같이 명확하게 요소를 불러와서 객체 생성을 해주자.<br>
실수를 줄여줄 뿐만 아니라 코드가 간결해지기까지 한다.<br>
뿐만 아니라 성능 저하도 없다!<br>

물론 다음과 같은 상황에서 향상된 for 문은 사용할 수 없다:
* 컬렉션을 순회하며 특정 요소를 제거할 때. (이럴 땐 `Collection`의 `removeIf` 메서드를 활용하자.)
* 컬렉션의 요소를 교체해야 할 때.
* 여러 컬렉션을 병렬로 순회할 때.

향상된 for 문은 `Iterator` 인터페이스를 구현한 객체에 적용할 수 있다.<br>
구현해야 할 메서드가 `iterator()` 하나뿐이니 필요하다면 구현해보자.<br>
웬만하면 Java가 제공하는 `Collection`을 사용해서 이런 경우가 흔하진 않다. 
