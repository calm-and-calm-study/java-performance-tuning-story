### Logging Wrapper

---

로깅 프레임워크는 다음과 같습니다.

1. Log4j
2. Java logging API
3. tinylog
4. Logback
5. JCL
6. Slf4j

여기서 1번 ~ 4번은 로깅 프레임워크 구현체이고, 5번 ~ 6번은 Logging Wrapper로 위의 4개 프레임워크와는 다르게 구분합니다. 

Logging Wrapper란 무엇일까요?

- Logging Wrapper는 로깅 프레임워크에 대한 추상화(인터페이스) 역할을 하는 추상 로깅 프레임워크입니다. 그래서 Logging Wrapper는 단독으로 사용할 수 없고 구현체로 다른 로깅 프레임워크가 필요합니다. 
- 예를 들어, slf4j를 사용하면 코드에서는 slf4j 인터페이스 코드를 사용하고, 실제 로깅하는 구현체는 추가로 참조한 라이브러리에서 구현됩니다.
- 즉, 사용자가 배포시 원하는 로깅 프레임워크를 결정하고 사용해도 인터페이스화 되어있기 때문에 Logging Wrapper를 의존하는 클라이언트 코드에서는 실제 구현을 몰라도 됩니다.

### SLF4J

---

- ****Simple Logging Facade For Java****
    - slf4j는 이름에서 알 수 있듯 여러 로깅 구현체를 추상화한 인터페이스입니다. slf4j 자체로만 사용하지 않고, 다른 로깅 프레임워크 구현체 API를 함께 사용합니다.
- Slf4j는 컴파일 타임에 구현체를 결정합니다.
    - cf) JCL은 런타임에 구현체를 결정

### SLF4J의 동작과정

---

- 개발할 때는 slf4j API를 사용해서 로깅 코드를 작성하고, 배포시에는 바인딩된 구현체가 실제 로깅 코드를 수행합니다.

![image](https://user-images.githubusercontent.com/64389364/185777083-8673276c-eacb-4a71-965e-d93580f1b063.png)

- slf4j는 아래 3개 모듈을 가집니다.
    - **Briding Modules**
        - 다른 로깅 API logger 호출을 slf4j 인터페이스와 연결하여 slf4j가 바인딩하는 로깅 라이브러리를 사용할 수 있도록 하는 역할을 합니다.
        - binding과 bridge가 같은 종류의 logger일 경우에는 무한 루프가 발생할 수 있습니다.
            
            ![image](https://user-images.githubusercontent.com/64389364/185777089-7626ecf5-30d6-4126-8dcb-813fd80fbd3e.png)
            
    - **Slf4j API**
        - 로깅 프레임워크의 인터페이스로 추상 메서드를 제공합니다.
        - 반드시 하나의 API는 하나의 Binding을 가져야합니다.
    - **Binding**
        - slf4j 인터페이스와 로깅 프레임워크 구현체를 바인딩하는 어댑터 역할을 합니다.
        - 사용하려는 로깅 프레임워크에 대한 slf4j 바인딩을 추가해야합니다.
- 스프링 부트에서는 기본적으로SLF4J, Logback을 채택하고 있습니다.

reference
- https://livenow14.tistory.com/63
- https://gmlwjd9405.github.io/2019/01/04/logging-with-slf4j.html
