---
layout: post
title: "[Effective Java] Item27. 비검사 경고를 제거하라"
description: 직접 머리 굴려서 해결하기 vs 애너테이션으로 무시하기
categories: self
---

Author: seovalue

> 아이템 27. 비검사 경고를 제거하라

-----

Author: PK

javac 커맨드에 `-Xlint:uncheck` 옵션을 추가하면 unchecked casting 관련 경고를 컴파일러가 보여준다.<br>
해당 옵션을 활용해서 다음과 같은 방식으로 코드를 수정할 수 있다:

```java
//before
Set<Something> somethings=new HashSet();

//after
Set<Something> somethins=new HashSet<>();
```

위의 경우엔 다이아몬드 연산자(`<>`)를 사용해서 문제를 해결했다.<br>
다이아몬드 연산자는 자바 7부터 지원한다.<br>
물론 타입 안전하지 않다는 경고가 보이는데도 내가 안전하다고 확신이 생긴다면
`@SuppressWarnings("unchecked")` 애너테이션을 달아서 경고를 제거할 수 있다. 웬만해서는 작음 범위에서 적용하도록 하자. (ex: 변수 선언, 메서드, 생성자)
만약 한 줄이 넘는 코드에 애너테이션이 달려있다면 최대한 지역변수 선언 쪽으로 옮기자. 이를 위해 지역변수를 생성하는 것도 좋다. (애너테이션은 선언에만 달 수 있다.)<br>

**그래도 웬만해서는 경고를 애너테이션으로 무시하지 말고 해결하도록 하자.**
