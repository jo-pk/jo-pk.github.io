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

-----

Author: PK

메모리 누수는 잘 발견되지 않는다. 지금 돌아가고 있는 소프트웨어도 힙 프로파일러와 같은 도구를 동원해서 디버깅을 해야 발견되는 경우도 많다.

가비지 컬렉션 언어에서는 메모리 누수를 찾기 매우 까다롭다. 그런데 불필요한 객체를 하나 살려두면 그 객체 뿐만 아니라 그 객체가 참조하고 있는 다른 객체도 회수할 수 없다. 이는 장기적으로 성능에 악영향을 끼친다.

- 그렇다고 객체를 다 사용한 경우에 null로 바꾸는 일은 하지 말자. 코드도 더러워지고, null 처리는 예외적인 경우여야 한다.
- null 처리는 컬렉션에 사용되지 않는 공간이 발생할 때 한다. 가비지 컬렉터가 그 공간이 사용되는지 안되는지 알 수 없기 때문이다.
- 캐시가 특히 메모리 누수의 주범이다. 캐싱해두고 정작 그 데이터를 사용하지 않으며 계속 방치해 두는 것이다. 이럴 때를 대비하여 WeakHashMap을 사용하여 캐시를 만들 수 있다. 다 쓴 엔트리는 그 즉시 자동으로 제거된다. 주의해야 할 점은 WeakHashMap이 이럴 때만 유용하다는 것이다.
- listener와 callback 역시 메모리 누수의 원인이다. 등록만 하고 사용하지 않는 경우가 있기 때문이다. 이 역시 콜백을 weak reference(약한 참조)로 저장하면 가비지 컬렉터가 즉시 수거한다. weak reference로 저장하는 한 가지 예시가 바로 WeakHashMap을 사용하는 것이다.
