---
title: "JUnit @Before & @After"
date: 2021-07-18T20:29:56+09:00
categories:
    - Java
tags:
    - Java
    - Junit
    - Annotation
    - Before
    - After
url: /2021/07/18/java-junit-before
featuredImage: /images/annotation-before.png
imamges:
    - /images/annotation-before.png
published: true
---
@Before 애노테이션을 지금까지 테스트 실행 전 실행이 된다는 것 정도만 알고 있었는데, 박재성 님의 책을 통해 좋은 내용을 알게 되었다.

JUnit을 사용할 때 인스턴스 생성을 위해 @Before 애노테이션을 활용한다.

<br>
@Before 애노테이션을 사용하지 않고 필드(field)로 구현하는 방법도 단위 테스트는 성공한다.
각 단위 테스트를 실행할 때마다 인스턴스를 생성하는 것은 똑같기 때문이다.

```java
import static org.junit.Assert.assertEquals;

import org.junit.Test;

public class CalculatorTest {
    private Calculator cal = new Calculator;

    @Test
    public void add() {
        assertEquals(6, cal.add(3, 3));
    }

    @Test
    public void sub() {
        assertEquals(0, cal.sub(3, 3));
    }

    @Test
    public void mul() {
        asssertEquals(9, cal.mul(3, 3));
    }

    @Test
    public void div() {
        assertEquals(2, cal.div(6, 3));
    }
}
```

위와 같이 작성해도 테스트는 성공한다.  
하지만, JUnit은 초기화 작업을 위와 같이 구현하는 것을 추천하지 않는다.

<br>
인스턴스를 매 테스트마다 생성하는 이유는 테스트 메서드를 실행할 때 인스턴스의 상태 값이 변경되어 다음 테스트 메서드를 실행할 때 영향을 미칠 수 있기 때문이다. 이와 같이 테스트 실행 순서나 인스턴스 상태 값에 따라 테스트가 성공하거나 실패할 수 있다.

## @Before
그러면 왜 @Before 애노테이션을 활용하도록 추천할까?

JUnit에는 `@RunWith`, `@Rule` 같은 애노테이션을 사용해 기능을 확장할 수 있는데, @Before 안이어 야만 @RunWith, @Rule에서 초기화된 객체에 접근할 수 있다는 제약사항이 있기 때문이다.
```java
import static org.junit.Assert.assertEquals;

import org.junit.Test;

public class CalculatorTest {
    private Calculator cal;

    @Before
    public void setup() {
        cal = new Calculator();
        System.out.println("before");
    }

    @After
    public void cleanup() {
        System.out.println("cleanup");
    }

    @Test
    public void add() {
        assertEquals(6, cal.add(3, 3));
        System.out.println("add");
    }

    @Test
    public void sub() {
        assertEquals(0, cal.sub(3, 3));
        System.out.println("sub");
    }

    @Test
    public void mul() {
        asssertEquals(9, cal.mul(3, 3));
        System.out.println("mul");
    }

    @Test
    public void div() {
        assertEquals(2, cal.div(6, 3));
        System.out.println("div");
    }
}
```
이전 코드에서 @Before, @After 애노테이션을 사용했다.  
<br>

테스트를 실행해보면 실행 순서는 다음과 같이 나온다.  
<img src = "/images/calculatortest.png" alt="junit-test" width="50%" height="50%">

* @Before -> method -> @After 순서로 나오는 것을 확인할 수 있다.

<br>
매번 초기화, 후처리 작업을 통해 각 테스트 간에 영향을 미치지 않으면서 독립적인 실행이 가능하다.