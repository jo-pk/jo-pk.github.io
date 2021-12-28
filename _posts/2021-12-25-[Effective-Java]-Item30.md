---
layout: post
title: "[Effective Java] Item30. 이왕이면 제네릭 메서드로 만들라"
description: 하는 김에 다 하자
categories: self
---

> 아이템 30. 이왕이면 제네릭 메서드로 만들라

-----

Author: seovalue

이 책의 주제는 이왕이면 제네릭 메서드로 만들라. 이다.<br>
프로그램을 짜 보면 알겠지만, 제네릭 메서드로 만드는 것은 굉장히 다루기 어렵다.<br>
그리고 형을 강제해야하는 경우도 존재한다. 예를 들어 이 메서드에서는 String 만을 받아야하는 경우라던지, 검증된 값이 들어오는데 굳이 제네릭으로 받을 필요가 없다던지..<br>
따라서 이러한 경우를 제외하고는 제네릭으로 만들어두는 "편"이 클라이언트가 활용하기에는 좋다는 생각이 든다.<br>
실제로 JdbcTemplate 내부만 보더라도 대부분 제네릭으로 되어있고, 나 또한 JdbcTemplate을 만들어 볼 때 반환값을 제네릭이 되도록 함으로써 형변환이 필요한 경우 편리하게 사용할 수 있었다.<br>




-----

Author: PK

제네릭 메서드를 사용하면 타입 안전성과 표현성을 지길 수 있다. 클래스 단위로 적용했던 것과 똑같다.
```java
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
```

제네릭은 런타임 때 타입이 소거된다는 특징이 있어서 어떤 타입으로든 매개변수화할 수 있다.<br>
이럴 때 `제네릭 싱글턴 팩터리` 패턴을 적용한다. 객체의 타입을 바꿔주는 정적 팩터리인 셈이다.<br>
`Collections.reverseOrder` 같은 함수 객체나 `Collections.emptySet` 같은 컬렉션용으로 사용한다.<br>

항등함수를 만들 때 자바의 라이브러리 `Function.identity`를 사용해도 되지만, 제네릭 싱글턴을 사용해서도 만들 수 있다.
```java
private static UnaryOperator<Object> IDENTITY_FUNCTION = (t) -> t;

@SuppressWarnings("unchecked")
public static <T> UnaryOperator<T> identityFunction() {
    return (UnaryOperator<T>) IDENTITY_FUNCTION;
}
```
`Object` 타입은 `T`가 아니기 때문에 비검사 형변환 경고가 나타난다.
그래서 그 경고를 지우기 위해 애너테이션을 붙인다.
항등함수는 입력값을 바꾸지 않으므로 타입 안전하다는 것을 확신할 수 있기 때문이다.<br>

**recursive type bound(재귀적 타입 한정)라는 개념도 있다.**<br>
이를 통해 자신이 들어간 표현식을 사용해서 타입 매개변수의 허용 범위를 지정할 수 있다.<br>
```java
public static <E extends Comparable<E>> E max(Collection<E> c);
```
위의 코드는 재귀적 타입 한정을 이용해 원소를 상호 비교할 수 있음을 표현한 것이다.
