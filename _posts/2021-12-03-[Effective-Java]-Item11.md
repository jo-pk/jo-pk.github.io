---
layout: post
title: "[Effective Java] Item11. equals를 재정의하려거든 hashCode도 재정의하라."
description: 은근 많은 걸 요구한다...
categories: self
---

Author: seovalue

> 아이템 11. equals를 재정의하려거든 hashCode도 재정의하라.

equals를 재정의 할 때 hashcode도 반드시 재정의해야한다. 재정의한 hashCode는 일반 규약을 따라야하며, 서로 다른 인스턴스라면 되도록 해시코드도 서로 다르게 구현해야한다. 

hashMap에서는 tree를 사용한 Separate Chaning 방법을 사용한다.
* 참고: https://d2.naver.com/helloworld/831311

-----

Author: PK

Object 명세에는 다음과 같은 규약이 있다:

- equals 비교에 사용되는 정보가 변경되지 않았다면, 애플리케이션이 실행되는 동안 그 객체의 hashCode 메서드는 몇 번을 호출해도 일관되게 항상 같은 값을 반환해야 한다. 단, 애플리케이션을 다시 실행한다면 상관 없다.
- equals(Object)가 두 객체를 같다고 판단했다면, 두 객체의 hashCode는 똑같은 값을 반환해야 한다.
- equals(Object)가 두 객체를 다르다고 판단했더라도, 두 객체의 hashCode가 서로 다른 값을 반환할 필요는 없다. 단, 다른 객체에 대해서는 다른 값을 반환해야 해시테이블의 성능이 좋아진다.

그냥 간단하게 요약하면, hashCode는 같은 객체라면 같게, 다른 객체라면 웬만해선 다르게 나와야한다는 것.

하지만 hashCode를 제대로 재정의하지 않으면 두 번째 규약에 문제가 생긴다. 개발자는 필드 값을 보고 같은 객체일 것이라고 생각하고 사용했는데, 막상 JVM에서는 다른 객체로 인식하는 상황이 발생하는 것. 그러지 말자.

사실 웬만하면 IDE에서 hashCode를 알아서 재정의해준다. 심지어 lombok을 사용해서 재정의해줄 수도 있다.
