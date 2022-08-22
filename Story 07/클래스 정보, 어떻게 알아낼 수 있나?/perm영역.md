
## java perm 영역
 - java 8 버전부터는 perm 영역 사라지고 metaspace 영역으로 대체되었다. 
 
 ### perm 영역이란
 - 펌( PERM : Permanent Generation) 영역은 객체의 생명주기가 영구적일 것으로 생각하는 객체들을 관리함
 - Class의 meta 정보, Method의 meta 정보, static object, Class와 관련된 배열 객체 meta정보 등 정보들을 저장하는 영역이다.
 - 어떤 클래스 정보를 로드할 때, 해당 클래스의 정보만 로드하는 것이 아니라, 클래스에 연관된 다른 클래스들도 같이 로드되고 펌 영역에 할당된다
 - Permanent Heap으로 JVM에 의해 관리되는 영역
 - JVM에 의해 영역의 크기가 결정됨
 
 ### metaspace 영역 이란
  - 기존에 Perm 영역에 저장되었던 Class의 Meta정보가 이 영역에 저장됨
  - Heap이 아닌 Native Memory 영역에 위치하며 JVM이 아닌 OS레벨에서 관리
  - 영역 크기의 제한이 없음
  
  
 : metaspace 영역은 perm 영역에 비해 비교적 OOM 발생 확률이 적다.
