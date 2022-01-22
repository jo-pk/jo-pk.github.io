---
layout: post
title: "[Effective Java] Item50. 적시에 방어적 복사본을 만들라"
description: 방어는 최고의 공격이다
categories: self
---

> 아이템 50. 적시에 방어적 복사본을 만들라

-----

Author: PK

방어적 복사를 말할 때 단골로 등장하는 유명한 api 가 있다.<br>
바로 `Date` api. 자바 8 이 나오면서 `Instant` 나 `LocalDateTime` api 가 대신 사용된다.<br>
이 `Date` api 를 보면 왜 방어적 복사가 중요한지 알 수 있다:
```java
public final class Period {
    private final Date start;
    private final Date end;
    
    public Period(Date start, Date end) {
        if (start.compartTo(end) > 0) {
            throw new IllegalArgumentException();
        }
        this.start = start;
        this.end = end;
    }
    
    public static void main(String[] args) {
        Date start = new Date();
        Date end = new Date();
        Period period = new Period(start, end);
        end.setYear(2049);
    }
}

```
위의 예시를 보면 `Period` 객체를 불변으로 만들기 위한 시도가 있었음에도 불구하고<br>
외부에서 필드 값을 변경할 수 있다는 큰 문제가 있다.<br>
따라서 다음과 같이 `Period` 클래스의 생성자 안에서 방어적으로 값을 복사해야 한다:
```java
public final class Period {
    private final Date start;
    private final Date end;
    
    public Period(Date start, Date end) {
        this.start = new Date(start.getTime());
        this.end = new Date(end.getTime());
        
        if (this.start.compareTo(this.end) > 0) {
            throw new IllegalArgumentException();
        }
    }
}
```
방어적 복사를 한 뒤에 유효성 검증을 하는 것이 굉장히 부자연스럽게 보일 수 있다.<br>
그러나 멀티스레딩 환경에서는 유효성 검증을 한 뒤, 복사를 하려는 그 찰나에 외부에서 값이<br>
바뀔 수 있다는 위험이 있다. 이를 TOCTOU (time of check / time of use) 공격이라고 한다.<br>

그리고 매개변수가 제3자에 의해 확장될 수 있는 타입이라면 방어적 복사본을 만들 때 `clone` 을 사용해서는 안 된다.<br>
`clone` 이 악의를 가진 하위 클래스의 인스턴스를 반환할 수도 있기 때문이다.<br>

필드 값을 외부로 반환할 때 역시 방어적 복사를 해준다:
```java
public Date start() {
    return new Date(this.start.getTime());
}

public Date end() {
    return new Date(this.end.getTime());
}
```
이렇게 하면 `Period` 클래스 외에 외부의 다른 요소가 필드 값을 변경할 수 없기 때문에<br>
완벽하게 캡슐화 되었다고 할 수 있다.<br>
접근자 메서드에서는 `clone` 을 사용해도 무방하다<br>
그것이 확실하게 내가 원하는 클래스임을 알기 때문이다.<br>
하지만 인스턴스를 복사할 때는 생성자나 정적 팩터리 메서드가 가장 좋다.
