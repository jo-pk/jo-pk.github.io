---
layout: post
title: "[Effective Java] Item43. 람다보다는 메서드 참조를 사용하라"
description: 간결함의 끝판왕 메서드 참조
categories: self
---

> 아이템 43. 람다보다는 메서드 참조를 사용하라

-----

Author: PK

람다를 사용하다보면 IDE가 자동으로 그것을 감지하고 메서드 참조를 사용하라고 권할 것이다. 그러니 그것을 사용하는 것이 이득이다.<br>

### 메서드 참조를 사용하면 안되는 상황
하지만 그렇다고 해서 항상 메서드 참조를 사용하면 되는 것이 아니다.<br>
예를 들어, 사용하려는 람다가 같은 클래스에 있을 때가 그렇다:
```java
service.execute(FreakingLongClassNameForThisExample::action);
// 위의 코드는 다음과 같이 람다로 다시 변경할 수 있다:
service.execute(() -> action());
```

### 메서드 참조 유형
| 메서드 참조 유형 | 예                      | 같은 기능을 하는 람다                                       |
|--------|------------------------|----------------------------------------------------|
| 정적     | Integer::parseInt      | str -> Integer.parseInt(str)                       | 
| 한정적 (인스턴스) | Instant.now()::isAfter | Instant then = Instant.now(); t -> then.isAfter(t) |
| 비한정적 (인스턴스) | String::toLowerCase    | str -> str.toLowerCase()                           | 
| 클래스 생성자 | TreeMap<K,V>::new      | () -> new TreeMap<K,V>()                           | 
| 배열 생성자 | int[]::new             | len -> new int[len]                                |
