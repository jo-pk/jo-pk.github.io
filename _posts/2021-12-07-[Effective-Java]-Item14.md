---
layout: post
title: "[Effective Java] Item14. Comparable을 구현할 지 고려하라"
description: 하지만 자신을 남과 비교하는건 고려하지 말자
categories: self
---

Author: seovalue

> 아이템 14. Comparable을 구현할 지 고려하라.

Comparable을 구현했다는 것은 그 크랠스의 인스턴스들에는 natural order가 있음을 뜻한다. 따라서 순서가 명확한 클래스를 만들 때에는 Comparable을 구현하자.

```java
public interface Comparable<T> {
    int compareTo(T t);
}
```

### compareTo 메서드 일반 규약
> 이 객체와 주어진 객체의 순서를 비교한다. 이 객체가 주어진 객체보다 작으면 음의 정수를, 같으면 0을, 크면 양의 정수를 반환한다.  

compareTo를 구현 시 필드의 값을 비교할 때 <와 > 연산자는 쓰지 말아야한다. 대신 박싱된 기본 타입 클래스가 제공하는 정적 compare 메서드나 Comparator 인터페이스가 제공하는 비교자 생성 메서드를 사용하자. 

-----

Author: PK

compareTo는 Comparable 인터페이스의 유일무이한 메서드. 기억할 건:

- compareTo가 Object의 메서드가 아니라는 것.
- 단순 동치성 비교에 더해 순서까지 비교할 수 있고, 제네릭하다. 즉, compareTo를 구현했다는 것은 자연스럽게 객체들 사이에 순서가 있다는 것을 의미한다.

compareTo로 수행하는 검사는 다음과 같은 특징을 만족해야 한다: 반사성, 대칭성, 추이성

여기서 마지막인 추이성은 "크기가 같은 객체들끼리는 어떤 객체와 비교하더라도 항상 같다"는 것으로, compareTo 메서드로 수행한 동치성 테스트의 결과가 equals와 같도록 하는 것을 의미한다. 그렇지 않으면 컬렉션이 구현한 인터페이스(Collection, Set, 또는 Map)에 정의된 동작과 다른 행동을 할 수도 있다. 이 컬렉션들은 동치성을 비교할 때 equals 대신 compareTo를 사용한다.

compareTo 메서드에서 필드의 값을 비교할 때 ">" 또는 "<" 연산자 대신 박싱된 기본 타입 클래스가 제공하는 정적 compare 메서드나 Comparator 인터페이스가 제공하는 비교자 생성 메서드를 사용해야한다.
