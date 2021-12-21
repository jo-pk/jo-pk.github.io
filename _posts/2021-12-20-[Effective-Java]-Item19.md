---
layout: post
title: "[Effective Java] Item19. 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라"
description: 들어가며
categories: self
---

Author: seovalue

> 아이템 19. 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라.

상속용 클래스를 설계하는 경우에는 클래스 내부에서 스스로를 어떻게 사용하는 지 모두 문서로 남겨야한다. 또한 상속을 허용하는 경우 효율 좋은 하위 클래스를 만들 수 있도록 몇가지 메서드를 protected로 제공해야한다. 
