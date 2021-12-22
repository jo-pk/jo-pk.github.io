---
layout: post
title: "[Effective Java] Item32. 제네릭과 가변인수를 함께 쓸 때는 신중하라"
description: 가변인수는 구현에 허점이 있다!
categories: self
---

Author: seovalue

> 아이템 32. 제네릭과 가변인수를 함께 쓸 때는 신중하라

-----

Author: PK

가변인수와 제네릭은 잘 어우러지지 않는다.<br>
**가변인수는 구현 방식에 허점이 있다.**<br>
가변 인수 메서드를 호출하면 가변인수를 담귀 위한 배열이 만들어지는데, 이게 클라이언트에게 노출된다.
그래서 가변인수에 제네릭이 포함되면 알기 어려운 컴파일 경고가 발생한다.<br>

만약 가변인수의 타입이 실체화 불가능한 타입으로 추론되면 메서드를 호출할 때 경고가 뜬다:<br>
`warning: [unchecked] Possible heap pollution from parameterized vararg type List<String>`<br>
**매개변수화 타입의 변수가 타입이 다른 객체를 참조하면 힙 오염이 발생한다.**<br>

**그런데도 불구하고 경고만 뜬다. 즉, 언어적으로 제네릭 형태의 가변인수를 허용하는 것이다.**<br>
이유는 간단하다. 이 기능이 굉장히 유용하기 때문이다.<br>
자바 라이브러리도 이런 메서드를 여럿 제공한다:
```java
// examples
Arrays.asList(T... a)
Collections.addAll(Collection<? super T> c, T... elements)
EnumSet.of(E first, E... rest)
```
이런 메서드를 작성할 때 자바 7 이전에는 `@SuppressWarnings("unchecked")` 애너테이션을 매번 달아야해서 번거로웠다.
그런데 자바 7 이후로는 `@SafeVarargs` 애너테이션으로 개발자가 메서드의 타입 안전성을 보장할 수 있다.
이 경우에는 가변인수로 전달받는 데이터가 제네릭 배열로 만들어진다는 것을 인지하고, **읽기 전용으로만 사용하면 된다.**<br>

배열인 가변인자 매개변수를 List 매개변수로 바꿀 수도 있다. 다음과 같이 코드를 작성하는 것이다:
```java
static <T> List<T> flatten(List<List<? extends  T>> lists) {
    List<T> result = new ArrayList<>();
    for (List<? extends T> list : lists) {
        result.addAll(list);
    }
    return result;
}
```
그리고 `List.of`를 활용해서 임의 개수의 인수를 넘길 수 있다. `List.of`에도 `@safeVarargs` 애너테이션이
있어서 가능한 일이다.
```java
List<String> stringList = flatten(List.of("pk", "joanne"));
```
