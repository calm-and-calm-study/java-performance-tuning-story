# Overview

---

WAS와 DB 사이의 연결을 할 때 가장 비용이 많이 드는 작업은 DB와의 Connection입니다. 하지만 요청이 들어올 때마다 DB와의 커넥션을 그때 그때 맺어준다면 이에 대한 부하는 꽤나 복잡해질 수 있습니다. 이를 보완할 수 있는 방법이 Connection Pool입니다.

![image](https://user-images.githubusercontent.com/66561524/185721876-e9c71752-d87c-4e49-b349-ebf05ff6b368.png)

IBM’s article [https://www.ibm.com/developerworks/data/library/techarticle/dm-1105fivemethods/index.html](https://www.ibm.com/developerworks/data/library/techarticle/dm-1105fivemethods/index.html)

데이터베이스와 연결된 커넥션을 미리 만들어놓고 이를 pool로 관리하는 것입니다. 필요할 때마다 커넥션 풀의 커넥션을 이용하고 반환하는 기법입니다. 미리 만들어놓고 커넥션을 이요하면 Connection의 비용을 줄이고 DB에 빠르게 접근할 수 있습니다.

또한 커넥션 수를 제한하여 서버 자원 고갈을 방지할 수 있고 DB 접속 모듈을 공통화해서 DB 서버의 환경이 바뀔 경우 유지보수를 쉽게 할 수 있습니다.

가령 MySql DB의 Connection Pool size가 200이며 MAX Connection Pool size가 10인 경우 20개 이상의 서버를 띄우면 max connection이 초과될 수 있어 서버 쪽에서 미리 제한할 수 있습니다.

# Connection Pool Size

---

그렇다면 하나의 서버를 기동시킬 때, Connection Pool의 사이즈는 크기만 하면 좋을까요? 그렇지 않습니다. WAS에서 Connection을 사용하는 주체는 Thread 입니다. 따라서 Thread Pool에서 트랜잭션을 처리하는 Connection 외의 Connection은 메모리 공간만 차지하게 됩니다.

즉, Thread Pool이 10개이고 Thread 당 처리하는 Connection이 최대 2개이면 한 어플리케이션에서 사용하는 Connection은 최대 20개가 됩니다. 이 때 Connection Pool Size가 20개가 넘을 필요가 없습니다. 

이상적인 Thread Pool은 CPU 개수와 동일할 때 빠른 성능으로 보이므로 사실상 Connection Pool 숫자는 제한적으로 늘릴 필요가 있습니다. 하지만 반대로 Connection Pool의 숫자가 Thread에서 사용하려는 Connection의 숫자보다 적으면 Connection Pool에 여분이 없어 다른 Connection 반납을 기다려야 하므로 DeadLock이 발생합니다.

우아한형제들 기술 블로그에서 적절한 Connection Pool Size를 설정하는 공식을 알려주었습니다.

![https://techblog.woowahan.com/wp-content/uploads/img/2020-02-06/magic-formula.png](https://techblog.woowahan.com/wp-content/uploads/img/2020-02-06/magic-formula.png)

- *Tn* : 전체 Thread 갯수
- *Cm* : 하나의 Task에서 동시에 필요한 Connection 수

HikariCP wiki에서는 이 공식대로 Maximum pool size를 설정하면 Dead lock을 피할 수 있다고 합니다. 자세한 내용은 아래 블로그를 확인하시면 됩니다.

[HikariCP Dead lock에서 벗어나기 (실전편) | 우아한형제들 기술블로그](https://techblog.woowahan.com/2663/)

# HikariCP

---

HikariCP는 2012년 개발된 가볍고 빠르고 안정적인 JDBC Connection Pool입니다. Spring Boot 2.x부터는 default JDBC connection pool로 선정되기 도했습니다.

![image](https://user-images.githubusercontent.com/66561524/185721882-3f3734c9-4b65-4b14-82d1-47f5e7b22c56.png)

[https://github.com/brettwooldridge/HikariCP](https://github.com/brettwooldridge/HikariCP)

그림에서 볼 수 있듯이 다른 Connection Pool에 비해 압도적인 성능을 지녔습니다.

HikariCP의 소스코드를 뜯어보면 다음과 같습니다. (우아한형제들 기술 블로그 참조)

HikaraPool에서 getConnection() 로직은 3단계를 통해 Connection을 반환하고 있습니다. 내부적으로 ConcurrentBag이라는 구조체를 이용하여 Connection을 리턴하는데요. 애플리케이션이 hikari connection pool에서 들고있는 connection을 빌리려면 다음과 같은 과정을 거치게 됩니다.

![image](https://user-images.githubusercontent.com/66561524/185721889-3cc29ecb-acf3-4690-8cdb-0ce5341bd9e6.png)

- 이전에 사용했던 connection을 우선으로 빌려줍니다.
- 사용가능한 connection이 없으면 handoffqueue에 등록하여 다음 connection의 반납을 기다린다 (FIFO)
- connection timeout이 지나면 connection을 획득하지 못한다.

애플리케이션이 사용한 connection을 반납하려면 다음과 같은 과정을 거치게 됩니다.

![image](https://user-images.githubusercontent.com/66561524/185721894-50132d39-aa9b-4f1b-bbda-89932f983685.png)

- 커넥션 반납은 connection.close()를 기점으로 시작된다. 따라서 dead lock을 방지하기 위해서는 가능한 빠른 시점에 connection을 닫아줘야 한다.
- 해당 connection을 사용가능한 connection(idel)으로 바꾼다.
- hansOdffQueue에서 connection을 기다리는 스레드가 있으면 해당 스레드에게 주고 아니면 pool에다가 다시 담는다.

# 정리

---

결론적으로 책에서 나온대로 ResultSet, Statement, Connection을 최대한 빠르게 close() 해줘야 합니다. 이 방법으로는 try-with-resource를 권장합니다. 또한 Connection Pool Size도 사용하는 Thread, Connection per Thread, 리소스의 CPU, DB의 Max Connection Pool 등을 고려해서 Dead Lock을 막는 것이 우리 개발자들에게는 가장 현실적인 성능 최적화 방법일 것 입니다.

# Reference

---

[DB 커넥션 풀(Connection pool)이란? HikariCP란?](https://code-lab1.tistory.com/209)

[HikariCP 소개](https://yjh5369.tistory.com/entry/HikariCP-%EC%86%8C%EA%B0%9C)

[HikariCP Dead lock에서 벗어나기 (이론편) | 우아한형제들 기술블로그](https://techblog.woowahan.com/2664/)

[](https://www.baeldung.com/hikaricp)

[HikariCP) Connection Pool 설정 및 확인하기](https://shanepark.tistory.com/338)

[내가 만든 서비스는 얼마나 많은 사용자가 이용할 수 있을까? - 3편(DB Connection Pool)](https://hyuntaeknote.tistory.com/12)

[HikariCP Dead lock에서 벗어나기 (실전편) | 우아한형제들 기술블로그](https://techblog.woowahan.com/2663/)
