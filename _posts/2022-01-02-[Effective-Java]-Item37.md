---
layout: post
title: "[Effective Java] Item37. ordinal 인덱싱 대신 EnumMap을 사용하라"
description: ordinal은 웬만해선 사용하면 안돼
categories: self
---

> 아이템 37. ordinal 인덱싱 대신 EnumMap을 사용하라

-----

Author: seovalue

드디어 사용해본 적이 있는 친구가 나왔다. <br>
앞서 `ordinal()`을 사용하지 말라고 했던 것을 기억할 것이다.<br>
`EnumMap`은 다음과 같은 특징이 있다.<br>

```text
EnumMap의 특징

1. Enum을 키로 사용한다.
2. 내부적으로 데이터를 저장할 때 배열을 사용한다.
3. 기본 연산이 모두 상수 시간 안에 가능하다.
4. 캐싱을 통한 성능 향상 가능
5. equals 연산 수행 시 내부 key, value가 모두 일치하는지 전체 순회한다.
6. Thread-safe 하지 않다. Collections.synchronizedMap으로 감싸야한다.
```

근데, HashMap도 있는데 굳이.... EnumMap?<br>

#### HashMap과 Put 메서드 비교
```java
// EnumMap
public V put(K key, V value) {
    typeCheck(key);

    int index = key.ordinal();
    Object oldValue = vals[index];
    vals[index] = maskNull(value);
    if (oldValue == null)
        size++;
    return unmaskNull(oldValue);
}

// HashMap
너무 길어서 적지를 못하겠다.
```

1. EnumMap은 키의 선언 순서인 ordinal을 인덱스로 사용한다.
2. HashMap은 키의 해시값을 인덱스로 사용한다.

<br>
결론: 어쨌든 EnumMap도 ordinal() 사용함. 그니깐 우리가 직접 ordinal() 쓰지 말고 EnumMap 쓰자.<br>


-----

Author: PK

배열이나 리스트에서 원소를 꺼낼 때 **굳이** `ordinal()` 메서드를 활용하여 인덱스를 주는 코드를 만들 수 있다.<br>
**하지 말자.** EnumMap 을 사용하면 훨씬 더 깔끔하게 만들 수 있다:
```java
Map<Plang.LifeCycle, Set<Plant>> plantsByLifeCycle = new EnumMap<>(Plant.LifeCycle.class);
for (Plant.LifeCycle lc : Plant.LifeCycle.values()) {
    plantsByLifeCycle.put(lc, new HashSet<>());
}
for (Plant p : garden) {
    plantsByLifeCycle.get(p.lifeCycle).add(p);
}
System.out.println(plantsByLifeCycle);
```
