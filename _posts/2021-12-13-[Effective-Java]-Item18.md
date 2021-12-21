---
layout: post
title: "[Effective Java] Item18. 상속보다는 컴포지션을 사용하라"
description: 들어가며
categories: self
---

Author: seovalue

> 아이템 18. 상속보다는 컴포지션을 사용하라.

메서드 호출과 달리 상속은 캡슐화를 깨트릴 "수" 있다. 예를 들어 부모 클래스의 구현이 변경되었을 때 하위 클래스까지 그 영향이 전파될 수 있다. HashSet의 예로서 HashSet의 addAll 메서드는 내부적으로 add를 사용하고 있는데, 만약 상속받은 클래스에서 add를 Override해서 다르게 구현한다면 하위클래스에서 addAll을 호출했을 때 예상과 다른 결과를 얻을 수 있다. 

따라서 이러한 문제를 해결하기 위해 기존 클래스를 확장하는 대신, 새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조하게 하는 방법을 컴포지션이라고 한다. 이는 forwarding방식으로 기존 클래스의 대응하는 메서드를 호출해 그 결과를 반환한다.

1. 새로운 클래스는 기존 클래스의 내부 구현 방식의 영향에서 벗어난다.
2. 기존 클래스에서 새로운 메서드가 추가되더라도 전혀 영향받지 않는다.


상속은 하위 클래스가 상위 클래스의 진짜 하위 타입인 상황, 즉 is-a 관계일 때만 사용해야한다. 상속을 고려하고 있는 하위 클래스에 대해 이 하위 클래스가 정말 상위 클래스인가? 를 고려해보자.

-----

Author: PK

해당 아이템은 인터페이스를 상속하는 것과는 무관하다.<br>

'상속보다는 조합을 사용하라'<br>
사람들이 흔히 하는 말이다.<br>

그리고 그 이유로 다음과 같이 말한다:
> 상속은 캡슐화를 해친다. 즉, 부모 클래스의 변경이 자식 클래스의 오작동으로 이어질 수 있다.

그리고 다음과 같은 조언을 준다:
> 명확하게 is-a 관계일 때만 상속을 쓰자.

...그리고 이 말로 그 뒤를 잇는다:
> is-a 관계라도 상속은 위험하다. 부모 클래스의 설계 방법에 따라, 또는 부모와 자식 클래스가 서로
> 같은 패키지에 있는지에 따라 위험성이 올라가기 때문이다.

알기 쉽게 예시를 통해 확인하자. (책의 예시를 그대로 가지고 왔다)
```java
public class PkHashSet<E> extends HashSet<E> {
    
    private int addCount = 0;
    
    // 생성자...
    
    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }
    
    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll();
    }
    
    // ...
}
```
위의 코드에서 `addAll(List.of("1", "2" "3"))` 과 같이 메서드를 실행하면
컬렉션의 크기가 3이 아닌 6이 된다. `addAll()` 메서드에서 addCount 값을 올리고,
`HashSet 의 addAll()` 이 `add()`를 사용해서 구현되었기 때문이다. 우리는 `add()` 를
오버라이드했기 때문에, `super.addAll()` 이 호출되었을 때 addCount 값이 다시 올라간다.<br>

*이처럼 자신의 다른 부분을 사용하는 '자기사용' 여부는 해당 클래스의 내부 구현 방식에 해당하며,
자바 플랫폼 전반적인 정책인지, 그래서 다음 릴리스에서도 유지될지는 알 수 없다.
따라서 이런 가정에 기댄 클래스는 깨지기 쉽다.*

그래서 **포워딩** 방식을 사용한다.<br>
새로운 클래스를 만들고 private 필드로 기존 클래스를 참조하는 방식이다.<br>
새로 생긴 클래스이 메서드들을 전달 메서드라고 부른다. 그리고 이 포워드 방식이 바로
상속보다는 조합을 사용하는 방법이다.
```java
public class ForwardingSet<E> implements Set<E> {
    private final Set<E> s;
    
    public ForwardingSet(Set<E> s) {
        this.s = s;
    }
    
    public void clear() {
        s.clear();
    }
    
    public boolean contains(Object o) {
        return s.contains(o);
    }
    
    public boolean isEmpty() {
        return s.isEmpty();
    }
    
    public int size() {
        return s.size();
    }
    
    public Iterator<E> iterator() {
        return s.iterator();
    }
    
    public boolean add(E e) {
        return s.add(e);
    }
    
    public boolean remove(Object o) {
        return s.remove(o);
    }
    
    public boolean containsAll(Collection<? extends E> c) {
        return s.addAll(c);
    }
    
    public boolean removeAll(Collection<? extends E> c) {
        return s.removeAll(c);
    }
    
    public boolean retainAll(Collection<? extends E> c) {
        return s.retainAll(c);
    }
    
    public Object[] toArray() {
        return s.toArray();
    }
    
    public <T> T[] toArray(T[] a) {
        return s.toArray(a);
    }
    
    @Override
    public boolean equals(Object o) {
        return s.equals(o);
    }
    
    @Override
    public int hashCode() {
        return s.hashCode();
    }
    
    @Override
    public String toString() {
        return s.toString();
    }
}
```
```java
public class PkHashSet<E> extends ForwardingSet<E> {
    private int addCount = 0;

    public PkHashSet(Set<E> s) {
        super(s);
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }
}
```
물론 포워딩 방식 역시 단점이 있긴하다.
바로 콜백(callback) 프레임워크와는 어울리지 않는다는 점이다.
콜백 프레임워크에서는 자기 자신의 참조를 다른 객체에 넘겨서 다음 호출 때 사용하도록 한다.
*내부 객체는 자신을 감싸고 있는 래퍼의 존재를 모르니 대신 자신(this)의 참조를 넘기게 되고,
콜백 때는 래퍼가 아닌 내부 객체를 호출하게 된다.*
**이를 SELF 문제라고 한다.**<br>

참고로 guava 라이브러리는 모든 컬렉션 인터페이스용 전달 메서드를 전부 구현해뒀다.<br>
아 그리고 래퍼 객체가 차지하는 메모리는 걱정할 필요가 없다.
프로덕션 환경에서는 그게 크게 고민할 요소가 아닐만큼 사소하기 때문이다.
