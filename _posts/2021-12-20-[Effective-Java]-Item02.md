---
layout: post
title: "[Effective Java] Item02. 생성자에 매개변수가 많다면 빌더를 고려하라"
description: 들어가며
categories: self
---

Author: seovalue

> 아이템 2. 생성자에 매개변수가 많다면 빌더를 고려하라.

생성자에 매개변수가 많을 때, 사용하는 **점층적 생성자 패턴**의 객체가 완전히 생성되기 전까지는 일관성이 무너진 상태에 놓이게 되는 단점과,  **자바 빈즈 패턴**의 클래스를 불변으로 만들수 없거나 하는 단점을 완화하고자 **Builder**를 사용한다.

Builder는 점층적 생성자 패턴의 안전성와 자바빈즈의 가독성을 겸비하였다.

Builder를 사용한다면, Client는 필수 매개변수 "만"으로 생성자를 호출해 빌더 객체를 얻고, 빌더가 제공하는 세터를 이용하여 원하는 선택 매개변수들을 설정할 수 있다.

```
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;
    
    public static class Builder {
        // 필수 매개변수
        private final int servingSize;
        private final int servings;
        
        // 선택 매개변수
        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;
        
        public Builder(int servingSize, int servings) { 
            this.servingSize = servingSize;
            this.servings = servings;
        }
        
        public Builder calories(int val) {
            calories = val;
            return this;
        }
        
        ....
        
        
  }
```

위와 같은 클래스가 주어졌을 때, 우리는 수많은 매개변수에 따른 생성자를 만들거나 자바빈즈를 활용하기 마련이다. 하지만 builder를 이용하여 필수 매개변수를 포함한 생성자를 통해 builder 객체를 생성하고, builder에서 선택 매개변수들을 위해 제공하는 setter를 사용하여 다음과 같이 가독성 높은 코드를 짤 수 있다.

```
NutritionFacts nutritionFacts = new NutritionFacts.Builder(240, 8).calories(100).~~;
```

이러한 빌더는 상당히 유연하며, 빌더 하나로 여러 객체를 순회하면서 만들 수 있고, 빌더에 넘기는 매개변수에 따라 다른 객체를 만들 수 있는 장점을 가진다. 하지만 생성자의 매개변수가 4개 이상이 되지 않는 경우에는 성능상의 이점이 생기지 않을 수도 있고, (생성자를 만드는 것에 비해) 또한 객체를 만들기 위해서 빌더부터 생성해야하는 단점이 존재할 수 있다.

-----

Author: PK

빌더 패턴은 (파이썬과 스칼라에 있는) 명명된 선택적 매개변수(named optional parameters)를 흉내낸 것.

- 빌더 하나로 여러 객체를 순회하면서 원하는 것을 만들 수 있다
- 매개변수에 따라 다른 객체를 만들 수 있다
- 부여되는 일련번호와 같은 특정 필드는 빌더가 알아서 채우도록 할 수 있다

즉, 유연하다.

하지만 빌더를 먼저 만들어야 해서 성능에 매우 민감한 상황에서는 사용하지 못 한다. 점층적 생성자 패턴보다는 구현이 복잡해서 필드가 많지 않으면 그 값어치를 하지 못 한다. 그리고 필드의 타입이 얼마나 겹치는지도 볼 필요가 있다.
