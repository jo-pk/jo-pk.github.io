---
layout: post
title: "[Effective Java] Item34. int 상수 대신 열거 타입을 사용하라"
description: public static final 보다 좋아요
categories: self
---

Author: seovalue

> 아이템 34. int 상수 대신 열거 타입을 사용하라

-----

Author: PK

# Enum 타입
* 일정 개수의 상수 값 정의 후 다른 값은 허용하지 않는 타입.
* 정수 열거 패턴(int enum pattern)에는 단점이 있다: 타입 안전 보장할 수 없고 표현력도 안 좋음.
```java
public static final int PERSON_PK       = 1;
public static final int PERSON_JOANNE   = 2;
```
* 문자열 열거 패턴(string enum pattern) 역시 오타가 있을 수 있고, 문자열 비교로 인한 성능 저하가 발생한다.
```java
public static final String PERSON_PK        = "amazing";
public static final String PERSON_JOANNE    = "ehh"; 
```
** 그러니 필요한 원소를 컴파일타임에 다 알 수 있는 상수 집합이라면 Enum 타입을 사용하자.**<br>
**Java 에서 Enum 은 완전한 형태의 클래스라 다른 언어의 Enum 타입보다 훨씬 강력하다.**

## Enum 타입의 특징
* Enum 타입 자체는 클래스, 상수 하나당 해당 클래스의 인스턴스를 하나씩 만들어서 public static final 필드로 공개.
* 인스턴스는 딱 하나씩 존재하는 것이 보장됨. 개발자가 임의로 더 만들 수 없음. 즉, 싱글턴을 일반화한 형태라고 볼 수 있다.
* 타입 안전성 보장됨.
* 새로운 상수를 추가하거나 순서를 바꿔도 다시 컴파일하지 않아도 된다.
* 임의의 메서드나 필드 추가 가능, 인터페이스를 구현하게 할 수도 있다. Object 메서드들, Comparable, Serializable 이 구현되어 있다.
* Enum 타입 안에 정의된 상수들의 값을 배열에 담아 제공하는 `values()` 메서드를 제공한다.
* 사용하던 상수를 하나 제거해도, 그 상수를 참조하지 않던 클라이언트에게는 아무런 영향이 없다. 참조하던 클라이언트는 다시 컴파일 해야한다.

## Enum 타입 사용 방식
* 웬만하면 private or package-private 메서드로 구현하자.
* 널리 쓰이면 톱레벨 클래스로, 한 곳에서만 쓰인다면 멤버 클래스로.
* 상수별 클래스 몸체(constant-specific method implementation) 활용해서 로직을 넣자:
```java
public enum Operation {
    PLUS("+") {
        public double apply(double x, double y){return x + y;}
    },
    MINUS("-") {
        public double apply(double x, double y){return x - y;}
    },
    TIMES("*") {
        public double apply(double x, double y){return x * y;}
    },
    DIVIDE("/") {
        public double apply(double x, double y){return x / y;}
    };
    
    private final String symbol;
    
    Operation(String symbol) {
        this.symbol = symbol;
    }
    
    public abstract double apply(double x, double y);
    
    @Override
    public String toString() {
        return symbol;
    }
}
```

## 전략 열거 타입 패턴
기존 열거 타입에 상수별 동작을 혼합해 넣을 때 사용하는 패턴
```java
enum PayrollDay {
    MONDAY(WEEKDAY), TUESDAY(WEEKDAY), WEDNESDAY(WEEKDAY),
    THURSDAY(WEEKDAY), FRIDAY(WEEKDAY),
    SATURDAY(WEEKEND), SUNDAY(WEEKEND);
    
    private final PayType payType;
    
    PayrollDay(PayType payType) {
        this.payType = paytype;
    }
    
    int pay (int minutesWorked, int payRate) {
        return payType.pay(minutesWorked, payRate);
    }
    
    enum PayType {
        WEEKDAY {
            int overtimePay(int mins, int payRate) {
                return minsWorked <= MINS_PER_SHIFT ? 0 : (minsWorked - MINS_PER_SHIFT) * payRate / 2;
            }
        },
        WEEKEND {
            int overtimePay(int mins, int payRate) {
                return minsWorked * payRate / 2;
            }
        };
        
        private static final int MINS_PER_SHIFT = 8 * 60;
        
        abstract int overtimePay(int mins, int payRate);
        
        int pay(int minsWorked, int payRate) {
            int basePay = minsWorked * payRate;
            return basePay + overtimePay(minsWorked, payRate);
        }
    }
}
```
