---
layout: post
title: "[Effective Java] Item12. toString을 항상 재정의하라"
description: 들어가며
categories: self
---

Author: seovalue

> 아이템 12. toString을 항상 재정의하라.

toString을 재정의해서, 원하는 정보를 담도록 하면 훨씬 디버깅하기 쉽다. 상위 오브젝트의 toString을 사용하게 된다면, 예를 들어 PhoneNumber@abcdd 와 같은 출력 형태를 볼 수 있을 것이다. 하지만, toString에 그 객체가 가진 주요 정보를 모두 반환하게 해둔다면 디버깅에 아주 효율적일 것이다.  
ex. {약물 #9: 유형=사랑, 냄새=테레빈유, 겉모습=먹물}
