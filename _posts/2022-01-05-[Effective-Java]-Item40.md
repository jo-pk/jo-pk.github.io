---
layout: post
title: "[Effective Java] Item40. @Override 애너테이션을 일관되게 사용하라"
description: 꼭 붙이자
categories: self
---

> 아이템 40. @Override 애너테이션을 일관되게 사용하라

-----

Author: seovalue

`@Override` 애너테이션을 통해 이 메서드가 재정의된 메서드임을 명시적으로 나타낼 수 있다고 생각한다. 불필요하지 않은 것을 생략하는 것은 실수를 유발할 수 있다.


-----

Author: PK

상위 클래스 메서드를 재정의하려는 모든 메서드에는 `@Override` 애너테이션을 다는 것이 실수를 줄이는 방법이다.<br>
추상 메서드를 구현할 때는 굳이 붙이지 않아도 된다고 저자는 말하지만, 나는 그것이 추상 메서드를 구현하는 것이라는 것을 코드 상에서 나타내는 것이
좋다고 생각해서 다는 것을 선호한다.<br>
