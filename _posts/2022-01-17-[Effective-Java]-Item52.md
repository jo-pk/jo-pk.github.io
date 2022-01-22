---
layout: post
title: "[Effective Java] Item52. 다중정의는 신중히 사용하라"
description: 안 그러면 디버깅할 때 괴로워진다
categories: self
---

> 아이템 52. 메서드 시그니처를 신중히 설계하라

-----

Author: PK

```java
public class CollectionClassifier {
    public static String classify(Set<?> s) {
        return "집합";
    }
    public static String classify(List<?> list) {
        return "리스트";
    }
    public static String classify(Collection<?> collection) {
        return "그 외";
    }
    
    public static void main(String[] args) {
        Collection<?>[] collections = {
            new HashSet<String>(),
            new ArrayList<BigInteger>(),
            new HashMap<String, String>().values()
        };
        for (Collection<?> collection : collections) {
            System.out.println(classify(collection));
        }
    }
}
```
해당 코드를 실행해보면 "그 외"만 세 번 출력한다.<br>
컴파일 타임 때 어느 메서드가 실행될지 정해지는데, for 문 안의 collection 은 항상 `Collection<?>` 타입이기 때문이다.<br>
런타임 때는 타입이 매번 달라지지만 컴파일 타임 때는 아니다.<br>
**재정의한 메서드는 동적으로 선택되고, 다중정의한 메서드는 정적으로 선택된다.**
프로그램 언어가 다중정의를 허용한다고 해서 꼭 그것을 활용할 필요는 없다.
