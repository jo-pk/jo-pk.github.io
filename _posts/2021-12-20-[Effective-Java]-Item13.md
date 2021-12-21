---
layout: post
title: "[Effective Java] Item13. clone 재정의는 주의해서 진행하라"
description: 들어가며
categories: self
---

Author: seovalue

> 아이템 13. clone 재정의는 주의해서 진행하라.

복제를 하게 되면 예상치 못한 위험이 발생할 수 있다. 예를 들어 복제를 했는데, 내부 가변 객체의 같은 주소값을 갖도록 복제된다면 복제한 객체의 필드를 변경했을 때 원본에도 영향을 미칠 수 있다.

-> 하지만 이 아이템은 아직 제대로 잘 이해를 못하겠다.. 
