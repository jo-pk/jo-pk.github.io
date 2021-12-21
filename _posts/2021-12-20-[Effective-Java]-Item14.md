---
layout: post
title: "[Effective Java] Item14. Comparable을 구현할 지 고려하라"
description: 들어가며
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
