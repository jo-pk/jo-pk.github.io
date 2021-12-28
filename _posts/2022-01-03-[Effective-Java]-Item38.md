---
layout: post
title: "[Effective Java] Item38. 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라"
description: Enum도 인터페이스를 적용할 수 있다
categories: self
---

> 아이템 38. 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라

-----

Author: seovalue

열거 타입의 확장이 필요하다는 것은, 상수로서의 사용에서 벗어났다는 생각이 든다.<br>
확장할 수 있는 열거 타입이 필요하다면 객체로 분리하는 게 더 낫지 않을까? <br>

-----

Author: PK

이 아이템은 인터페이스를 이용해서 Enum 을 만든 뒤, 그것을 사용하는 예시를 보여준다.
```java
public interface Operation {
    double apply(double x, double y);
}
```
```java
public enum BasicOperation implements Operation {
    PLUS("+") {
        @Override
        public double apply(double x, double y) {
            return x + y;
        }
    },
    MINUS("-") {
        @Override
        public double apply(double x, double y) {
            return x - y;
        }
    },
    TIMES("*") {
        @Override
        public double apply(double x, double y) {
            return x * y;
        }
    },
    DIVIDE("/") {
        @Override
        public double apply(double x, double y) {
            return x / y;
        }
    };
    
    private final String symbol;
    
    BasicOperation(String symbol) {
        this.symbol = symbol;
    }
    
    @Override
    public String toString() {
        return symbol;
    }
}
```
```java
public enum ExtendedOperation implements Operation {
    EXP("^") {
        @Override
        public double apply(double x, double y) {
            return Math.pow(x, y);
        }
    },
    REMAINDER("%") {
        @Override
        public double apply(double x, double y) {
            return x % y;
        }
    };

    private final String symbol;

    BasicOperation(String symbol) {
        this.symbol = symbol;
    }

    @Override
    public String toString() {
        return symbol;
    }
}
```
```java
// 활용 1
private static <T extends Enum<T> & Operation> void test(Class<T> opEnumType, double x, double y) {
    for (Operation op : opEnumType.getEnumConstants()) {
        System.out.println("%f %s %f = %f%n", x, op, y, op.apply(x, y));
    }
}
// 활용 2
private static void test(Collection<? extends Operation> opSet, double x, double y) {
    for (Operation op : opSet) {
    System.out.println("%f %s %f = %f%n", x, op, y, op.apply(x, y));
    }
}
```
이렇게 인터페이스를 사용하면 열거 타입끼리 구현을 상속할 수 없으므로 중복 코드가 발생할 수 있긴 하다.<br>
공유하는 부분이 많다면 도우미 클래스나 정적 도우미 메서드로 분리해보자.
