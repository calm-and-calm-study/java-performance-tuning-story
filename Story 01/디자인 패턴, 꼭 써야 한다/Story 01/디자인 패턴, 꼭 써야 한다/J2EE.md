## J2EE
 - J2EE란 Java 2 Enterprise Edition (J2EE라고 불리었으나 버전 5.0 이후로 Java EE로 개칭됨)
 - J2EE는 자바 기술로 기업환경의 어플리케이션을 만드는데 필요한 스펙들을 모아둔 스펙 집합
 - Java로 구현된 기술로 Java의 기본적인 특징인 플랫폼 독립성을 가지고 있음
 - Client단에 J2EE는 순수 HTML 지원. HTML, 다른 포맷 data 를 만들어 제공하기 위해서는 JSP, servlet code 가 필요함
 
 대표적인 구성요소
 EJB(Enterprise Java Bean)는 J2EE핵심기술. EJB 서버는 스레딩, 동시성제어, 보안, 메모리 관리 등을 지원하는 함수들 제공
 JDBC(자바 데이터베이스 연결성)을 제공. 자바 데이터베이스를 위한 표준 인터페이스
 Servlet은 클라이언트가 보내는 HTTP 요청을 처리하는 서버측 자바 프로그램이며, Servlet 엔진이 있어야 함
 JSP(Java Server Pages)은 HTML이나 Java 코드를 써서 사용자에게 정보를 보여 주며, JSP가 처음 실행될 때 Servlet 엔진이 이것을 Servlet으로 컴파일시켜서 내부적으로는 Servlet으로 동작함
 GUI (그래픽 사용자 인터페이스)를 요구하는 일 없이 개발자들에게 일관성을 향상시켜주는 자바 서블릿 API 제공
 
 
 ## 스프링이랑 J2EE는 다른건가?
  - 스프링 프레임워크 또한 J2EE application 이다.
  - 스프링은 J2EE를 대체할 수 없고, 다만 스프링은 J2EE 만족한다.
  - 우리가 작성한 코드는 스프링 컨테이너에게 뭘 해야할지를 알리고, 스프링은 J2EE 클래스들에게 이야기를 전달한다.
  - J2EE 없이는 스프링 프레임워크 또한 없다.
