---
layout: post
title: "[Effective Java] Item25. 톱레벨 클래스는 한 파일에 하나만 담으라"
description: 안 그러면 소스 코드 컴파일 순서 때문에 결과가 바뀐다
categories: self
---

Author: seovalue

> 아이템 25. 톱레벨 클래스는 한 파일에 하나만 담으라

두 클래스가 한 파일에 정의되어있는 경우, 외부에서 복합적으로 사용할 때 중복 정의로 인한 문제가 발생할 수 있다.

-----

Author: PK

소스파일 하나당 톱레벨 클래스는 하나만 담도록 한다.<br>
이를 통해 컴파일러가 한 클래스에 대한 정의를 여러 개 만드는 일이 없도록 한다.
**그런데 톱레벨 클래스가 여러 개라도 자바 컴파일에서 에러를 내뱉진 않는다. 경고도 하지 않는다!**<br>

그렇기 때문에 더더욱 조심해야 한다. 앞서 말했듯, 톱레벨 클래스가 여러 개라면 한 클래스에 대한 정의가 여러 개 생길 수 있고, 이는 어느 소스 파일이 먼저 컴파일 되는가에 따라 문제가 생길 수 있다.

```java
// Main.java
public class Main {

    public static void main(String[] args) {
        System.out.println(Pk.NAME + " " + Joanne.NAME);
    }
}
```
```java
// Pk.java - 두 class 가 하나의 소스 파일에 정의된 상태
class Pk {

    static final String NAME = "pkeugine";
}

class Joanne {

    static final String NAME = "seovalue";
}
```
```java
// Joanne.java - 두 class 가 하나의 소스 파일에 정의된 상태
class Pk {

    static final String NAME = "Eugine Park";
}

class Joanne {

    static final String NAME = "MinJung Seo";
}
```
위의 예시를 활용하여 프로그램을 실행해보자.
#### javac Main.java Joanne.java
컴파일 오류가 나고 클래스가 중복 정의되었다고 뜬다.
Main.java 안에서 Pk 에 대한 참조가 먼저 오기 때문에 Pk.java 를 살핀다음,
명령에 포함된 Joanne.java 를 살피기 때문이다.

#### javac Main.java
"pkeugine seovalue"을 출력한다.

#### javac Main.java Pk.java
역시 "pkeugine seovalue"을 출력한다.

#### javac Joanne.java Main.java
"seovalue pkeugine"를 출력한다. 즉, 어떤 소스 파일을 먼저 컴파일 하는가에 따라 결과가 달라진다.

```java
// Main.java
public class Main {

    public static void main(String[] args) {
        System.out.println(Pk.NAME + " " + Joanne.NAME);
    }

    private static class Pk {

        static final String NAME = "Eugine Park";
    }

    private static class Joanne {

        static final String NAME = "MinJung Seo";
    }
}
```
한 가지 해결책으로 위와 같이 정적 멤버 클래스로 두 클래스를 바꿔줄 수 있다.
