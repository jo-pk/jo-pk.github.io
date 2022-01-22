---
layout: post
title: "[Effective Java] Item55. 옵셔널 반환은 신중히 하라"
description: 성능 고려를 하주자
categories: self
---

> 아이템 55. 옵셔널 반환은 신중히 하라

-----

Author: PK

Optional 을 반환하는 곳에서 null 을 반환하면 Optional 의 역할을 완전히 무시하는 행위다.
