### HashMap의 정의

---

- 키에 대한 해시 값을 사용하여 값을 저장하고 조회하며, 키-값 쌍의 개수에 따라 동적으로 크기가 증가하는 associate array

### HashMap에서 사용하는 hash함수

---

- HashMap은 객체의 hashCode() 메서드가 반환하는 값을 사용하는데, 메서드 결과 값의 자료형은 int이다.
- 32비트 정수 자료형으로는 완전한 자료 해시 함수를 만들 수 없다
    - 완전한 해시 함수 : 동일하지 않은 어떤 객체 X와 Y가 있을 때, 즉 X.equals(Y)가 '거짓'일 때 X.hashCode() != Y.hashCode()가 같지 않다면, 이때 사용하는 해시 함수
    - 생성 가능한 객체의 수가 2^32보다 많을 수도 있고, 모든 HashMap객체에서 O(1)를 보장하려면 모든 HashMap이 원소가 2^32인 배열을 갖고 있어야 하기 때문이다.
- 그러므로 HashMap에서는 메모리를 절약하기 위해 실제 해시 함수의 표현 정수 범위 |N| 보다 작은 M개의 원소를 갖는 배열만 사용한다. 아래와 같이 hashCode()의 나머지 값을 해시 버킷 인덱스 값으로 사용한다.
    
    ```java
    int index = X.hashCode() % M; 
    ```
    
    - 버킷(Bucket) : 하나의 주소를 갖는 한 구역
    

### HashMap에서 해시 충돌

---

- hashCode()의 나머지 값을 인덱스 값으로 사용하면, 서로 다른 hash code를 갖는 객체가 1/M의 확률로 같은 해시버킷을 사용하게 된다.
- 해시 충돌을 해결하는 방식에는 대표적으로 두 가지가 있다.
    - Open Addressing : 데이터를 삽입하려는 해시 버킷이 이미 사용 중인 경우 다른 해시 버킷에 해당 데이터를 삽입하는 방식이다.
    - Separate Chaining : 동일한 버킷에 값이 있으면 linkedList로 value를 존재하는 값의 뒤에 연결하는 방식.
    
    ![image](https://user-images.githubusercontent.com/64389364/184207798-13a08a0b-b645-43f6-aabb-f15a1c56b01e.png)
    
- 두 방식 모두 최악의 경우 O(M)의 시간복잡도를 가진다.
- Open Addressing은 연속된 공간에 데이터를 저장하기 때문에 Separate Chaining에 비해 캐시 효율이 높다.
- 하지만, M값이 커질수록 L1, L2 캐시 적중률이 낮아진다. 그러므로 HashMap에 저장된 키-값 쌍 개수가 일정 개수 이상으로 많아지면, 일반적으로 Open Addressing은 Separate Chaining보다 느리다.
- Java HashMap에서 사용하는 방식은 Separate Channing이다.

### Java8에서 Separate Chaining

---

- Java 8에서는 이전 버전과는 다르게, 데이터의 개수가 많아지면 Seperate Chaining에서 LinkedList 대신 Tree를 사용한다.
- Tree를 사용하는 기준은 해시 버킷에 할당된 키-값 쌍의 개수이다. 하나의 해시 버킷에 8개의 키-값 쌍이 모이면 LinkedList를 Tree로 변경하고, 해당 버킷에 있는 데이터를 삭제해서 개수가 6개가 되면 다시 LinkedList로 변경한다.
    
    ```java
    /**
     * The bin count threshold for using a tree rather than list for a
     * bin.  Bins are converted to trees when adding an element to a
     * bin with at least this many nodes. The value must be greater
     * than 2 and should be at least 8 to mesh with assumptions in
     * tree removal about conversion back to plain bins upon
     * shrinkage.
     */
    static final intTREEIFY_THRESHOLD= 8;
    
    /**
     * The bin count threshold for untreeifying a (split) bin during a
     * resize operation. Should be less than TREEIFY_THRESHOLD, and at
     * most 6 to mesh with shrinkage detection under removal.
     */
    static final intUNTREEIFY_THRESHOLD= 6;
    ```
    

- Java 8 HashMap에서는 Entry 클래스 대신 Node 클래스를 사용한다. Java7과 다른 점은 LinkedList에서 Tree로 변경할 수 있도록 sub class인 TreeNode가 존재한다는 점이다. 이때 사용하는 Tree가 Red-Black Tree이며, Tree 순회시 사용하는 대소 판단 기준은 hashCode() 값이다.
  ```java
     * Basic hash bin node, used for most entries.  (See below for
     * TreeNode subclass, and in LinkedHashMap for its Entry subclass.)
     */
    static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;
		...
  ```
