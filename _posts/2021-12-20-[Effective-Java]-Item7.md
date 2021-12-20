---
layout: post
title: "[Effective Java] Item7. 다 쓴 객체 참조를 해제하라"
description: 들어가며
categories: self
---

Author: seovalue

> 아이템 7. 다 쓴 객체 참조를 해제하라

이 글에서 다루는 내용은, 말 그대로 다 쓴 객체 참조를 해제하라! 라는 것이다.

자바를 사용하다 보면 GC라는 것이 동작한다. 따라서 우리는 다른 C++과 같이 메모리 관리를 필요로하는 언어보다 좀 더 메모리를 신경쓰지 않고, 프로그래밍을 할 수 있다는 장점이 있다.

혹시라도 GC에 대해서 잘 모르는 독자가 있다면, [이 글](https://joanne.tistory.com/267)을 참조하길 바란다.

하지만, 우리가 신경쓰며 객체 참조를 해제해주어야할 때가 있다. 일반적으로 자기 메모리를 직접 관리하는 클래스라면 메모리 누수에 주의하여야한다. 예를 들어, 다음 코드에서 메모리 누수가 일어나는 위치를 찾아보자.

```
public class Stack {
    private Object[] elems;
    private int size;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;
    
    public Stack() {
        elems = new Object[DEFAULT_INITIAL_CAPACITY];
    }
    
    public void push(Object o) {
        ensureCapacity();
        elems[size++] = o;
    }
    
    public Object pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        return elems[--size];
    }
}
```

이 코드에서는 스택이 커졌다가 줄어들었을 때 스택에서 꺼내진 객체들을 가비지 컬렉터가 회수하지 않는다. 프로그램에서 그 객체들을 더 이상 사용하지 않더라도 말이다. 왜냐하면, 이 스택 내부에서 그 객체들의 다 쓴 참조를 여전히 가지고 있기 때문이다.

다 쓴 참조라는 것은 앞으로 쓰지 않을 참조를 뜻하기도 한다. 위 코드에서는 elems 배열의 활성 영역 밖의 참조들이 모두 다 쓴 참조에 해당한다.

따라서 가비지 컬렉션 언어에서는 이러한 경우 메모리 누수를 찾기가 매우 까다로워진다.

위와 같은 경우에는 pop 메서드를 다음과 같이 수정할 수 있다.

```
public Object pop() {
    if (size == 0) {
        throw new EmptyStackException();
    }
    Object result = elems[--size];
    elems[size] = null; // 다 쓴 참조를 해제한다.
    return result;
}
```
