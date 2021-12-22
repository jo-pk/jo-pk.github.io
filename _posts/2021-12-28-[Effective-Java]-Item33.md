---
layout: post
title: "[Effective Java] Item33. 타입 안전 이종 컨테이너를 고려하라"
description: 다양한 타입을 골라 담을 수 있다
categories: self
---

Author: seovalue

> 아이템 33. 타입 안전 이종 컨테이너를 고려하라

-----

Author: PK

`type safe heterogeneous container pattern (타입 안전 이종 컨테이너 패턴`이란:<br>
컨테이너 대신 키를 매개변수화한 다음, 컨테이너에 값을 넣거나 뺄 때 매개변수화한 키를 함께 제공하는 것이다.<br>
무슨 말인지 잘 모르겠으니 예시를 보자:
```java
public class Favorites {
    
    private Map<Class<?>, Object> favorites = new HashMap<>();

    public <T> void putFavorite(Class<T> type, T instance) {
        favorites.put(Objects.requireNonNull(type), instance);
    }
    public <T> T getFavorite(Class<T> type) {
        return type.cast(favorites.get(type));
    }
    
    public static void main(String[] args) {
        
        Favorites f = new Favorites();
        
        f.putFavorite(String.class, "Java");
        f.putFavorite(Integer.class, 0xcafebabe);
        f.putFavorite(Class.class, Favorites.class);
        
        String favoriteString = f.getFavorite(String.class);
        int favoriteInteger = f.getFavorite(Integer.class);
        Class<?> favoriteClass = f.getFavorite(Class.class);

        // Java cafebabe Favorites
        System.out.printf("%s %x %s%n", favoriteString, favoriteInteger, favoriteClass);
    }
}
```
위의 코드에서 cast 메서드는 인스턴스가 원하는 타입을 가지고 있는지 확인해서 문제가 없으면 반환, 그렇지 않으면 ClassCastException 을 반환한다.
이 메서드가 Favorites 를 타입 안전하게 만드는 비결이다.

### super type token (슈퍼 타입 토큰)
```java
Favorites f = new Favorites();

List<String> pets = Arrays.asList("dog", "cat", "bird");

f.putFavorite(new TypeRef<List<String>>(){}, pets);
List<String> listOfStrings = f.getFavorite(new TypeRef<List<String>>(){});
```
이런 식으로 적용하면 제네릭 타입도 저장 가능하다.<br>
스프링 프레임워크에서는 `ParameterizedTypeReference`라는 클래스로 미리 구현해놓았다.<br>
주말에 아래의 블로그 포스트를 읽고 한 번 보충해보자.

[닐 개프터의 블로그 포스트](http://gafter.blogspot.com/2006/12/super-type-tokens.html)
[닐 개프터가 말하는 슈퍼 타입 토큰의 한계](http://gafter.blogspot.com/2007/05/limitation-of-super-type-tokens.html)
