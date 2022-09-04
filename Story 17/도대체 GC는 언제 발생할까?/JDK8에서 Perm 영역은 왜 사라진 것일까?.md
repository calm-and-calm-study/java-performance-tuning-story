Java7 HotSpot JVM 구조

![image](https://user-images.githubusercontent.com/64389364/188302614-c1630c89-0e33-445e-b77d-34c68be39768.png)


- Java7의 Perm 영역에는 아래 데이터들이 저장되었다.
    - Class의 메타데이터
    - Method의 메타데이터
    - static Object, static 상수
    - Class와 관련된 배열 객체 메타데이터
    - JVM 내부적인 객체들과 JIT 관련 데이터
    

이 Permanent 영역은 JDK8에서는 삭제되었고, 아래 그림처럼 Metaspace라는 영역으로 대체되었다. 

```
Oracle의 What’s New in JDK8

HotSpot
- Removal of PermGen.
```


Java8 HotSpot JVM 구조

![image](https://user-images.githubusercontent.com/64389364/188302620-4854e513-5b02-4308-b7b5-83153956367c.png)


- Metaspace는 JDK7의 Perm 영역을 대체하는 것으로 Class와 Method의 메타데이터들이 저장되는 영역이다.
- Metaspace영역은 Native Memory영역으로 JVM이 아닌 OS에 의해 관리된다.

### 그래서 Perm 영역이 사라지고 Metaspace 영역으로 대체된 이유는?

Permanent 영역에는 위의 `Java7 HotSpot JVM 구조`에서 보다시피 Class와 Method의 메타데이터가 저장되었는데, 이 영역은 OS, JVM 버전마다 각기 다른 default 값을 가지고 있었고, 대부분 아주 작게 할당되어 있었다. 그래서 jdk 7 까지는 클래스 로딩을 많이 하다보면 `OutOfMemoryError: PermGen Space error` 이 발생하곤 했다.

이런 OOM에러의 발생을 막기 위해 JDK8에서는 Static Object를 Heap영역으로 이동시켜서 GC의 대상이 되게 했다. 그리고 Metaspace는 Class 메타 데이터를 native 메모리에 저장하고, 메모리가 부족할 경우 이를 자동으로 늘려주게 됐다.

Reference
- [JDK 8에서 Perm 영역은 왜 삭제됐을까](https://johngrib.github.io/wiki/java8-why-permgen-removed/)

- [Java 8 에서 사라진 maxPermSize, PermSize을 대체하는 옵션?](https://blog.voidmainvoid.net/184)

- [Java 8에서 JVM의 변화 : PermGen이 사라지고 Metaspace가 등장하다.](https://goodgid.github.io/Java-8-JVM-Metaspace/)
