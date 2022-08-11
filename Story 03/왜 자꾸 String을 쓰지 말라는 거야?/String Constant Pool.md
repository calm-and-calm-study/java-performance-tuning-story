# Overview

---

Chapter03에서 우리는 String이 성능에 매우 밀접하다는 사실을 알게 되었다. 공부를 하면서 가장 기본적인 String 객체의 메모리 사용량을 해결하기 위한 다른 노력들을 찾아보았다. 

우리는 String을 사용할 때 2가지 방법을 사용할 수 있다.

- String literal
- new String()

첫 번째 방법은 “”를 사용해 String을 사용하는 방법이고 두 번째 방법은 String 객체를 생성하는 방법이다.

```java
@Test
void stringLiteral() {
    String str1 = "asd";
    String str2 = "asd";
    assertThat(str1 == str2).isTrue();
}

@Test
void stringObject() {
    String str1 = new String("asd");
    String str2 = new String("asd");
    assertThat(str1 == str2).isTrue();
}
```

![image](https://user-images.githubusercontent.com/66561524/182009590-332ed8d7-8f78-4a9d-b35b-c78c2dc01426.png)

둘의 결과는 다르게 나온다. String literal 객체는 같은 메모리 주소를 가르키고 있고 new 연산자로 생성한 String 객체는 내용이 같더라도 개별적인 객체이다.

# String Constant Pool

---

String literal로 문자열을 생성하면 해당 String 값은 Heap 영역 내 ‘String Constant Pool’에 저장되어 재사용된다. 하지만 new 연산자를 사용하면 같은 내용이라도 여러 개의 객체가 각각 Heap 영역을 차지하게 된다.

![image](https://user-images.githubusercontent.com/66561524/182009595-98cf1045-e8fa-4922-9a80-433a9492ef0b.png)

- 생성된 String 객체는 Stack영역에 만들어져 GC가 제거해야할 대상의 객체가 된다. 그래서 성능적인 비효율이 발생한다.

# Reference

---

[](https://www.baeldung.com/java-string-pool)
