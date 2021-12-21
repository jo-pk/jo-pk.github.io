---
layout: post
title: "[Effective Java] Item06. 불필요한 객체 생성을 피하라"
description: 들어가며
categories: self
---

Author: seovalue

> 아이템 6. 불필요한 객체 생성을 막아라.

똑같은 기능의 객체를 매번 생성하기보다는 객체 하나를 재사용하는 편이 나을 때가 많다. 특히 불변 객체는 언제든 재사용할 수 있다.

실행될 때 마다 인스턴스를 새로 만드는 다음과 같은 예제는 절대 추천하지 않는다. 우리는 첫번째 코드 대신, 개선된 두번째 코드를 통해 생성되는 인스턴스의 수를 줄일수도 있을 뿐만 아니라 모든 코드가 같은 객체를 사용함을 보장할 수 있다.

```
String s = new String("joanne"); // NOPE !

String s = "joanne"; // BETTER
```

우리가 아무렇지 않게 사용하는 객체 중에는 생성 비용이 아주 비싼 객체도 더러 있다. 그것은 바로바로! Pattern 객체이다.

Pattern 인스턴스는 입력받은 정규 표현식에 해당하는 유한 상태 머신을 만들기 때문에 인스턴스 생성 비용이 매우 높다. 따라서 성능을 개선하기 위해서는 필요한 정규표현식을 표현하는 불변 Pattern 인스턴스를 정적 초기화 과정에서 직접 생성해 캐싱해두고, 나중에 해당 메서드가 호출될 때마다 이 인스턴스를 **재사용**하도록 하는 방법이 가장 적합하다.

아래는 내가 우테코 코드리뷰를 진행하며 리뷰어분께 받은 피드백의 일부이다.

코드 내부에서 Pattern을 작성해두었기 때문에, 해당 메서드가 실행될 때 마다 패턴이 새롭게 생성된다.

[##_Image|kage@BlWvl/btqXKwgvfQ1/VVhE29tyHQdRAEUhDN6e2K/img.png|CDM|1.3|{"originWidth":1644,"originHeight":752,"style":"alignCenter","mobileStyle":"widthContent","filename":"스크린샷 2021-02-17 오후 10.49.16.png"}_##]

하지만 나는 첫번째 피드백을 이해하지 못하고.. 매직넘버를 상수화하듯이 패턴도 static final로 필드에 추가하여 다시 재 리뷰를 요청했다.

```
private static final String PATTERN = "//(.)\n(.*)";
```

하지만 패턴 자체는 String이 아닌 Pattern 객체로 미리 생성해둘 수 있다.

패턴과 더불어, **박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의한다면** 더 나은 코드를 작성할 수 있다. 책에서 발췌한 다음 코드 예제를 보자. 이 코드는 sum 변수를 long이 아닌 Long으로 선언함으로서 불필요한 Long 인스턴스가 약 2^31개나 만들어진다. (long 타입인 i가 sum에 더해지는 모든 순간마다 생성된다 😞)

```
private static long sum() {
    Long sum = 0L;
    for (long i = 0; i <= Integer.MAX_VALUE; i++){
        sum += i;
    }
    return sum;
}
```

-----

Author: PK

불변 객체는 언제든지 재사용할 수 있다.

```java
String never = new String("don't do this"); // do not follow this code

String good = "this is better";
```

String.matches 메서드는 정규표현식으로 문자열 형태를 확인하는 가장 쉬운 방법이지만, 성능이 중요한 상황에서 반복해 사용하기엔 적합하지 않다. 이 메서드가 사용하는 Pattern 인스턴스는 한 번 쓰고 나면 가비지 컬렉션 대상이 되는데, 이 객체는 입력받은 정규표현식에 해당하는 유한 상태 머신(finite state machine)을 만들기 때문에 인스턴스 생성 비용이 높다. 그러니 스태틱 필드로 캐싱해두고 재활용하는 것이 좋다.

```java
private static final Pattern ROMAN = Pattern.compile(/* pattern */);

static boolean isRomanNumeral(String target) {
	 return ROMAN.matcher(target).matches();
}
```

만약 이것을 보고 많이 사용하지 않을 것을 고려해서 지연초기화를 생각했다면, 저자는 비추한다. 지연초기화 구현의 복잡함에 비해 성능적 이점이 그리 크지 않아서다.

박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의하자.

다음 코드는 불변인 Long 타입 객체를 사용하는 바람에 시간이 엄청나게 느려진다:

```java
private static long sum() {
  Long sum = 0L;
  for (long i = 0; i <= Integer.MAX_VALUE; i++) {
    sum += i;
  }
  return sum;
}
```

똑똑한 척 하려고 객체 생성에 소비되는 시간을 줄이기 위해 pool을 만들겠다는 생각은 되도록이면 하지 말자. 데이터베이스 연결과 같은 경우는 워낙 생성 비용이 비싸서 pool을 만들고 관리하는 것이 좋지만, 요즘 JVM의 가비지 컬렉터는 웬만한 경우에 직접 만든 pool보다 훨씬 빠르게 작동한다.
