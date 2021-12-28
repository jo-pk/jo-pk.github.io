---
layout: post
title: "[Effective Java] Item37. ordinal 인덱싱 대신 EnumMap을 사용하라"
description: ordinal은 웬만해선 사용하면 안돼
categories: self
---

Author: seovalue

> 아이템 37. ordinal 인덱싱 대신 EnumMap을 사용하라

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
