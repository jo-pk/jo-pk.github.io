---
layout: post
title: "[Effective Java] Item15. 클래스와 멤버의 접근 권한을 최소화하라"
description: 들어가며
categories: self
---

Author: seovalue

> 아이템 15. 클래스와 멤버의 접근 권한을 최소화하라.

모든 클래스와 멤버의 접근성을 가능한 한 좁히자. 패키지를 외부에서 쓸 일이 없다면 package-private 접근 제어자를 사용하자. 멤버 변수는 최대한 private으로 사용하자. public 클래스의 인스턴스 필드는 되도록 public이 아니어야하며, public 가변 필드를 갖는 public 클래스는 스레드 안전하지 않다.
