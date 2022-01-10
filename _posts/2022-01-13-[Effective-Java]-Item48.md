---
layout: post
title: "[Effective Java] Item48. 스트림 병렬화는 주의해서 적용하라"
description: 동시성은 늘 어렵다
categories: self
---

> 아이템 48. 스트림 병렬화는 주의해서 적용하라

-----

Author: PK

```java
public static void main(String[] args) {
    primes().map(p -> TWO.pow(p.intValueExact()).subtract(ONE))
        .filter(mersenne -> mersenne.isProbablePrime(50))
        .limit(20)
        .forEach(System.out::println);
}

static Stream<BigInteger> primes() {
    return Stream.iterate(TWO, BigInteger::nextProbablePrime);
}
```
해당 스트림을 더 빨리 돌리기 위해서 `parallel()` 중간 연산을 포함시켰다고 해보자.<br>
그러면 파이프라인을 병렬화하는 방법을 스트림 라이브러리가 찾지 못해서 연산이 매우 많이 느려진다. (응답 불가 상태. liveness failure)<br>
데이터 소스가 `Stream.iterate` 이거나 중간 연산으로 `limit` 을 사용하면 파이프라인 병렬화로는 성능 개선을 기대할 수 없다.<br>
그리고 이전 연산의 결과가 다음 연산에 필요하다면, 병렬적으로 계산하는게 오히려 더 오래 걸릴 수 있다. 위의 연산이 대표적인 예시다.

* 스트림의 소스가 ArrayList, HashMap, HashSet, ConcurrentHashMap 의 인스턴스거나 배열, int 범위, long 범위일 때 병렬화의 효과가 가장 좋다. 데이터를 쉽게 나눌 수 있어서 다수의 스레드에 분배하기 좋다는 특징이 있기 때문이다.
* 그리고 참조 지역성(locality of reference)이 좋다는 이유도 있다. 메모리에 연속적으로 저장되어 가지고 오는 속도가 빠르기 때문이다.
* 종단 연산이 오래걸리고 순차적인 연산이라면 병렬 처리에 영향을 준다. 병렬화에 가장 적합한 것을 축소다. (reduction)
* 가변 축소(mutable reduction)는 오히려 병렬화에 적합하지 않다.
