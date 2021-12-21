---
layout: post
title: "[Effective Java] Item16. public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라"
description: 들어가며
categories: self
---

Author: seovalue

> 아이템 16. public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라.


필드를 public으로 노출하는 것이 아닌, getter와 같은 접근자 메서드를 사용하자. 특히 가변의 경우에는 방어적 복사등을 접근자 메서드를 통해 수행할 수 있다.
