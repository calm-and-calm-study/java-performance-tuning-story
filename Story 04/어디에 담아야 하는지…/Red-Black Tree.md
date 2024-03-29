# Overview

책에서 `TreeSet`과 `TreeMap`은 Red Black Tree에 담겨진다고 나와있었습니다. red-black 트리에 예전에 제가 정리한 내용을 공유해드립니다. 하지만 내용이 물리고 물리는 내용이 많아 최소한만 가져왔습니다. 트리, 이진탐색, 이진탐색트리, 2-3 트리, AVL 트리가 모두 합쳐진 내용이 red-black 트리여서 고민하다가 이 내용을 가져왔습니다.

# 트리(Tree)

우선 일반적인 트리를 먼저 알아야한다. 

일반적인 트리(General Tree)는 실제 트리를 거꾸로 세워 놓은 형태의 자료구조이다. HTML과 XML의 문서 트리, 자바 클래스 계층구조, 운영체제의 파일시스템, 탐색트리, 이항(Binomial)힙, 피보나치(Fibonacci)힙과 같은 우선순위 큐에서 사용되는 자료구조이다.

> 트리는 empty이거나, empty가 아니면 루트노드 R과 트리의 집합으로 구성되는데 각 트리의 루트노드는 R의 자식노드이다. 단, 트리의 집합은 공집합일 수도 있다.

![image](https://user-images.githubusercontent.com/66561524/183315188-e2c3cda7-faee-42bd-858b-6b409e3106dc.png)

용어

- 루트(Root)노드 - 트리의 최상위에 있는 노드
- 자식(Child)노드 - 노드 하위에 연결된 노드
- 차수(Degree) - 자식노드 수
- 부모(Parent)노드 - 노드의 상위에 연결된 노드
- 이파리(Leaf)노드 - 자식이 없는 노드
- 형제(Sibiling)노드 - 동일한 부모를 가지는 노드
- 조상(Ancestor)노드 - 루트노드까지의 경로상에 있는 모든 노드들의 집합
- 후손(Descendant)노드 - 노드 아래로 매달린 모든 노드들의 집합
- 서브트리(Subtree) - 노드 자신과 후손노드로 구성된 트리
- 레벨(Level) - 루트노드가 레벨 1에 있고, 아래 층으로 내려가며 레벨이 1씩 증가한다. 레벨은 깊이(Depte)와 같다.
- 높이(Height) - 트리의 최대 레벨
- 키(Key) - 탐색에 사용되는 노드에 저장된 정보

# 이진탐색트리(Binary Search Tree)

---

이진탐색트리는 이진탐색의 개념을 트리 형태의 구조에 접목한 자료구조이다. 이진탐색은 정렬된 데이터의 중간에 위치한 항목을 기준으로 데이터를 두 부분으로 나누어 가며 특정 항목을 찾는 탐색방법이다.  [이진 탐색](https://www.notion.so/8cb92eb8fda84a3599d5324c8c3be5d6) 

트리 형태의 자료구조에서 이진 탐색을 수행하기 위해 정렬된 리스트를 단순 연결리스트로 만둔 후 점진적으로 이진트리 형태로 변환해가는 과정

![image](https://user-images.githubusercontent.com/66561524/183315196-c112c416-003e-480b-b251-308487c3382b.png)

![image](https://user-images.githubusercontent.com/66561524/183315198-ad00a703-c760-43f3-8ed1-e283122abed0.png)

단순연결리스트에서 각 노드가 다음 노드만을 가리키므로 이진탐색이 쉽지 않다. 하지만 중간 노드를 중심으로 좌측 노드들은 이전 노드를 가리키도록 하고 같은 방법으로 각각 좌우측 노드들에게 재귀적으로 적용하여 바꾼 뒤 마지막으로 연결리스트의 중간 노드를 위로 끌어당기면 트리가 완성된다. 단순연결리스트로 구현한 이진탐색트리에는 이진탐색이 이와 같이 내포되어있다.

> 이진탐색트리는 이진탐색을 수행하기 위해 단순연결리스트를 변형시킨 자료구조이다.

이진탐색트리의 특징 중 하나는 트리를 **중위순회하면 정렬된 출력**을 얻는 다는 점이다.

![image](https://user-images.githubusercontent.com/66561524/183315206-e40ccbc8-0a14-482f-a610-91a1d2a7faff.png)

![image](https://user-images.githubusercontent.com/66561524/183315209-8f1f92f8-167a-44e0-8845-909202d8e61a.png)

이진탐색트리는 기본적인 트리 형태의 자료구조이다. 균형(Balanced) 이진탐색트리, B-트리, 다방향(Multi-way) 탐색트리가 이진탐색트리에 기반해서 구현되었다. 이진탐색트리는 데이터베이스 등의 대용량 데이터 저장의 기본 개념으로 활용된다.

**정의**

이진탐색트리는 이진트리로서 각 노드가 다음과 같은 조건을 만족한다.

1. 각 노드 n의 키값이 n의 왼쪽 서브트리에 있는 노드들의 키값들보다 크다.
2. n의 오른쪽 서브트리에 있는 노드들의 키값들보다 작다.

# 레드블랙트리(Red-Black Tree)

레드블랙트리는 노드에 색을 부여하여 트리의 균형을 유지하며 탐색,삽입,삭제 연산의 수행시간이 각각 $O(logN)$을 넘지 않은 효율적인 자료구조이다. 일반적인 레드블랙트리는 삽입이나 삭제를 수행할 때 균형을 유지하기 위해 상당히 많은 경우를 고려해야 하고 이에 따라 프로그램이 복잡해지고 길이가 증가된다. 하지만 좌편향 레드블랙(Left-Leaning Red-Black, LLRB)트리는 삽입이나 삭제시 고려해야 하는 경우의 수가 적어 프로그램의 길이가 일반 레드블랙 트리에 1/5에 불과하다. 또한 LLRB 트리는 AVL트리, 2-3트리, 2-3-4트리, 일반 레드블랙트리보다 우수한 성능을 갖는 자료구조이다.

> LLRB트리는 2-3 트리에서 3-노드의 두 개의 키를 두 노드로 분리 저장하고 하나는 레드 다른 하나는 블랙으로 만든 형태와 같다.

![image](https://user-images.githubusercontent.com/66561524/183315220-0b5858fc-a39b-4829-8360-a6e0f67076e7.png)

LLRB트리는 개념적으로 2-3 트리와 같아 2-3 트리의 장점인 완전균형트리의 형태를 내포하고 있다. LLRB 트리의 노드는 블랙 또는 레드의 두 가지 색 정보를 가진다. 노드와 부모 노드를 연결하는 link의 색은 노드의 색과 같다. 따라서 LLRB 트리에서는 link의 색을 별도로 저장하지 않는다. 노드 n의 왼쪽 자식노드는 레드이고 그 연결 link도 레드이며 n의 오른쪽 자식노드는 블랙이고 그 연결 link도 블랙인 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/66561524/183315225-fe7d054c-9872-4509-838d-ebc4a8b6a2c5.png)

**정의**

LLRB 트리는 이진탐색트리로서 네 가지 조건을 만족한다.

- 루트노드와 null은 블랙이다.
- 루트노드로부터 각 null까지 2개의 연속된 레드 link는 없다. (연속 레드 link 규칙)
- 루트노드로부터 각 null까지의 경로에 있는 블랙 link 수는 모두 같다. (동일 블랙 link 수 규칙)
- 레드 link는 왼쪽으로 기울어져 있다. (레드 link 좌편향 규칙)

RedBlackTree 클래스는 Node 클래스를 내부(Inner) 클래스로 갖는다. Node 객체는 id(키), name(키에 관련된 정보), 왼쪽 자식과 오른쪽 자식을 참조하기 위한 left와 right를 가진다.
