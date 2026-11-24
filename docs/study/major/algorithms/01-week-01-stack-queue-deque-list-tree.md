---
title: "1주차(Stack Queue Deque, List, Tree)"
date: 2022-09-29
last_modified_at: 2022-10-16
category: major
tags: [알고리즘, 스택, 큐, 연결 리스트, 이진 탐색 트리]
---

# 1주차(Stack Queue Deque, List, Tree)

## # `Stack(스택)`

**`Stack(스택)`**

- `스택의 특징` :` LIFO`, Last-In-First-Out  
    → 예제 : 시스템 스택을 이용한 함수 호출 (함수 스택)

- `스택의 연산`

    1. push() : 스택에 데이터를 추가
    2. pop() : 스택에서 데이터를 삭제
    3. is_empty(s) : 스택이 `공백상태`인지 검사
    4. is_full(s) : 스택이 `포화상태`인지 검사
    5. create() : 스택을 생성
    6. peek(s) : 요소를 스택에서 `삭제하지 않고 보기`만 하는 연산  
        (참고) pop 연산은 요소를 스택에서 완전히 삭제하면서 가져온다.

- 스택 추상데이터타입(ADT, Abstract Data Type)

    객체 : 0개 이상의 원소를 갖는 유한 선형 리스트

    `연산`

    1. `create(size)`  
        최대 크기가 size인 공백 스택을 생성한다.
    2. `is_full(s)`  
        if(스택의 원소 수 == size) return TRUE;  
        else return FALSE;
    3. `is_empty(s)`  
        if(스택의 원소 수 == 0) return TRUE;  
        else return FALSE;
    4. `push(s, item)`  
        if (is_full(s)) return ERROR_STACKFULL;  
        else 스택의 맨 위에 item을 추가한다. → top 변수를 이용.
    5. `pop(s)`  
        if(is_empty(s)) return ERROR_STACKEMPTY;  
        else 스택의 맨 위의 원소를 제거해서 반환한다.
    6. `peek(s)`  
        if (is_empty(s)) return ERROR_STACKEMPTY;  
        else 스택의 맨 위의 원소를 제거하지 않고 반환한다.

- `배열`을 이용한 `스택`의 구현

    1. `stack`은 `1차원 배열`을 이용한다.
    2. 스택에서 가장 최근에 입력되었던 자료를 가리키는 `top` 변수를 선언한다.
    3. `top` 변수는 초기에 공백 상태에서는 -1, 자료가 하나 늘어날 때마다 ++되어서  
        가장 최근에 입력된 자료의 index를 가리킨다.
    4. 즉, 가장 먼저 들어온 요소는 `stack[0]`에, 가장 최근에 들어온 것은 `stack[top]`에 저장된다.

- `is_empty`

```c
is_empty(s):

if (top == -1) // is_empty는 비어있으면 top == -1이므로, true를 return한다.
	then return TRUE
	else return FALSE
```

- `is_full`

```c
is_full(s):

if ( top == (MAX_STACK_SIZE - 1) )
	then return TRUE
	else return FALSE
```

- `push 연산`

```c
push(S, x):

if (is_full(s))
	then error "overflow"
	else top <- top + 1
				stack[top] <- x
// push를 할 때, 먼저 stack이 꽉 찼는지 check
// 만약 꽉 찼다면 return 하고
// 빈 공간이 있다면 top++해서 증가된 top의 index에
// 전달된 argument x를 넣는다.
```

- `pop 연산`

```c
pop(S, x):

if (is_empty(S))
	then error "underflow"
	else e <- stack[top]
				top <- top - 1
				return e
// pop을 하면, 가장 최근에 들어온 요소를 return 해줘야한다.
// 그 전에, 현재 스택이 비어있는지를 is_empty로 체크한다.
// 비어있지 않다면, top element를 변수에 저장한 후, top--를 한다.
// 그 후 저장한 top element를 return 한다.
```

---

## # `Queue(큐)`

**`Queue(큐)`**

- Queue

    먼저 들어온 데이터가 먼저 나가는 구조, FIFO (First-In First-Out)

- 큐(Queue) 추상데이터 타입(ADT)

    객체 : 0개 이상의 요소들로 구성된 선형 리스트

    연산

    1. `create(max_size)`  
        최대 크기가 max_size인 공백 큐를 생성한다.
    2. `init(q)`  
        큐를 초기화 한다.
    3. `is_empty(q)`  
        if (size == 0) return TRUE;  
        else return FALSE;
    4. `is_full(q)`  
        if (size == max_size) return TRUE;  
        else return FALSE;
    5. `enqueue(q, e)`  
        if (is_full(q)) queue_full 오류;  
        else q의 끝에 e를 추가한다.
    6. `dequeue(q)`  
        if (is_empty(q)) queue_empty 오류;  
        else q의 맨 앞에 있는 e를 제거하여 반환한다.
    7. `peek(q)`  
        if (is_empty(q)) queue_empty 오류;  
        else q의 맨 앞에 있는 e를 읽어서 반환한다.

- `선형 큐 (Linear Queue)`

    삽입을 계속하기 위해서는 요소들을 이동시켜야 함.  
    초기에 front와 rear는 -1  
    데이터를 하나 추가 (enqueue) 하면, rear++ 후에, rear index에 데이터를 추가한다.  
    이후에 만약 데이터를 제거한다면 (dequeue) front의 데이터를 return하고, front++

---

## # `deque(덱)`

**`deque(덱)`**

- 덱 (deque)은 double-ended enqueue의 줄임말로서 큐의 전단 (front) 와 후단 (rear) 에서 모두 삽입과 삭제가 가능한 큐 (Queue) 를 말한다.

- 덱(Deque) 추상데이터 타입(ADT)

    객체 : n개 element 형으로 구성된 요소들의 순서있는 모임

    연산

    1. `create()`  
        덱을 생성한다.
    2. `init(dq)`  
        덱을 초기화 한다.
    3. `is_empty(dq)`  
        덱이 공백 상태인지를 검사한다.
    4. `is_full(q)`  
        덱이 포화 상태인지를 검사한다.
    5. `add_front(dq, e)`  
        덱의 앞 (front)에 요소를 추가한다.
    6. `add_rear(dq, e)`  
        덱의 뒤 (rear)에 요소를 추가한다.
    7. `delete_front(dq)`  
        덱의 앞 (front)에 있는 요소를 반환한 다음 삭제한다.
    8. `delete_rear(dq)`  
        덱의 뒤 (rear)에 있는 요소를 반환한 다음 삭제한다.
    9. `get_front(dq)`  
        덱의 앞 (front)에서 삭제하지 않고 앞에 있는 요소를 반환한다.
    10. `get_rear(dq)`  
        덱의 뒤 (rear)에서 삭제하지 않고 뒤에 있는 요소를 반환한다.

---

## # `List(리스트)`

**`List(SLL, DLL)`**

- What is a List?

    A list is a collection of items where each item holds a relative position with respect to the others. This type of list is referred as unordered list.  

- `List Operations`

    1. `List()`  
        빈 리스트를 생성, 인수가 필요없다.  
        빈 리스트를 리턴한다.  
        Creates a new list that is empty.  
        No parameter is needed. Returns an empty list.

    2. `add(item)`  
        리스트에 새로운 아이템을 추가한다.  
        아이템을 인수로 넘겨주어야 하며, 아무것도 리턴하지 않는다.  
        Adds a new item to the list.  
        It needs the item and return nothing.

    3. `remove(item)`  
        리스트에 들어있는 원소를 제거한다.  
        이 과정에서 전달받은 인수가 리스트에 들어 있는지 traversal을 진행하여 확인하고,  
        인수를 값으로 갖는 리스트 노드를 찾게되면, 그 노드를 제거한다.  
        Removes the item to the list.  
        It need the item and modifies the list.  
        Assume the item is present in the list.

    4. `isEmpty()`  
        Tests to see whether the list is empty.  
        No parameter is needed. Returns a Boolean value.

    5. `length()`  
        Returns the number of the items in the list.  
        No parameter is needed. Returns an integer.

    6. `append(item)`  
        Adds a new item to the end of the list making it the last item in the collection.  
        It needs the item and returns nothing.  
        Assume the item is not already in the list.

    7. `index(item)`  
        Returns the position of item in the list.  
        It needs the item and returns the index.  
        Assume the item is in the list.

    8. `insert(pos, item)`  
        Adds a new item to the list at position pos.  
        It needs the item and returns nothing.  
        Assume the item is not already in the list and there are enough items to have position pos.

    9. `pop()`  
        Removes and returns the last item in the list.  
        It needs nothing and returns an item.  
        Assume the list has at least one item.

    10. `pop(pos)`  
        Removes and returns the item at position pos.  
        It needs the position and returns the item.  
        Assume the item is in the list.

- `(Singly) Linked List`

    SLL은 다음의 구성요소를 갖는다.  

    1. Data  
    2. A link to the next node (Pointer)

- `Add Method`

    `Add Method`는 `list`의 `head`를 바꿔주는 `node` 추가 작업이다.  
    이를 수행하기 위해서는 다음의 두 과정을 거쳐야 한다.

    1. `new node`의 `next node reference`가 기존 list의 head를 가리키게 한다.
    2. Add Method로 추가된 new node를 list의 head로 설정한다.

- `Length Method`

    `Length Method`는 `linked list traversal`을 이용한다. (리스트를 순차적으로 탐색하는 것)  
    리스트를 순차적으로 탐색하는 `current`라는 이름의 새로운 `reference(pointer)`를 생성한다.   

    current는 pointer 이므로 각각의 노드를 참조할 수 있으며, 초기 상태에 리스트의 첫 번째 노드, 즉, `head`를 가리킨다.  

    그 후,  current reference가 각각의 노드에 있는 next node pointer를 따라 이동하면서 리스트에 존재하는 노드의 개수를 센다. current reference가 마지막 노드에 다다르면, 그 노드는 next node pointer가 null을 가리키므로, 이를 기점으로 traversal을 종료한다. 이후, 카운트한 개수를 반환.

- `Search Method`

    `Search Method`도 `traversal`을 이용한다.  
    list에 있는 각각의 `node`를 참조할 수 있는 `current reference`를 생성한다.  

    초기 상태에 `current`가 리스트의 `head node`를 가리키도록 한다. 이후, traversal을 진행하면서 current node의` data`가 찾으려는 `value`와 일치하는지 확인한다. 만약 일치하지 않는다면 `current reference`가 `current node`의 `next node`를 가리키도록 만들면서 모든 node를 각각 탐색한다.  

    이 과정에서 원하는 `data`를 찾는다면 그 결과를 `return` 하고, 만약 `list`의 마지막 node까지 이동했는데도 원하는 `data`를 찾지 못했다면,  search를 종료한다.

- `Remove Method`

    `Remove Method`의 과정은 두 가지 step을 거친다.

    1. 지우고자 하는 value를 data로 갖는 node가 있는지 traversal을 통해 확인한다.
    2. node를 찾게 되면, 지우고자 할 노드를 가리키고 있는 `current reference`가 존재한다.  
        traversal을 하면서 current reference가 가리키는 node의 바로 전 node를 참조하면서 traversal을 같이 진행하게 되는 `previous reference`가 있는데, 이를 이용하여 `remove`  
        과정을 수행한다.

current reference가 지우고자 하는 node를 가리키고 있으면, previous reference는 해당 node의  
바로 이전 node를 가리키게 된다. 이 때, previous node가 next node로 current node의 next node를 참조하도록 만든 뒤, current node를 remove 하면 list의 구조를 유지하면서 원하는 node를  
제거할 수 있다.  

만약 지우고자 하는 current node가 `head`라면, 단순히 current node의 next node를 head로 바꾸어 준 뒤, current node를 제거한다. 이렇게 하면 list의 구조를 유지하면서 head node를 제거할 수 있다.

- `Disadvantage of SLL`

일방향적인 탐색(traversal)만 가능하다.  
→ 만약 가장 끝에 있는 node를 찾게 되는 경우라면, 탐색 시간이 길어진다.

- `Alternative of SLL : Doubly Linked List (DLL)`

    DLL은 각각의 node가 두 개의 pointer를 갖는다.  
    이전에 SLL은 next node를 가리키는 pointer만 존재했지만, DLL은 previous node를 가리키는 pointer도 갖는다.  

    Data  
    Next node  
    Previous node  

    또한, DLL에는 두 개의 끝 지점이 존재한다.

    head  
    tail

→ DLL에서 `head`의 `previous pointer`는 `null`이며, `tail`의 `next pointer`는 `null`이다.

- `Circular Doubly Linked List`

    head의 previous pointer는 tail을 가리키며, tail의 next pointer는 head를 가리키는 DLL

---

## # `Binary Search Tree(BST)`

**`BST`**

- `BST`란, 각각의 node가 key를 저장하는 `binary tree`이다.

    BST의 조건에 따라서 parent node의 key는 반드시 left child node의 key보다 크다. parent node보다 작은 key를 갖는 node를 left child, parent node의 value보다 큰 key를 갖는 node를 right child로 한다.

- `inorder traversal`

    BST에서 inorder traversal을 이용하면 각 node별 key의 increasing order를 반환받을 수 있음.

- `Insertion`

    만약 새롭게 들어오는 key의 값이 root node의 key보다 작다면, root node의 left side.  
    새롭게 들어오는 key의 값이 root node의 key 보다 크다면, root node의 right side.  

    insertion은 `recursive`로 이루어진다.

- `Searching`

    만약 찾고자 하는 key가 root보다 작다면, root의 left side에 존재한다.  
    그 반대라면, root의 right side에 존재한다.  
    만약 leaf node에 도달해도 key를 찾지 못한다면, key가 BST에 존재하지 않는 것이다.  

    searching 과정 또한 `recursive`이다.  

- `Deleting`

    1. `No Children`  
        만약, current node가 자식 노드가 없다면, parent node가 current node를 가리키는 reference를 제거하고 current node를 제거하면 된다.

        만약, 지우고자 하는 current node가 root node라면, root node = null로 만든 뒤,  
        current node를 제거한다.

    2. `One Child`  
        만약, current node가 자식 노드를 하나만 갖는다면, current node의 child node가 자신의 parent node를 대체하도록 위치를 바꿔주면 된다. (제거할 노드의 위치에 제거할 노드의 자식 노드를 갖다 놓는다.)

        만약, 지우고자 하는 current node가 root node라면, child node를 root로 만든 뒤,  
        current node를 제거한다.

    3. `Two Children`  
        만약, current node가 두 개의 자식 노드를 갖는다면, current node를 대체할 `Successor`가 필요하다. (후임, descendent)

        `Successor Node`는 current node의 key 다음으로 큰 값을 갖는 node로 상정한다.  
        즉, current node의 오른쪽 sub tree에서 가장 작은 값을 가져오면 된다.  
        (right sub tree에서 가장 left에 있는 node)

- `Performance`

    Insert, finding, removing all depend on the height of the BST  

    Best Case : O(log n)  
    Worst Case : O(n)
