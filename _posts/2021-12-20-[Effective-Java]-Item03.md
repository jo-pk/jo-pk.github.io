---
layout: post
title: "[Effective Java] Item03. private 생성자나 열거 타입으로 싱글턴임을 보증하라"
description: 들어가며
categories: self
---

Author: seovalue

> 아이템 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라.

싱글턴(singleton)이란? 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말한다.

_(인스턴스에 대해서도 접은글로 설명을 덧붙여놓았다.)_

더보기

그렇다면 인스턴스란?

어떤 클래스에 속하는 각 객체를 인스턴스라고 한다. 좀 뜻이 모호한데, 좋은 예시를 찾다가 [해당 블로그](https://victor8481.tistory.com/280)에서 제시해주신 예시를 인용하여 설명을 보충해보자.

> 붕어빵을 만들려면 붕어빵을 찍기 위한 틀이 있어야한다. 틀이 준비되었다면 틀에 밀가루 반죽과 팥을 집어넣고 구워준다. 적당히 구워지면 틀에서 꺼낸다. 그로써 붕어빵이 탄생한다.
>
> 여기서 우리는 붕어빵틀을 **클래스**, 붕어빵을 **오브젝트**, 각각의 붕어빵을 **인스턴스**라고 빗댈 수 있으며, 붕어빵을 굽는 행위를 **인스턴스화 하다**라고 표현할 수 있다. 
>
> 붕어빵이라는 클래스는 밀가루 반죽과 팥이라는 멤버 변수를 갖고 있고, 굽다()라는 메서드를 갖고 있다. 이제 붕어빵틀에 밀가루 반죽과 팥을 집어넣어 붕어빵을 만들어보자. 각각의 붕어빵들은 붕어빵틀의 굽다() 라는 메서드를 통해 구워질 수 있다. 붕어빵이 구워지고... (인스턴스화 되고 있다) 붕어빵이 다 구워지면, 객체가 생성된 것이다!

싱글턴의 대표적인 예로는 함수와 같은 무상태(stateless) 객체나 설계 상 유일해야하는 시스템 컴포넌트를 들 수 있다.

싱글턴을 만드는 방식에는 두 가지가 있는데, 두 방식 모두 생성자는 private으로 감춰두고, 유일한 인스턴스에 접근할 수 있는 수단으로 public static 멤버를 하나 만들어둔다.

_이 게시글에서 작성된 코드는 모두 Effective Java 책에서 발췌하였다._

1\. public static final 필드 방식의 싱글턴

```
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis () { ... }
}
```

이 경우에서 private 생성자는 Elvis.INSTANCE를 호출할 때 딱 한번 호출된다. public 이나 protected 생성자가 없으므오 Elvis 클래스가 초기화될 때 만들어진 인스턴스가 전체 시스템에서 하나뿐임을 보장할 수 있다. 이 방식의 장점은 해당 클래스가 싱글턴임이 API에 명백히 드러나는 것이다. pulbic static 필드가 final이니 절대 다른 객체를 참조할 수 없다.

2\. 정적 팩터리 방식의 싱글턴

```
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    
    private Elvis() { ... }
    public static Elvis getInstance() {
        return INSTANCE;
    }
```

이 방식의 장점은 API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다는 점이다. 유일한 인스턴스를 반환하던 팩터리 메서드가 호출하는 스레드별로 다른 인스턴스를 리턴하도록 할 수 있다. 또한 원한다면 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다는 점이다. (제네릭 싱글턴 팩터리는 이후 다른 게시글로서 정리하겠다.) 또한 정적 팩터리 메서드의 메서드 참조를 공급자로 사용할 수 있다. [공급자(Supplier)와 관련하여 잘 정리된 게시글](https://m.blog.naver.com/zzang9ha/222087025042)이 있어 참고 링크로 걸어두었다.

위 두가지 방법으로 만든 싱글턴 클래스를 직렬화하기 위해서는 단순히 Serializable로 구현한다고 선언하는 것만으로는 부족한다. 모든 인스턴스 필드를 transient라고 선언하고, readResolve 메서드를 제공해야한다.

3\. 열거 타입 방식의 싱글턴 - 바람직한 방법

```
public enum Elvis {
    INSTANCE;
    ...
}
```

위 enum 방식을 활용한다면 직렬화, 리플렉션 공격에서도 제 2의 인스턴스가 생성되는 것을 완벽히 막아준다. 대부분의 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.

-----

Author: PK

싱글턴으로 상태가 없는 객체나 설계상 유일해야 하는 시스템 컴포넌트를 만든다.

하지만 인터페이스를 구현해서 만든 객체가 아니라면 mock 구현으로 대체할 수가 없어서 테스트가 어렵다.

싱글턴을 만드는 세 가지 방법:

1. public static final 필드 방식의 싱글턴

```java
public class Something {

  public static final Something INSTANCE = new Something();
  private Something() {}
  // ...
}
```

1. 정적 팩터리 방식의 싱글턴

```java
public class Something {

  private static final Something INSTANCE = new Something();
  private Something();

  public static Something getInstance() {
    return INSTANCE;
  }
}
```
두 번째 방법의 장점은 변경에 유연한 것. 첫 번째 방법의 장점은 싱글턴 인스턴스라는 것을 사용자가 명확하게 알고 사용한다는 점이다. 둘 다 직렬화 하려면 신경을 좀 써야한다. 그렇지 않으면 역직렬화 할 때 새로운 인스턴스가 생성된다.

1. enum 타입 방식의 싱글턴

```java
public enum Something {
  INSTANCE;

  public void doAction() {
  }
}
```

이 방법은 public static 필드 방식과 비슷한데, 더 간결하고 추가 노력 없이 직렬화가 가능하다. 심지어 아주 복잡한 직렬화 상황이나 리플렉션 공격에서도 제2의 인스턴스가 생기는 일을 완벽하게 막아준다. 조금 부자연스럽게 보일 수는 있어도 저자가 가장 바람직하다고 말하는 방식이다.
