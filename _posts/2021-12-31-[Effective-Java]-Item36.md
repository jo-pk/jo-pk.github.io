---
layout: post
title: "[Effective Java] Item36. 비트 필드 대신 EnumSet을 사용하라"
description: 비트에 몸을 맡기지 말자
categories: self
---

> 아이템 36. 비트 필드 대신 EnumSet을 사용하라


-----

Author: seovalue

비트 필드를 사용해 본 적이 없다.<br>
다행인 걸까?<br>

비트 필드 대신 EnumSet을 활용하라고 되어있다.<br>
EnumSet은 List.of 처럼 `EnumSet.of(Enum.PK, Enum.JOANNE)`와 같이 사용할 수 있다.<br>
EnumSet은 비트 필드 수준의 명료함과 성능을 제공하며, 열거 타입의 장점까지 제공하기 때문이다.<br>

```text
EnumSet의 특징
1. enum 클래스로 동작하기 위해 특화된 Set 컬렉션.
2. Set이기 때문에 데이터 중복 저장 불가, 순서 보장 안됨.
3. EnumSet의 내부는 비트 벡터로 구현되어있음.
4. 열거형 값만 포함할 수 있음.
5. null 안됨.
6. thread-safe 하지 않음.
``` 


-----

Author: PK

# 비트 필드
```java
public class Text {
    public static final int STYLE_BOLD          = 1 << 0 // 1
    public static final int STYLE_ITALIC        = 1 << 1 // 2
    public static final int STYLE_UNDERLINE     = 1 << 2 // 4
    public static final int STYLE_STRIKETHROUGH = 1 << 3 // 8
    
    public void applyStyles(int styles) { /*...*/ }
}
```
비트 필드를 사용하면 비트별 연산을 사용해 합집합과 교집합 같은 집합 연산을 효율적으로 할 수 있다.<br>
하지만 비트 필드는 열거 상수의 단점을 그대로 갖고 있다:
* 비트 필드 값은 출력했을 때 해석하기 어렵다.
* 비트 필드 하나에 녹아 있는 모든 원소를 순회하기 까다롭다.
* 최대 몇 비트가 필요한지 API 작성시 미리 예측하여 적절한 타입(보통 int 또는 long)을 선택해야 한다.

# EnumSet
```java
public class Text {
    public enum Style { BOLD, ITALIC, UNDERLINE, STRIKETHROUGH }
    
    // EnumSet 이 거의 매번 사용된다 하더라도 웬만하면 인터페이스를 사용하는 것이 바람직하다.
    public void applyStyles(Set<Style> styles) { /*...*/ }
}
```
```java
text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));
```
* 타입 안전하다.
* 비트 백터로 구현되어 원소가 총 64개 이하라면 long 변수 하나로 표현하여 비트 필드에 비견되는 성능을 보여준다.

## 특징
* 불변 EnumSet 을 만들 수 없다 (Java 11까지도 그 개선이 이루어지지 않았다.)
* 그러나 Google 의 Guava 라이브러리에서는 제공을 해준다. (결국 EnumSet 을 이용해서 만들었기 때문에 성능상 손해가 있긴 하다.)
* `Collections.unmodifiableSet` 으로 감싸서 사용할 수 있긴 하다.
