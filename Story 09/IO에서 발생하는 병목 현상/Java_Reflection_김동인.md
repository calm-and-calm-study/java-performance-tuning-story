# Overview

스프링은 프레임워크를 구동하게 되면 `@Controller`, `@Service` 등의 어노테이션만 사용하면 런타임에 자동으로 컨트롤러, 서비스의 빈으로 등록이 되고 의존성 주입이 됩니다. 하지만 컴파일 시점에는 단지 어노테이션만 확인할 수 있지 어떤 동작을 하는 역할인지 알 수 없습니다. 다른 코드가 없으니까요.

스프링을 구동시키면 런타임 시점에 모든 빈을 주입하고 스프링 환경이 구성이 됩니다. 그 부팅 과정에서 어떤 기술로 이러한 어노테이션을 시스템이 인식하고 컨트롤러, 서비스 등의 역할을 부여할까요? 기본적으로 DI(Dependency Injection)의 기술을 사용해서 구현이 되며 DI는 Java의 Reflection을 통해 구현이 된 기술입니다.

자바의 리플렉션은 아래의 정의로 이해할 수 있습니다.

> Reflection is a feature in the Java programming language. It allows an executing Java program to examine or "introspect" upon itself, and manipulate internal properties of the program. For example, it's possible for a Java class to obtain the names of all its members and display them. - Oracle Technical Article

- 자바 프로르개밍 언어의 기능
- 실행중인 자바 프로그램의 내부 속성을 조작할 수 있게 한다.
- Java 클래스는 모든 구성원의 이름을 가져와 표시할 수 있다.

> In this article, we will be exploring Java reflection, which allows us to inspect or/and modify runtime attributes of classes, interfaces, fields, and methods. This particularly comes in handy when we don't know their names at compile time. Additionally, we can instantiate new objects, invoke methods, and get or set field values using reflection.- baeldung Guide

- 클래스, 인터페이스, 필드 및 메서드 속성을 런타임 때 검사하거나 수정할 수 있다.
- 컴파일 시점에 클래스, 인터페이스, 필드, 메서드의 이름을 모를 때 리플렉션이 유용하다.
- 새 객체를 인스턴스화하고 메서드를 호출하고 반사를 사용하여 필드 값을 가져오거나 설정할 수 있다.

Java의 Reflection은 런타임에 동작한다. 우선 Runtime 환경에서 JVM이 어떻게 동작하는지 알아봐야 한다.

# JVM Runtime Data Area

Java 프로그램이 실행되면 JVM은 시스템으로부터 프로그램을 수행하는 필요한 메모리를 할당받고 이 메모리를 용도에 따라 여러 영역을 나누어서 관리합니다.

- Java Compiler : Java 코드를 ByteCode로 변환한다.
- Java API, Class Files : 컴파일 된 자바 클래스 파일
- **Class Loader System** : 컴파일된 자바 클래스 파일을 메모리(Runtime Data Area)에 적재한다
- Execution Engine : ByteCode를 실행 가능 하도록 해석

![image](https://user-images.githubusercontent.com/66561524/184524056-d1778217-c0b4-45ec-b8c6-20077cc3636d.png)

JVM의 Run-Time Data Area는 크게 PC Registers, JVM Stacks, Method Area, Heap, Native Method Stacks가 있습니다. 각 영역에 대한 설명은 아래와 같습니다.

![image](https://user-images.githubusercontent.com/66561524/184524059-3f6ed12d-ad5b-4ea8-9865-2d166f4a8caf.png)

## PC(Program Counter) Registers

- Java에서 Thread는 각자의 메소드를 실행하게 된다.
- 이 때 Thread 별도로 동시에 실행하는 환경이 보장되어야 한다. JVM에서는 명령어 주소값을 저장할 공간이 필요하다.
- Thread 들은 각각 자신만의 PC Registers를 가지고 있다.
- 만약 실행했던 메소드가 네이티브 하다면 undefined가 기록된다. 네이티브하지 않다면 PC Registers는 JVM에서 사용된 명령의 주소값을 저장하게 된다.

## JVM Stacks

- 각 Thread 별로 따로 할당되는 영역이다.
- Heap 메모리 영역보다 비교적 빠르다.
- 각각의 Thread 별로 메모리를 따로 할당하기 때문에 동시성 문제에서 자유롭다.
- Thread 들은 메소드를 호출할 때마다 Frame 이라는 단위를 추가(push)하게 된다.
- 메서드가 마무리 되면 결과를 반환하면 Frame은 Stack으로부터 제거(pop)이 된다.
- Frame은 메서드에 대한 정보를 가지고 있는 Local Variable, Operand Stack 그리고 Constant Pool Reference 로 구성이 되어있다.
    - Local Variable 메서드 안의 지역 변수들을 가지고 있다.
    - Operand Stack은 메서드 내 연산을 위해서 바이트 코드 명령문이 들어있는 공간이다.
    - Constant Pool Reference는 Constant Pool 참조를 위한 공간이다.
- 이렇게 구성된 Java Stack은 메서드가 호출될 때 마다 Frame이 쌓이게 된다.
- Java Stack 영역이 가득 차게 되면 StackOverflowError가 발생된다.

```java
class Person {
    int id;
    String name;

    public Person(int id, String name) {
        this.id = id;
        this.name = name;
    }
}

public class PersonBuilder {
    private static Person buildPerson(int id, String name) {
        return new Person(id, name);
    }

    public static void main(String[] args) {
        int id = 23;
        String name = "John";
        Person person = null;
        person = buildPerson(id, name);
    }
}
```
![image](https://user-images.githubusercontent.com/66561524/184524061-74eaec5d-523b-47e5-bc47-c0c01b8b171d.png)

- 각 Frame의 연산이 끝나게 되면 결과값을 호출한 상위 Frame에 반환하게 된다.
    - 네모박스가 하나의 Frame이다.

## Method Area

- Method Area에는 인스턴스 생성을 위한 객체 구조, 생성자, 필드 등이 저장된다.
- Runtime Constant Pool과 static 변수 그리고 메소드 데이터와 같은 Class 데이터들도 이곳에서 관린된다.
- 이 영역은 JVM당 하나만 생성이 된다.
- 인스턴스 생성에 필요한 정보도 존재하기 때문에 JVM의 모든 Thread 들이 Method Area를 공유하게 된다.
- JVM의 다른 메모리 영역에서 해당 정보에 대한 요청이 오면 실제 물리 메모리 주소로 변환해서 전달해준다.
- JVM 구동시 생성이 되며 종료시까지 유지되는 공통 영역이다.

## Heap

- 코드 실행을 위한 Java로 구성된 객체 및 JRE 클래스들이 탑재된다.
- 문자열에 대한 정보를 가진 String Pool 뿐만 아니라 실제 데이터를 가진 인스턴스, 배열 등이 저장된다.
- JVM 당 역시 하나만 생성되고 해당 영역이 가진 데이터는 모두 Java Stack 영역에서 참조되어 Thread 간 공유가 된다.
- Heap 영역이 가득 차게 되면 OutOfMemoryError를 발생시킨다.
- Heap 에서는 참조되지 않은 인스턴스와 배열에 대한 정보 또한 얻을 수 있기 때문에 GC의 주 대상이 된다.
- Thread 별로 메모리를 할당받는 Java Stack 영역과 달리 조금 속도가 느리다. 모든 Thread 들이 해당 영역인 Heap을 공유해서 Java의 동시성 문제가 발생한다. Thread에 의해서 공유가 되기 때문에 Thread Safe 하지 않다. 이 때문에 해당 영역에 있는 객체나 인스턴스를 사용하게 되면 synchronized 블록을 사용하는 방법들을 비롯하여 동시성을 지켜주는 방법을 사용한다.

## Native Method Stacks

- Java로 작성된 프로그램을 실행하면서 순수 Java로 구성된 코드만 사용할 수 없는 시스템의 자원이나 API가 존재한다.
- 다른 프로그래밍 언어로 작성된 메소드들을 Native Method라고 한다. Native Method Stacks는 Java로 작성되지 않은 메서드를 다루는 영역이다.
- Java Stacks 영역과 비슷하게 Native Method가 실행될 경우 Stack에 해당 메서드가 쌓이게 된다.
- 각각의 Thread 들이 생성되면 Native Method Stacks도 동일하게 생성된다.

# Reflection이 클래스 정보를 다루는 방법

---

Reflection은 Heap 영역에 할당되는 클래스의 인스턴스를 생성하여 클래스를 다루는 것에 있습니다. 클래스 인스턴스를 생성하면 클래스 정보를 다양하게 활용할 수 있는 메서드가 있습니다.

java.lang.reflect 패키지에는 생성자, 메서드, 필드 같은 클래스들이 있다. 생성자를 통해 새로운 객체를 생성할 수 있게 됩니다. 그러면 필드의 접근제어자와 상관없이 필드의 값을 읽거나 수정할 수 있습니다. 또한 invoke라는 메서드를 통해 해당 클래스의 메서드를 호출할 수 있죠. 그리고 메서드의 parameter, return type, modifier, annotation에도 접근할 수 있습니다.

따라서 런타임시점에도 클래스의 정보를 다루게 될 수 있습니다. 리플렉션을 가장 쉽게 이용할 수 있는 방법은 Annotation 입니다. annotation을 클래스에 작성하여 어노테이션이 붙은 클래스를 대상으로 삼을 수 있죠. Spring Framework가 빈주입을 위해 사용하는 DI, Spring AOP 및 Hibernate의 기술을 구현하기 위한 Dinamic Proxy 등이 Java의 Reflection으로 구현이 되어있어 런타임시점에 주입이 진행되죠. 또한 Jackson도 Reflection을 사용해서 기술을 구현하고 있습니다.

![image](https://user-images.githubusercontent.com/66561524/184524067-912360ac-1062-457e-b516-614d877cf502.png)

가볍게 소개하면 Spring CGLIB에서 사용하는 Dynamic Proxy는 Reflection으로 어노테이션이 있는 클래스를 읽어 정보를 가져옵니다. 

# 주의점

---

Reflection 사용시 Class.newInstance()을 하게 되면 생성자의 인자값을 알 수 없습니다. 따라서 리플렉션을 사용하기 위해서는 기본생성자를 만들어줘야 합니다. 하지만 public하게 만들어줄 필요가 없습니다. 기본생성자를 호출하기만 하면 되기 때문입니다.

따라서 어떠한 리플렉션으로 기술이 구현되어있는지 확인해보고 기본 생성자가 필요한 경우에는 생성자를 추가해야 됩니다. JPA의 Entity, Spring의 CGLIB의 기술도 그렇게 구현이 되어있어서 이 점을 유의해야 합니다.

# Reference

---

[[Java 이야기] Spring DI의 동작원리(feat.Reflection)](https://better-dev.netlify.app/java/2020/08/15/thejava_7/)

[JVM의 Runtime Data Area](https://www.holaxprogramming.com/2013/07/16/java-jvm-runtime-data-area/)

[JVM에 관하여 - Part 3, Run-Time Data Area](https://tecoble.techcourse.co.kr/post/2021-08-09-jvm-memory/)

[](https://www.baeldung.com/java-stack-heap)

[Class (Java Platform SE 8 )](https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html)

[몰라도 되는 Spring - 리플렉션을 이용하는 Spring container](https://taes-k.github.io/2021/05/23/spring-di-reflection/)

[[우아한테크코스] 기본 생성자가 필요한 이유 (Why the default constructor is needed) (feat. Jackson ObjectMapper + Reflection)](https://da-nyee.github.io/posts/woowacourse-why-the-default-constructor-is-needed/)

[Java Reflection API에 대하여 (JPA에서 기본 생성자가 반드시 필요한 이유)](https://1-7171771.tistory.com/123)
