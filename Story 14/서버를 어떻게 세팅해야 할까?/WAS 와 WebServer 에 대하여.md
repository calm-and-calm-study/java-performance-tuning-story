# WAS 와 WebServer 에 대하여

---

우리는 개발을 하면서 Was 와 WebServer 라는 단어 자체는 많이 들어봤을 것입니다.
하지만 Was 와 웹서버의 차이점을 명확하게 알지는 못하는 경우가 다반수입니다. 

게다가 SpringBoot 에서는 Apache Tomcat 을 내장서버로 제공하고 있기 때문에 어플리케이션에서 Tomcat 과의 연결을 자동으로 설정해줘 실제 우리가 Tomcat 을 띄어보거나, 설정을 조작하는 일에서 많이 벗어나게 되었습니다.

그러나 Was 와 웹서버를 모르는 건 단순히 어플리케이션을 구동할 때에만 가능하지, 실제 우리의 서버가 Dev, Stage, Product 으로 나가게 될 때에는 예기치 못한 오류를 해결하지 못하게 됩니다.


## WAS 와 Web Server 의 차이

---

![1](https://user-images.githubusercontent.com/41246605/187384087-12cb34ba-e9eb-4172-b96f-0892fd8e5a1d.png)

WAS ( Web Application Server ) , Web Server 는 위의 그림과 같이, Web Server 를 WAS 가 포함하고 있는 것을 알 수 있습니다. **WAS** 의 기능은 browser, Mobile App 등 Client 로 HTTP 요청을 처리하는 것입니다.

이 HTTP Request 의 요청을 크게 2가지로 구분할 수 있는데,

- 정적인 컨텐츠 요청 ( .html, .jpeg, .css 등 )
- 동적인 컨텐츠 요청

정적인 컨텐츠는 모든 사용자에게 동일하게 제공되는 정보로, html, image, css 등을 의미하며, 동적인 컨텐츠는 Application 에 따라 로직을 수행하여 사용자에 따라 다르게 보여주는 정보를 의미합니다.

이 정적인 컨텐츠만을 제공하는 것을 Web Server 로 볼 수 있으며, WAS 는 DB connection 등 어플리케이션에서 로직을 수행하여 동적인 정보까지 제공하는 Server 로 볼 수 있는 것입니다.

***그렇다면, WebServer 와 WAS 의 기능을 파악하여 이 둘의 차이점을 명확히 알아보자.***




### WebServer 의 기능

---

- HTTP 프로토콜을 기반으로 하여 클라이언트 (웹 브라우저 또는 웹 크롤러) 의 요청을 서비스하는 기능을 담당한다.
- 요청에 따라 아래 두가지 기능을 선택하여 수행한다.
    - 기능 1
        - 정적인 컨텐츠 제공
        - WAS 를 거치지 않고 바로 자원을 제공
    - 기능 2
        - 동적인 컨텐츠 제공을 위한 요청 전달
        - 클라이언트의 요청 (Request) 을 WAS 에 보내고, WAS가 처리한 결과를 클라이언트에게 전달 (Response) 한다.
- Web Server 의 예시
    - Apache Server. Nginx 등


<br /> <br />


### WAS 의 기능

---

- WAS = Web Server + Web Container
- Web Server 기능들을 구조적으로 분리하여 처리하고자 하는 목적
    - 분산 트랜잭션, 보안, 메시징, 쓰레드 처리 등의 기능을 처리하는 분산 환경에서 사용
    - DB 서버와 같이 수행
- 주요 기능
    - 동적 컨텐츠를 제공
    - 프로그램 실행 환경과 DB 접속 기능 제공
    - 여러 개의 트랜잭션 (논리적인 작업 단위) 관리 기능
    - 업무를 처리하는 비즈니스 로직 수행
    - 여러 대의 WAS를 연결 가능
        - Load Balancing 을 위해서 Web Server 를 사용
        - fail over (장애 극복), fail back 처리에 유리
        - 대용량 웹 어플리케이션의 경우 ( 여러 개의 서버 사용 ) Web Server 와 WAS를 분리하여 무중단 운영을 위한 장애 극복에 쉽게 대응 가능
        - 앞 단의 Web Server 에서 오류가 발생한 WAS 를 이용하지 못하도록 하고, WAS 를 재시작함으로써 사용자는 오류를 느끼지 못함
- WAS 의 예시
    - Tomcat, Jeus, Web Sphere 등

<br />

![2](https://user-images.githubusercontent.com/41246605/187385500-3214dcc7-9dec-48e8-a05a-f673fe6196ee.png)


- Apache 라는 웹서버와 3개의 WAS 로 구성된 아키텍처


# WebServer 와 WAS 의 분리 이유

---

WebServer 와 WAS 가 하나로 통합되어서 사용될 수는 있다. 그러나 WAS 는 동적인 컨텐츠를 제공하기 위해 DBMS 나 여러 MiddleWare 와 통신을 하기 때문에 Request 에 대한 부하를 최대한 많이 줄여주는 것이 좋다.

이에 Ngnix 와 같은 Web Server 를 WAS 앞 단에 두면 정적인 컨텐츠에 대한 요청은 WAS 에 전달하지 않고, WebServer 에서 자원을 제공하는 것이 가능하기 때문에 WAS 의 요청에 대한 부담을 확실히 덜어줄 수 있다.




### WebServer 가 WAS 앞 단에 있을 때의 요청 처리 과정

---

![3](https://user-images.githubusercontent.com/41246605/187385936-66153c08-6be2-4f20-95ed-bf578b754de2.png)


1. Web Server 는 웹 브라우저 클라이언트로부터 HTTP 요청을 받는다.
2. Web Server 는 클라이언트의 데이터 처리 요청(Request) 을 WAS 에 보낸다.
3. WAS 는 해당 요청과 관련된 Thread 를 생성하여 로직을 수행한다.
4. 이때 DB Server 와 통신하여 데이터 요청를 처리한다.
5. WAS 는 Thread 로 생성된 Response 객체를 HttpResponse 형태로 바꾸어 Web Server 에 전달한다.
6. 생성된 Thread를 종료하고, HttpServletRequest 와 HttpServletResponse 객체를 제거한다.


