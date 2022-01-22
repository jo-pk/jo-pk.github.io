---
layout: post
title: "[Effective Java] Item53. 가변인수는 신중히 사용하라"
description: 성능 고려를 하주자
categories: self
---

> 아이템 53. 가변인수는 신중히 사용하라

-----

Author: PK

인수 개수가 일정하지 않은 메서드를 정의해야 한다면 가변인수가 반드시 필요하다<br>
메서서드를 정의할 때 필드 매개변수 앞에 두고, 가변인수를 사용할 때는 성능 문제까지 고려하자.
