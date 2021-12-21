---
layout: post
title: "[Effective Java] Item5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라"
description: 들어가며
categories: self
---

Author: seovalue

> 아이템 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라.

하나 이상의 자원에 의존하는 클래스의 경우에는 다음과 같이 구현된 경우를 드물지 않게 볼 수 있다.

```
// 정적 유틸리티를 잘못 사용한 예
public clas SpellChecker {
    private static final Lexicon dictionary = ...;
    
    private SpellChecker() {} //객체 생성 방지
    
    public static boolean isValid(String word) { ... }
    public static List<String> suggestions(String type) { ... }
}

// 싱글턴을 잘못 사용한 예
public clas SpellChecker {
    private final Lexicon dictionary = ...;
    
    private SpellChecker(...) {}
    public static SpellChecker INSTANCE = new SpellChecker(...);
    
    public boolean isValid(String word) { ... }
    public List<String> suggestions(String typo) { ... }
}
```

위 두가지 클래스의 구현이 아주 훌륭하지는 못하다고 할 수 있는 이유는, 실전에서는 사전이 언어별로 따로 있고 특수 어휘용 사전을 별도로 두기도 하기 때문이다. 사전 하나(필드)로 이 모든 쓰임에 대응할 수 있기를 바라지 말자.

이를 개선하기 위해서 SpellChecker가 여러 사전을 사용할 수 있도록 만들어보자. final 한정자를 삭제하고 다른 사전으로 교체하는 메서드를 추가할 수 있지만 이 방법은 멀티스레드 환경에서는 쓸 수 없다.

더보기

🤔 이와 관련하여 우아한 테크코스의 첫번째 미션 중 내가 구현한 레이싱게임 클래스이 정적 유틸리티 클래스였고, 레이싱게임이 끝났는지를 판단하는 포인트가 되는 isRunning 필드가 static boolean으로 선언되어있었다. 이 부분에서 리뷰어분께 static으로 했을 때 멀티스레드 환경에서 잘 동작할 수 있을지 여부에 대해서 고민해보라고 하셨는데, 이 문제와 동일한 문제일 것이다. **사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.** _스레드1에서 해당 isRunning 필드 값을 변경할 경우, 동시에 참조하던 스레드2에서 문제가 발생할 수 있다._

따라서 이 경우에는 클래스가 여러 자원 인스턴스를 지원해야 하며, 클라이언트가 원하는 자원을 사용해야한다. 이 조건을 만족시키기 위해서는 바로 **인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식**이다. 이는 의존 객체 주입의 한 예이다.

```
public class SpellChecker {
    private static final Lexicon dictionary;
    
    public SpellChecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    }
    
    public static boolean isValid(String word) { ... }
    public static List<String> suggestions(String type) { ... }
}
```

이 **의존 객체 주입 패턴**의 쓸만한 변형으로는 생성자에 자원 팩터리를 넘겨주는 방식이 있다. 즉, 팩터리 메서드 패턴을 구현하는 것이다. 이 방식을 사용하면 클라이언트는 자신이 명시한 타입의 하위 타입이라면 무엇이든 생성할 수 있는 팩터리를 넘길 수 있다.

```
Mosaic create(Supplier<? extends Tile> tileFactory) { ... }
```

-----

Author: PK

간단하다.

객체가 사용하는 자원이 있다면, 객체가 그것을 직접 생성하게 하지 말고 외부로부터 주입 받자. 당연하다.
