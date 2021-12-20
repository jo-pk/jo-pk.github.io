---
layout: post
title: "[Effective Java] Item9. try-finally보다는 try-with-resources를 사용하라"
description: 들어가며
categories: self
---

Author: seovalue

> 아이템 9. try-finally보다는 try-with-resources를 사용하라.

자바 라이브러리에는 close 메서드를 호출해 직접 닫아줘야하는 자원이 많다. 예를 들자면, InputStream, OutputStream, Connection 등이 있다. 이러한 "자원 닫아주기" 행위는 놓치기 쉽기 때문에 예측할 수 없는 성능 문제로 이어지기도 한다. OutOfMemory와 같은..

전통적으로는 자원 닫힘을 보장하는 수단으로서 try-finally 구문이 사용되었다.

```
BufferedReader br = new BufferedReader(new FileReader(path));
try {
    return br.readLine();
} finally {
    br.close();
}
```

하지만 자원을 여러개 사용하게 된다면 불필요한 try-catch-finally 문이 **중첩**될 수 있다.

또한 위와 같은 경우 br.readLine에서도 예외가 발생하고, br.close에서도 예외가 발생한다면 스택 추적 내역에는 두 예외 상황이 모두 리포트되는 것이 아니라 특정 한가지의 예외 정보는 남지 않게 되어 디버깅을 매우 어렵게 한다.

```
try (InputStream in = new FileInputStream(src);
     OutputStream out = new FileOutputStream(dst)) {
     byte[] buf = new byte[BUFFER_SIZE];
     int n;
     while ((n = in.read(buf)) >= 0) 
         out.write(buf, 0, n);
 }
```

try-with-resources를 사용하면 위와 같이 코드를 변경할 수 있다.

먼저, 복수의 자원에 대해서 try 구문 안에 작성함으로써 짧게 코드를 개선할 수 있다. 또한 문제 상황이 발생했을 때에도 그를 진단하기가 훨씬 좋다.  만약, 위에서 readLine과 close가 함께 예외가 발생하면 둘 중 하나의 예외는 리포트되지 않는다고 했는데, try-with-resources를 사용하게 되면 스택 추적 내역에 숨겨진 예외도 출력되므로 프로그램 코드에서 가져와 볼 수도 있다.

또한 try-with-resources 절은 catch 구문과도 함께 사용할 수 있고, 불필요한 중첩 없이 다수의 예외를 처리할 수 있다.
