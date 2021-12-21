---
layout: post
title: "[Effective Java] Item16. public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라"
description: 귀찮지만 장점이 많다
categories: self
---

Author: seovalue

> 아이템 16. public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라.


필드를 public으로 노출하는 것이 아닌, getter와 같은 접근자 메서드를 사용하자. 특히 가변의 경우에는 방어적 복사등을 접근자 메서드를 통해 수행할 수 있다.

-----

Author: PK

클래스의 필드는 사실상 언제나 private 으로 선언하는 것이 개발자의 습관이다. (아니면 지금부터 습관화 하자 ㅎㅎ)<br>

그렇다고 필드에 final 처리를 한 뒤, public 접근제한자를 붙여도 좋은 것이 아니다.
필드를 접근자 없이 사용하면 부가작업(예: 방어적 복사)을 못 하게 되고, API 를 변경하지 않고서는 표현 방식을 바꿀 수 없다.<br>

해당 아이템은 그러려니하고 읽고 있었는데, 저자가 다음과 같은 말을 했다:<br>
`package-private 클래스 혹은 private 중첩 클래스라면 데이터 필드를 노출한다 해도 하등의 문제가 없다.`<br>
즉, 다음과 같은 형태도 OK 라는 것이다.
```java
class ThisIsOK {

    public String name;
    public int num;
    
    // ...
}
```
코드 면에서 접근자(getter, setter)보다 깔끔하고, 어차피 해당 클래스를 사용하는 클라이언트 역시 같은
패키지 내의 클래스라는 것이 그 이유다. private 중첩 클래스라면 특히 private 으로 지정해도 접근 가능하다.<br>

Java 플랫폼 라이브러리에도 public 클래스의 필드를 직접 노출시키는 경우가 있다고 한다.<br>
대표적으로 `java.awt.package`의 `Point`와 `Dimension` 클래스다.<br>
물론 이 때문에 `Dimension` 패키지의 심각한 성능 문제는 오늘날에도 해결하지 못했다고 한다.<br>

이 부분은 조앤이랑 한 번 얘기해봐야겠다. 팀의 컨벤션을 따라가는 것이 제일 좋을 것 같기도 하고.
