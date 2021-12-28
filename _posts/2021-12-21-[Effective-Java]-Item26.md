---
layout: post
title: "[Effective Java] Item26. 로 타입은 사용하지 말라"
description: 제네릭이 등장한 이유
categories: self
---

> 아이템 26. 로 타입은 사용하지 말라

| 용어                      | 한글 용어         | 예                                | 아이템        |
|-------------------------|---------------|----------------------------------|------------|
| parameterized type      | 매개변수화 타입      | List<String>                     | 아이템 26     |
| actual type parameter   | 실제 타입 매개변수    | String                           | 아이템 26     |
| generic type            | 제네릭 타입        | List<E>                          | 아이템 26, 29 |
| formal type parameter   | 정규 타입 매개변수    | E                                | 아이템 26     |
| unbounded wildcard type | 비한정적 와일드카드 타입 | List<?>                          | 아이템 26     |
| raw type                | 로 타입          | List                             | 아이템 26     |
| bounded type parameter  | 한정적 타입 매개변수   | <E extends Number>               | 아이템 29     |
| recursive type bound    | 재귀적 타입 한정     | <T extends Comparable<T>>        | 아이템 30     |
| bounded wildcard type   | 한정적 와일드카드 타입  | List<? extends Number            | 아이템 31     |
| generic method          | 제네릭 메서드       | static <E> List<E> asList(E[] a) | 아이템 30     |
| type token              | 타입 토큰         | String.class                     | 아이템 33     |


-----

Author: seovalue

로 타입을 사용하면 런타임에 예외가 일어날 수 있으니 사용하면 안된다.<br>

그렇다면 로 타입이 존재하는 이유는 무엇일까?<br>
로 타입은 제네릭이 도입되기 이전 코드와의 호환성을 위해 존재한다.<br>

```java
// GOOD
Set<Object> set = new HashSet<>();
Set<?> set = new HashSet<>();

// BAD
Set badSet = new HashSet<>(); -> 컴파일될 지도 모르겠다.
```


_____

Author: PK

로 타입은 제네릭이라는 개념이 등장하기 전에 만들어진 코드도 호환되도록 존재하는 것이다.<br>
그 외의 이유는 없다.<br>
따라서 제네릭이 존재하는 이 시점에는 로 타입을 사용할 이유가 없다.<br>

제네릭을 사용하여 컬렉션을 만들면 원소를 꺼낼 때마다 컴파일러가 알아서 형변환을 해준다.<br>
그리고 코드상에서 해당 컬렉션이 어떤 클래스의 인스턴스를 가지고 있는지 볼 수 있으므로
**제네릭을 통해 타입 안정성과 표현력을 챙길 수 있다.**<br>

누군가 다음과 같이 물어볼 수 있다:
> `List<Object>`는 써도 괜찮나요?

정답은 당연히 YES.<br>
`List<Object>`는 모든 타입의 인스턴스를 담는다고 명시한 것이므로, 타입 안정성과 표현력이 유지된다.<br>

그렇다면 타입을 신경쓰고 싶지 않은 상황에는 어떻게 할까?<br>
**와일드 카드**를 쓰면 된다. 제네릭 타입 `Set<E>`의 비한정적 와일드카드 타입은 `Set<?>`다. 이는 제네릭 형태를 유지하므로 타입 안전하며, 특정 타입을 고집하지 않아서 유연하기까지 하다.

```java
static int doSomethingWithSets(Set<?> s1,Set<?> s2){
    // ...
    }
```

와일드카드 타입과 로 타입의 가장 큰 차이점은
**와일드카드 타입의 컬렉션에는 null을 제외하고 그 어떤 원소도 넣을 수 없다는 점이다.**<br>

그런데 와일드카드에 대해 기억할 점이 두 가지 있다:

1. class 리터럴에는 꼭 로 타입을 써야한다. (제네릭을 지원하지 않는다.)

```java
// can not do.
List<String>.

class
List<?>.

class

// can do.
List.class
int[].class
int.class
```

2. instanceof 는 와일드카드나 로 타입에 관계없이 똑같이 작용한다. 그리고 제네릭 타입 정보는 런타임 때 지워져서 적용할 수 없다. 따라서 다음과 같이 사용해야 한다:

```java
if(o instanceof Set){
    Set<?> s=(Set<?>)o; // checked cast. so no error
    }
```
