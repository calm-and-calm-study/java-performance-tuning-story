# Overview

---

스프링 부트에서는 운영 애플리케이션을 쉽게 관리할 수 있는 Actuator를 제공합니다. 애플리케이션을 모니터링하고 관리할 수 있는 기능을 간편하게 만들 수 있습니다.

## Spring Boot Actuator

Spring Boot [공식 Reference](https://docs.spring.io/spring-boot/docs/2.1.8.RELEASE/reference/html/production-ready.html)에 나와있는 Actuator에 대한 설명

> Spring Boot includes a number of additional features to help you monitor and manage your application when you push it to production. You can choose to manage and monitor your application by using HTTP endpoints or with JMX. Auditing, health, and metrics gathering can also be automatically applied to your application.
> 

어플리케이션을 모니터링하고 관리하는 기능을 Spring Boot에서 자체적으로 제공한다.

## 운영 애플리케이션 관리

서버를 운영하는 팀에서는 다음과 같은 것을 궁금해한다.

- 애플리케이션 서버에 ping을 확인할 수 있는지
- 모니터링 지표를 볼 수 있는지
- 통계 데이터를 확인할 수 있는지
- 서버 세부 상태를 확인할 수 있는지
- DB 연결 사용과 사용에 문제가 있는지

이에 대한 데이터를 직접 컨트롤러를 만들어서 대응할 수 있지만 actuator를 도입하면 빠르게 도입할 수 있다.

**actuator 의존성**

```
<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

```

스프링 부트 액추에이터를 추가하고 실행하면 여러 가지 컴포넌트가 추가된다. 액츄에이터는 다음과 같은 기능을 바로 사용할 수 있다.

### 애플리케이션 정상 상태 점검 : /actuator/health

[http://localhost:8080/actuator/health](http://localhost:8080/actuator/health)

> JSON 결과를 그대로 노출해주므로 브라우저에 JSON 뷰어를 설치하면 쉽게 확인할 수 있다.
> 

`application.properties`에 `management.endpoint.health.show-details=always`를 추가하면 서버 상태에 대한 자세한 정보를 확인할 수 있다.

```
// 20210724202539
// <http://localhost:8080/actuator/health>

{
  "status": "UP",
  "components": {
    "diskSpace": {
      "status": "UP",
      "details": {
        "total": 494897459200,
        "free": 95440420864,
        "threshold": 10485760,
        "exists": true
      }
    },
    "mongo": {
      "status": "UP",
      "details": {
        "version": "3.5.5"
      }
    },
    "ping": {
      "status": "UP"
    }
  }
}

```

이외에도 레디스(redis), 카산드라(cassandra), 래빗엠큐(RabbitMQ), 관계형DB, 이메일 등 다른 모듈을 스프링부트와 함께 사용하면 스프링 부트 액추에이터가 해당 모듈의 HealthIndicator 구현체를 찾아 등록한다. 각 구현체는 UP, DOWN, OUT_OF_SERVICE, UNKNOWN 중 하나를 status 값으로 반환한다.

### 컴포넌트의 버전 정보 : **/actuatior/info**

애플리케이션에 사용된 컴포넌트의 버전 정보를 알려주는 api이다. 배포된 애플리케이션 버전을 쉽게 확인할 수 있다. 아래처럼 프로퍼티에 작성하면 애플리케이션 버전 정보를 입력할 수 있다.

`application.properties`

```
info.project.version=@project.version@
info.java.version=@java.version@
info.spring.framework.version=@spring-framework.version@
info.spring.data.version=@spring-data-bom.version@

```

메이븐을 사용하면 @로 감싸진 데이터의 실젯값이 자동으로 입력된다. 깃을 사용하고 있으면 다음과 같은 스프링 부트 메이븐 플러그인을 추가해서 커밋과 브랜치 정보도 확인할 수 있다.

```
<plugin>
	<groupId>pl.project13.maven</groupId>
	<artifactId>git-commit-id-plugin</artifactId>
</plugin>

```

[http://localhost:8080/actuator/info](http://localhost:8080/actuator/info)

```
// 20210724205940
// <http://localhost:8080/actuator/info>

{
  "project": {
    "version": "0.0.1-SNAPSHOT"
  },
  "java": {
    "version": "1.8.0_275"
  },
  "spring": {
    "framework": {
      "version": "5.3.8"
    },
    "data": {
      "version": "2021.0.2"
    }
  }
}

```

고객이 최신 버전을 사용 중인지 쉽게 판별할 수 있다.

### 다양한 액추에이터 엔드포인트

모든 엔드포인트 공개

application.properties

```
management.endpoints.web.exposure.include=*

```

모든 액추에이터 엔드포인트를 웹으로 공개하는 것은 간단하다. 하지만 이렇게 서버의 운영 상태를 웹에 전부 공개하면 안된다. 따라서 공개할 엔드 포인트를 하나하나 명시해야 안전성을 확보할 수 있다.

공개할 엔드포인트를 명시적으로 지정

```
management.endpoints.web.exposure.include=audittevents,beans,caches,conditions,configprops,env,flyway,health,heapdump,httptrace,info,logfile,loggers,metrics,mappings,shutdown,threaddump

```

### 로깅 정보 엔드포인트 : /actuator/loggers

loggers 엔드포인트를 공개하면 사용중인 모든 로거와 로그 레벨 정보를 확인할 수 있다.

[http://localhost:8080/actuator/loggers](http://localhost:8080/actuator/loggers)

```
// 20210724210026
// <http://localhost:8080/actuator/loggers>

{
  "levels": [
    "OFF",
    "ERROR",
    "WARN",
    "INFO",
    "DEBUG",
    "TRACE"
  ],
  "loggers": {
    "ROOT": {
      "configuredLevel": "INFO", // 1
      "effectiveLevel": "INFO"   // 2
    },
...

```

1. ROOT 로거는 스프링 부트에 의 INFO 레벨로 기본으로 추가된다.
2. 다른 정책으로 로그 레벨을 변경하지 않았으므로 실제 적용 레벨(effective level) 값도 INFO다.
3. 애플리케이션 최상위 패키지인 com은 로그 레벨이 명시적으로 지정되지 않았다.
4. com 패키지에 대한 로그 레벨이 INFO로 지정됐다.

로그 레벨 지정 해제

```
curl -v -H 'Content-Type: application/json' -d '{"configuredLevel":"TRACE"}'  <http://localhost:8080/actuator/loggers/com.greglturnquist/>

```

### 스레드 정보 확인 : **/actuator/threaddump**

쓰레드 정보를 확인할 수 있다.

[http://localhost:8080/actuator/threaddump](http://localhost:8080/actuator/threaddump)

```

{
      "threadName": "reactor-http-nio-11",
      "threadId": 71,
      "blockedTime": -1,
      "blockedCount": 0,
      "waitedTime": -1,
      "waitedCount": 0,
      "lockName": null,
      "lockOwnerId": -1,
      "lockOwnerName": null,
      "inNative": true,
      "suspended": false,
      "threadState": "RUNNABLE",
      "stackTrace": [
        {
          "methodName": "poll0",
          "fileName": "WindowsSelectorImpl.java",
          "lineNumber": -2,
          "className": "sun.nio.ch.WindowsSelectorImpl$SubSelector",
          "nativeMethod": true
        },
..

```

### 스레드 정보 확인 : /actuator/heapdump

현재 애플리케이션에서 사용되고 있는 모든 스레드의 정보를 확ㅇ니할 수 있다. 리액터 기반으로 처리되는 로직은 모두 리액터 스레드에서 실행된다. 그리고 리액터에 사용되는 스케줄러는 기본적으로 CPU 코어 하나당 한 개의 스레드만 생성된다. 부하가 많이 걸릴때나 없을 때 스레드 정보를 확인하면 쓰레드 수준에서 확인할 수 있다.

### 힙 정보 확인 : /actuator/heapdump

힙 덤프 파일 확인

```
jhat ~/Downloads/heapdump

sdk list visualvm
sdk install visualvm 2.0.6
visualvm --jdkhome $JAVA_HOME

```

- 힙 히스토그램
- 플랫폼 포함 모든 클래스의 인스턴스 개수
- 플랫폼 제외 모든 클래스의 인스턴스 개수

자세한 분석은 VisualVM을 따로 설치해서 사용하면 된다.

### HTTP 호출 트레이싱 : /actuator/httptrace

[http://localhost:8080/actuator/httptrace](http://localhost:8080/actuator/httptrace)

애플리케이션을 누가 호출하는지 쉽게 볼 수 있는 기능을 제공한다.

- 가장 많이 사용되는 클라이언트 유형은? 모바일? 브라우저?
- 어떤 언어로된 요청이 가장 많은가?
- 가장 많이 요청되는 엔드포인트는?요청이 가장 많이 발생하는 지리적 위치는?

스프링부트는 HttpTraceRepository 인터페이스를 제공하고 이 인터페이스를 구현한 빈을 자동으로 찾아서 요청 처리에 사용한다. 메모리 기반으로 동작하는 InMemoryHttpTraceRepository는 가장 간편하게 사용할 수 있는 구현체다.

```
@Bean
HttpTraceRepository traceRepository(){
	return new InMemoryHttpTraceRepository();
}

```

이 빈이 감지되면 /actuator/httptrace 엔드포인트를 자동으로 활성화한다. 이를 스프링 웹플럭스와 연동해서 모든 웹 요청을 추적하고 로그를 남긴다.

- 타임스탬프
- 보안 상세정보
- 세션 ID
- 요청 상세정보(HTTP 메소드, URI, 헤더)
- 응답 상세정보(HTTP 상태 코드, 헤더)
- 처리시간

### 그 밖의 엔드포인트

### 액추에이터 엔드포인트

다만 이러한 Actuator가 제공하는 정보는 서버에 민감한 정보이므로 보안에 신경을 많이 써야한다. 또한 데이터가 메모리상에서 저장이 되기 때문에 영속화 시킬 수 있는 방법을 구상해야한다.

# Reference

[스프링 부트 실전 활용 마스터 - 교보문고](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791189909307&orderClick=LEa&Kc=)

| 액추에이터 엔드포인트 | 설명 |
| --- | --- |
| /actuator/auditevents | 감사(audit) 이벤트 표시 |
| /actuator/beans | 직접 작성한 빈과 자동설정에 의해 애플리케이션 컨텍스트에 등록된 모든 빈 표시 |
| /actuator/caches | 모든 캐시 정보 표시 |
| /actuator/conditions | 스프링 부트 자동설정 기준 조건 표시 |
| /actuator/configprops | 모든 환경설정 정보 표시 |
| /actuator/env | 현재 시스템 환경 정보 표시 |
| /actuator/flyway | 등록된 플라이웨이(Flyway) 데이터베이스 마이그레이션 도구 표시 |
| /actuator/mappings | 모든 스프링 웹플럭스 경로 표시 |
| /actuator/metrics | 마이크로미터(micrometer)를 사용해서 수집하는 지표(metrics) 표시 |
