---
layout: post
title: "[Effective Java] Item10. equals는 일반 규약을 지켜 재정의하라"
description: 잘 만든 equals 여러 테스트 코드 부럽지 않다
categories: self
---

Author: seovalue

> 아이템 9. equals는 일반 규약을 지켜 재정의하라.

### equals 재정의 일반 규약
1. 반사성: null이 아닌 모든 참조 값에 대해 `x.equals(x)`는 true.
2. 대칭성: null이 아닌 모든 참조값 x,y에 대해 `x.equals(y)`가 true이면 `y.equals(x)`도 true.
3. 추이성: null이 아닌 모든 참조 값 x,y,z에 대해 `x.equals(y)`가 true이고, `y.equals(z)`가 true이면 `x.equals(z)`도 true이다.
4. 일관성: null이 아닌 모든 참조 값 x,y에 대해 `x.equals(y)`를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다.
5. null-아님: null이 아닌 모든 참조값 x에 대해 `x.equals(null)`은 항상 false이다.


### 올바른 equals 구현 방법
1. == 연산자를 사용해 입력이 자기 자신의 참조인지 확인한다.
2. instanceof 연산자로 입력이 올바른 타입인지 확인한다. 
3. 입력을 올바른 타입으로 형변환한다. 이는 2과정을 전제한다.
4. 입력 객체와 자기 자신의 대응되는 핵심 필드들이 모두 일치하는 지 하나씩 검사한다. 

여기서 float과 double을 제외한 기본 타입 필드는 ==으로, float, double은 compare메서드를 활용해 비교하자. 이는 Float.NaN, -0.0f 등 특수한 부동소수 값을 다뤄야하기 때문이다.

-----

Author: PK

동일성이 아닌 동등성을 유지하고 싶을 때 equals 메서드를 재정의해야한다. (내가 직접 씀)

참고로 Object 명세에 있는 equals 메서드가 따라야 하는 규약들은 다음과 같다:

- 반사성 : x.equals(x) == true, null 제외
- 대칭성: x.equals(y) == y.equals(x), null 제외
- 추이성: x.equals(y) == y.equals(z) == z.equals(x), null 제외
- 일관성: 항상 그 결과가 같음
- null-아님: x.equals(null) == false, null 제외

살짝쿵 너무 당연한 내용이다. 그러니 만약 equals 메서드를 재정의하게 된다면 떠올리자.

요즘엔 웬만하면 IDE에서 자동으로 생성해준다. lombok을 사용해서 만들 수도 있다.

값을 비교하는 상황에서는 값 싼 필드를 먼저 비교하도록 하자.
