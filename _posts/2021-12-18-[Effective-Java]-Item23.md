---
layout: post
title: "[Effective Java] Item23. 태그 달린 클래스보다는 클래스 계층 구조를 활용하라"
description: 들어가며
categories: self
---

Author: seovalue

> 아이템 23. 태그 달린 클래스보다는 클래스 계층 구조를 활용하라.

처음엔 태그 달린 클래스라는 것이 정확히 이해가 안되었는데, 이해하기로는 현재를 나타내는 표시를 갖고 있는 클래스라고 이해했다. 그리고 그 표시는 경우에 따라서 여러 가지를 가질 수 있으며, 이는 내부적으로 if나 switch등을 통해 구현되는 경우를 말한다고 생각했다.


이와 같은 경우에는 여러 구현이 한 클래스에 혼합되어있기 때문에 가독성이 떨어진다. 이와 같은 경우에는 클래스 계층 구조를 활용해 변경하자. 계층 구조의 루트가 될 추상 클래스를 정의하고, 태그 값에 따라 달라지는 메서드를 루트 클래스의 추상 메서드로 선언한다. 그리고 이를 구현하고 재정의하여 각 클래스의 특징에 맞게 동작하도록 구현할 수 있다.

-----

Author: PK

태그 달린 클래스는 단점이 정말 많다:
* 열거 타입, 태그 필드, switch 문 등 다양한 개념이 공존한다. 그래서 코드 가독성도 나쁘다.
* 다른 의미를 위한 코드가 있어서 메모리를 많이 차지한다.
* 필드를 final 처리하려면 해당 태그 상태에서 필요없는 필드까지 다 생성자에서 초기화해야 한다.
* 새로운 상태가 추가되면 switch 또는 if 문을 뒤져가며 코드를 추가해야하고, 빼먹으면 안된다.
* 인스턴스만 보고 그것이 어떤 상태인지 알 수가 없다. 캡슐화 되어있기 때문이다.

태그 달린 클래스가 뭔가 싶다가도, 이 단점을 보면 무엇인지 알아챌 것이다.<br>
인스턴스의 상태를 결정짓는 필드를 가진 클래스를 말한다.<br>

이러한 태그 달린 클래스는 클래스 계층 구조를 활용하여 간단하고 깔끔하게 해결할 수 있다.
```java
abstract class Figure {
    
    abstract double area();
}
```
```java
class Circle extends Figure {
    
    private final double radius;
    
    Circle(double radius) {
        this.radius = radius;
    }
    
    @Override
    double area() {
        return Math.PI * (radius * radius);
    }
}
```
```java
class Rectangle extends Figure {
    
    private final double length;
    private final double height;
    
    Rectangle(double length, double height) {
        this.length = length;
        this.height = height;
    }
    
    @Override
    public double area() {
        return length * height;
    }
}
```
```java
class Square extends Rectangle {
    
    Square(double side) {
        super(side, side);
    }
}
```
