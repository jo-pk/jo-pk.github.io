---
layout: post
title: "[Effective Java] Item15. 클래스와 멤버의 접근 권한을 최소화하라"
description: 들어가며
categories: self
---

Author: seovalue

> 아이템 15. 클래스와 멤버의 접근 권한을 최소화하라.

모든 클래스와 멤버의 접근성을 가능한 한 좁히자. 패키지를 외부에서 쓸 일이 없다면 package-private 접근 제어자를 사용하자. 멤버 변수는 최대한 private으로 사용하자. public 클래스의 인스턴스 필드는 되도록 public이 아니어야하며, public 가변 필드를 갖는 public 클래스는 스레드 안전하지 않다.

------

Author: PK

캡슐화는 객체지향적 언어의 특징 중 하나다.<br>
클래스 안의 필드나 메서드와 같은 컴포넌트에 사용자가 직접 접근하지 못하도록 하는 것이 바로 캡슐화다.<br>
잘 설계된 컴포넌트는 모든 내부 구현을 완벽하게 숨겨서 API 와 구현을 깔끔하게 분리한다.<br>

캡슐화를 올바르게 적용했을 때의 장점은 다음과 같다:
* 개발 속도 상승 - 여러 컴포넌트를 병렬적으로 개발 가능
* 시스템 확장 용이 및 관리 비용 절감 - 개별 컴포넌트 동작 검증 및 디버깅이 쉬움
* 성능 최적화 용이 - 사이드 이펙트 없이 특정 컴포넌트 최적화 가능
* API 재사용성 증가 - 다른 컴포넌트에 의존하고 있지 않으므로 다른 환경에서 사용 가능

즉, 필요한 의존성만 형성하고 구체적인 내용은 최대한 숨겨서 변경이 자유롭도록 하는 것이다.<br>
그렇기 때문에 **모든 클래스와 멤버의 접근성을 가능한 한 좁혀야 한다.**<br>

```java
class HiddenClass {
    
    private int num;
    
    public HiddenClass(int num) {
        this.num = num;
    }
}
```
클래스는 패키지 외부에서 사용할 것이 아닌 이상 package-private 으로 둔다. 간단하다.<br>
만약 한 클래스 안에서만 사용하는 클래스 또는 인터페이스가 있다면 아예 사용하는 해당 클래스 안에
private static 으로 중첩시켜도 좋다:
```java
class OuterClass {

    private InnerClass innerClass = new InnerClass(1);

    public OuterClass() {
    }

    public int calculate() {
        return innerClass.numPlusOne();
    }

    private static class InnerClass {

        private int num;

        public InnerClass(int num) {
            this.num = num;
        }

        public int numPlusOne() {
            return num + 1;
        }
    }
}
```
이렇게 할 경우 OuterClass 에서 InnerClass 의 필드에 직접 접근할 수 있다는 것을 유의하자.<br>

멤버(필드, 메서드, 중첩 클래스, 중첩 인터페이스) 역시 예외 없이 최대한 private 접근자로 해결하자.
private, package-private, protected, 그리고 public 이렇게 네 가지 접근자가 올 수 있는데, protected 접근자를 채택하는 순간 해당 멤버에 접근할 수 있는 대상이 굉장히 많아진다.
**protected 접근자는 package-private의 접근 범위를 포함한다.**<br>

멤버 접근성을 좁히지 못하는 경우가 있다. 리스코프 치환원칙에 의해 상위 클래스의 메서드를 재정의 할 때,
그 접근 수준을 상위 클래스에서보다 좁게 설정할 수 없는 경우다.
인터페이스의 구현체를 만드는 것이 이 상황의 예시 중 하나다.
이땐 인터페이스에 명시된 메서드를 API 로 하고, 그 구현을 모두 private 으로 두면 된다.<br>

**테스트를 위해 접근성을 넓히는 행동은 절대로 하지 말자** 저자는 package-private 으로 두면 테스트 할 수 있다고 하는데, 웬만하면 private 으로 두자.<br>

인스턴스 필드는 사실상 거의 매번 private 으로 두자. **그리고 `public static final Integer[]`과 같은 배열 필드나 해당 필드를 반환하는 메서드는 절대 만들지 말자!** 길이가 0이 아닌 배열은 모두 변경 가능하다.
```java
class CorrectWay1 {
    
    private static final Integer[] PRIVATE_VALUES = { 1, 2, 3};
    public static final List<Integer> VALUES =
        Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
}
```
```java
class CorrectWay2 {

    private static final Integer[] PRIVATE_VALUES = { 1, 2, 3};
    public static final Integer[] values() {
        return PRIVATE_VALUES.clone();
    }
}
```
위의 두 가지 방법 중 하나를 선택해서 사용하자.

### Java 9 & 모듈 시스템
패키지는 클래스의 묶음이다. 그리고 모듈은 패키지의 묶음이다.<br>
*모듈은 자신에 속하는 패키지 중 공개(export)할 것들을 (관례상 module-info.java)에 선헌한다.*<br>
그리고 그렇게 공개된 클래스는 모듈 밖에서도 사용할 수 있다.<br>
하지만 모듈의 JAR 파일을 애플리케이션의 클래스패스(classpath)에 두면 그 모듈 안의 모든 패키지는 모듈이 없는 것처럼 외부에서 접근할 수 있게 된다.
(물론 public & protected 컴포넌트만 해당한다.)
*JDK 가 이 모듈 기능을 활용한 대표적인 예시인데, 자바 라이브러리에서 공개하지 않은 패키지들은 해당 모듈 밖에서는 접근할 수 없다.*<br>

이런 모듈 기능의 장점을 누리기 위해선 할 일이 많다 :
* 패키지를 모듈 단위로 묶는다.
* 모듈 선언에 패키지들의 모든 의존성을 명시한다.
* 소스 트리를 재배치하고 모듈 안으로부터 (모듈 시스템을 적용하지 않는) 일반 패키지로의 모든 접근에 특별한 조치를 취한다.
그래서 저자는 모듈 개념은 JDK 외에도 널리 받아들여지기 전엔 사용하지 않는 것을 추천한다. <br>

모듈 관련해서는 다음에 또 알아봐야겠다.
