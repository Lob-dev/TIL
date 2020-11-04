2020.10.11

<br/>

### Java의 Array와 List Collection Interface (DataStructure) 차이
- Array는 불변적인 선형 자료구조, List는 가변적인 선형 자료구조
- 추가 설명 : Array은 메모리에서 연속적으로 값을 저장한 불변적인 선형 자료구조
- List는 가변적으로 노드 (데이터) 를 추가하여 저장할 수 있는 가변적인 선형 자료구조

- 동작 원리 : Array는 연속된 인덱스를 통해 접근 시 해당 위치를 계산이 가능하여 랜덤 엑세스 할 수 있다. List는 진입점인 노드를 이용하여 탐색해서 접근해야 한다... 등 (추가 질문이 있을 경우..)

<br/>

### 해당 List Node가 마지막 노드인 것을 아는 방법?

노드의 next 라는 다음 노드를 가리키는 값이 null인 경우 해당 노드가 마지막 인 것을 알 수 있다.

<br/>

### 해당 List Node가 첫번째 노드인 것을 아는 방법?

노드의 prev 라는 이전 노드를 가리키는 값이 null인 경우 해당 노드가 마지막 인 것을 알 수 있다.

<br/>

### 자료구조로써의 Linked List의 장점? 

배열에 비해 추가 / 삭제가 용이하고, 가변적으로 데이터를 추가할 수 있지만 특정 데이터를 찾기 위해서는 순차 탐색을 하므로 탐색 속도가 느린 단점이 있다. 

<br/>

### ArrayList [non-synchronized]

크기가 불변적인 Array를 개선한 가변적인 선형리스트. 배열과 같이 값 접근시 Random Access 를 사용할 수 있는데, 그 이유는 순차적인Index를 기준으로 내부의 객체, 값을 관리하기 때문에 Index를 통해 해당 위치를 유추할 수 있기 때문입니다. (내부 구조가 배열.)

<br/>

### Java의 Array List가 가득 찼을 경우 늘어나는 메모리 크기(영역)는? 

Array List 내부적으로 설정된 변수인 size의 값을 증가시키다가 elementData.length와 비교하여 현재 길이가 같은지 검증하고 증가한다. (size 10 → 10 + (10 >> 1) → 15 ) 

인자값이 없는 생성자로 호출했을 경우에는 Default Capacity 인 10만큼 생성된다.

ArrayList의 최대 크기는 **Integer.MAX_VALUE - 8, = 2,147,483,639** 이것을 넘을 경우 **OOM**

<br/>

### Java의 Linked List는 해당 모순(구현?)을 어떻게 해결했나요? 

Linked List의 경우 맨 첫번째 노드와 맨 마지막 노드를 따로 참조 변수로 저장하고 있기 때문에  두개의 케이스에 경우에는 바로 접근할 수 있으며 그 외 노드들은 받은 index(위치)를 size 의     중간 값을 기준으로 나누어서 절반씩 탐색한다. 

즉 탐색 부분에 대하여서 어느정도 최적화를 해놓고 진행하고 있다.

```java
Node<E> node(int index) {

    if (index < (size >> 1)) {
        Node<E> x = first;
        for (int i = 0; i < index; i++)
            x = x.next;
        return x;
    } else {
        Node<E> x = last;
        for (int i = size - 1; i > index; i--)
            x = x.prev;
        return x;
    }
}
```

```java
public void add(int index, E element) {
    checkPositionIndex(index);

    if (index == size)
        linkLast(element);
    else
        linkBefore(element, node(index));
}
```


### LinkedList [non-synchronized]

양방향 리스트의 구현체. 공간에 연속적으로 위치된 것이 아니라,     참조값을 통하여서 이전, 다음 노드의 위치를 알고 있기 때문에 탐색시 O(N) 만큼의 시간 복잡도를 가지고 있습니다.