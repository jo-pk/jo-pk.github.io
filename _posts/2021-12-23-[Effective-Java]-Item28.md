---
layout: post
title: "[Effective Java] Item28. 배열보다는 리스트를 사용하라"
description: 이 둘의 차이점은 무엇일까?
categories: self
---

> 아이템 28. 배열보다는 리스트를 사용하라

-----

Author: seovalue

이 또한 너무 자연스럽게 리스트를 사용하고 있었다.<br>
사실, Java로 코딩테스트를 하면서 처음으로 자바에서 배열을 사용해봤다.<br>
배열을 사용하게 되면 배열은 공변이기에 실체화된다. 따라서 배열은 런타임에 타입 안전하지 않다.<br>
하지만 컬렉션을 사용하게 되면 제네릭을 활용하기 때문에 컴파일타임에 타입을 체크하고, 런타임에 타입 안정성을 보장한다.<br>

즉, 런타임에 타입 안정성을 보장하기 위해서는 제네릭이 도입된 컬렉션을 사용하는 것이 안전하다.<br>


-----

Author: PK

### 차이점 1
`covariant`: 공변. **배열은 공변이다.**<br>
`invariant`: 불공변. **리스트는 불공변이다.**<br>

즉 `Sub`가 `Super`의 하위 타입이면 `Sub[]` 역시 `Super[]`의 하위 타입이다.<br>
그러나 `List<Sub>`와 `List<Super>`는 완전 별개다.
```java
Object[] objectArray = new Long[1]; // 여기서 에러가 터지지 않는다. 즉, 런타임에 실패한다.
objectArray[0] = "impossible"; // ArrayStoreException 발생

List<Object> objectList = new ArrayList<Long>(); // 컴파일되지 않는다.
```

### 차이점 2
`reify`: 실체화. **배열은 실체화된다.**<br>
런타임 때도 원소의 타입을 인지하고 있는 것. 그래서 위의 코드처럼 ArrayStoreException 이 발생한다.<br>
`erasure`: 소거. **제네릭은 타입 정보가 런타임에는 소거된다.**<br>
컴파일 시기에만 타입 정보가 사용되고 런타임 때는 전혀 쓰이지 않는다.
이와 같은 개념이 등장한 이유는 레거시 코드와 제네릭이 함께 쓰이도록 하기 위해서다. 자바 5 때 나왔다.<br>

위와 같은 이유로 배열은 제네릭을 사용해서 만들 수 없다. 바로 컴파일 에러 난다.<br>
```java
// 불가능한 코드.
List<String>[] stringLists = new List<String>[1]; // 이것부터가 불가능. 이것이 가능하다면 왜 문제가 발생할지 생각해보자.
List<Integer> intList = List.of(42);
Object[] objects = stringLists;
objects[0] = intList;
String s = stringLists[0].get(0);
// 제네릭은 런타임 때 소거된다는 점을 고려하면 답이 보일 것이다.
```

배열 대신 컬렉션을 사용하면 코드가 좀 복잡해지고 성능이 살짝 나빠질 수는 있어도
타입 안정성과 상호운용성이 좋아져서 오히려 좋다. 그것만으로도 가치가 있으니 웬만하면 리스트(컬렉션)을 활용하자.
