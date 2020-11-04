2020.11.02

<br/>

### Tree Set의 실제 자료구조

<img src="./../image/500px-Red-black_tree_example.svg.png" width="90%"></img>

<br/>

**Red-Black Tree (Binary Search Tree의 일종, Self-Balanced binary search tree 라고도 부른다)**

- 많은 SDK나 라이브러리에서 검색 알고리즘으로 채택되어 사용된다.

- **Binary Search Tree**는 Root Node를 중심으로 왼쪽엔 Key 값이 작은 것, 오른쪽에는 큰 것이 온다.
    - 매번 Root Node보다 작은 값 혹은 큰 값만 들어온다면 한쪽에만 노드가 몰리게 된다.
    - 이 경우 트리의 높이만큼의 탐색시간이 걸리게 된다.

- **Red-Black Tree** 는 **Binary Search Tree** 의 규칙을 깨지않고, 균형을 위해 트리를 부분적으로      재구성하는 방법을 고안하였다.

- **각 노드에 Color 라는 속성 추가하고, 각각에 Red 혹은 Black 를 적용한다.**

    **Red Black Tree의 제약 조건.**

    - **Root Property** : Root Node는 Black 이다.
    - **External Property** : 모든 External Node는 Black 이다.
    - **Internal Property** : Red Node의 Child Node는 Black 이다. (Red Node는 연속적일 수 없다.)
    - **Depth Property** : 모든 Leaf Node에서 Root Node까지 가는 경로에 Black Node는 수와 같다.
    - 해당 조건들을 통하여 Red-Black Tree의 높이를 $LogN$ 에 바운드 한다.

<br/>

**Tree Node의 Flow**

- 모든 삽입된 Node는 기본적으로 Red Node이다.
- Node 삽입 시 **Double Red 가 발생한다면?**
    - **부모 노드의 형제 노드가 Black? → Restructuring**
        - 삽입 Node와 부모 Node, 부모의 부모 Node를 오름차순 정렬한다.
        - 가운데 존재하는 값을 부모 노드로 만들고, 나머지 두개를 자식으로 만든다.
        - 부모가 된 노드를 Black으로, 나머지 두개를 Red Node로 만든다.
        - 기존 부모의 형제 노드는 해당 부모 노드의 색상에 따라서 변경된다.
        - **Restructuring 는 다른 서브 트리에게 영향을 미치지 않으며, 1회만 진행된다.**
            - **Restructuring 의 수행시간은 노드 삽입시 일어남으로 $logn$ 이다.**

    - **부모 노드의 형제 노드가 Red? → Recoloring**
        - 삽입 Node의 부모 Node와 형제 노드를 Black으로 바꾸고 부모의 부모를 Red로 한다.
        - 만약에 Grand Parent가 Root Node가 아닌 상태라면 Double Red가 발생할 수 있다.
        - **Recoloring 는 다른 서브 트리에게 영향을 미칠 수 있으며, 1번 이상 진행될 수 있다.**
            - **Recoloring 또한 노드 삽입시 일어남으로 $logn$ 이 걸릴 수 있다.**