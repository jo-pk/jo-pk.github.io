---
layout: post
title: "[Effective Java] Item11. equals를 재정의하려거든 hashCode도 재정의하라."
description: 들어가며
categories: self
---

Author: seovalue

> 아이템 11. equals를 재정의하려거든 hashCode도 재정의하라.

equals를 재정의 할 때 hashcode도 반드시 재정의해야한다. 재정의한 hashCode는 일반 규약을 따라야하며, 서로 다른 인스턴스라면 되도록 해시코드도 서로 다르게 구현해야한다. 

hashMap에서는 tree를 사용한 Separate Chaning 방법을 사용한다.
* 참고: https://d2.naver.com/helloworld/831311
