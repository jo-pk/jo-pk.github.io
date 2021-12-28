---
layout: post
title: "[Effective Java] Item35. ordinal 메서드 대신 인스턴스 필드를 사용하라"
description: enum 활용하기
categories: self
---

Author: seovalue

> 아이템 35. ordinal 메서드 대신 인스턴스 필드를 사용하라

-----

Author: PK

# Ordinal
Enum 속 상수의 순서를 반환하는 메서드
```java
public enum Ensemble {
    SOLE, DUET, TRIO, QUARTET, QUINTET,
    SEXTET, SEPTET, OCTET, NONET, DECTET;
    
    public int numberOfMusicians() {
        return ordinal() + 1;
    }
}
```
위의 Enum 타입에서 상수의 순서를 바꾸거나 새로운 상수를 추가하면 `numberOfMusicians()` 메서드는 오작동한다.

그러니 그냥 다음과 같이 바꾸자. `ordinal()` 메서드는 유지보수 관점에서 매우 제한적인 메서드다.
```java
public enum Ensemble {
    SOLE(1), DUET(2), TRIO(3), QUARTET(4), QUINTET(5),
    SEXTET(6), SEPTET(7), OCTET(8), NONET(9), DECTET(10), TRIPLE_QUARTET(12);

    private int numberOfMusicians;
    
    Ensemble(int numberOfMusicians) {
        this.numberOfMusicians = numberOfMusicians;
    }
    
    public int numberOfMusicians() {
        return numberOfMusicians;
    }
}
```
Enum api 를 보면 ordinal 에 대해 이렇게 쓰여 있다:
**대부분 프로그래머는 이 메서드를 쓸 일이 없다.**<br>
이 메서드는 **EnumSet과 EnumMap 같이 열거 타입 기반의 범용 자료구조에 쓸 목적**으로 설계되었다.
