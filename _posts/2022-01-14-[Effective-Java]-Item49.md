---
layout: post
title: "[Effective Java] Item49. 매개변수가 유효한지 검사하라"
description: 돌다리도 두들겨 보고 건너라
categories: self
---

> 아이템 49. 매개변수가 유효한지 검사하라

-----

Author: PK

해당 item 은 library 를 만드는 상황에 적절한 팁이라고 생각하며 흡수하면 좋을 것 같다.<br>

"오류는 가능한 빨리 발생한 곳에서 잡아라"<br>
메서드는 최대한 범용적으로 설계하되, 해당 메서드가 의도된 역할을 수행하지 못하는 상황을 막아야 한다.<br>
따라서 그런 상황이 발생하는 경우를 방지하는 유효성 검사를 메서드 초반부에 실행해야 한다.<br>

public 또는 protected 메서드라면 상황에 적절한 예외를 던지고 그 내용을 문서화 해야한다.<br>
private 메서드라면 해당 메서드를 활용하는 것은 api 제작자인 나 자신 뿐이므로 파라미터에<br>
유효한 값이 올 것이라는 사실을 어느정도 보장할 수 있다. 그렇기 때문에 `assert` 를 사용해도 좋다.<br>
`assert` 는 런타임에 아무런 효과도, 성능저하도 없다는 이점이 있다.<br>

생성자 역시 해당 item 이 말하는 '메서드'에 해당한다.<br>
생성자에서 파라미터를 검증하는 것은 클래스 불변식을 어기는 객체가 만들어지지 않도록 하기 위해 꼭 필요한 요소라고 한다.<br>

그러나 로직상 검증이 저절로 되는 상황에서는 굳이 앞부분에 추가적인 검증을 하지 않는 것이 좋다.