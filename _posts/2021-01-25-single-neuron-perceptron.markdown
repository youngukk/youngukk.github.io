---
layout: post
read_time: true
show_date: true
title: 연결리스트 C언어로 표현하기
date: '2021-08-09 13:32:20 -0600'
description: 연결리스트 C언어로 표현하기.
img: posts/20210125/Perceptron.jpg
tags:
  - coding
author: Younguk Kim
github: null
mathjax: 'yes'
published: true
---
자료구조나 알고리즘은 정보처리기사나 전공과목으로 기초 이론은 공부 했었지만 C언어로 직접 구현하는것은 정말 어려웠어요. 진짜 어떤식으로 이 자료구조나 알고리즘이 사용되는지는 알았지만 직접 사용하고 구현하는거는 다른 문제더라고요

저는 자료구조를 구현하면서 포인터 구조체 등등 기초문법을 대충 이해하고 넘어간것을 후회했고 실제로 활용하는 부분에서 이해가 안돼 막히는 부분들이 너무 많아서 시간을 많이 사용했습니다.

그러니 자료구조나 알고리즘을 공부하려는 분들은 다시한번 기본문법을 복습하고 오면 더 좋은 결과를 얻을 수 있을거에요. 저같은 실수 하시지 않기를...

연결 리스트(Linked List)는 리스트를 구현하는 여러가지 기법 중에서도 간단한 방법으로 사용되는 자료구조 이다. 

리스트 내의 각 요소는 노드(Node)라고 부른다. 연결 리스트는 '노드를 연결해서 만드는 리스트'라고 해서 붙여진 이름이다. 

<img src="./assets/img/posts/20210125/2021_08_11_Node.png" alt=""/>

위 그림처럼 데어터와 포인터로 이루어진 노드들을 엮으면 링크드 리스트가 되는 것이다

리스트의 첫 번째 노드를 헤드(Head)라 부르며 마지막 노드를 테일(Tail)이라고 부른다.

연결 리스트는 사이에 새로운 노드를 끼워 넣거나 제거하는 것도 아주 쉽다. 해당 노드를 가리키는 포인터만 교환하면 되기 때문이다.

연결리스트의 간단한 설명은 이 정도로 하고 코드로 구현하면서 좀 더 자세히 알아보자.

### C언어로 표현하는 링크드 리스트의 노드

먼저 연결 리스트의 기본 연산을 하기전에 노드를 표현해야 하는데 연결 리스트의 노드는 C언어로 표현하면 다음과 같은 구조체로 표현할수 있다.

```c
typedef struct tagNode{
	int Data;              // 데이터 필드
	struct Node* NextNode; // 다음 노드를 가리키는 포인터
} Node;
```

### 연결 리스트의 주요 연산

기본적으로 연결 리스트를 구축하고 연결 리스트에 있는 자료를 사용하기 위해 필요한 연산은 다섯 가지로 표현할수 있다.

- 노드 생성/소멸
- 노드 추가
- 노드 탐색
- 노드 삭제
- 노드 삽입

### 노드 생성/소멸

 C언어는 3가지 메모리 영역이 있다. 전역 메모리(Static memory), 자동 메모리(Automatic Memory), 자유 저장소(Free Store) 그중 전역 메모리 할당은 일반적으로 관련 프로그램을 실행하기 앞서 컴파일 시간에 메모리를 할당하는 것이다.  

자동 메모리는 스택 구조로 이루어져 코드블럭이 끝나면 블럭안에 있는 모든 메모리가 삭제된다. 즉 자동 메모리는  자료형에 맞게 자동으로 메모리 크기를 부여하고 그 블럭이 끝나면 자동으로 삭제시키는 것이다.  그러므로 우리가 사용해야할 연결리스트 하고는 어울리지 않다는것을 알 수 있다.

그렇다면 자동 메모리에 노드를 생성한다면 어떻게 함수로 구현할수있는지 알아보자

일단 자유 저장소에 메모리를 할당하려면 malloc( ) 함수가 필요하다.

malloc( )함수를 사용하여 노드를 자유 저장소에 생성하는 예제는 다음과 같다.

```c
//노드 생성
Node* SLL_CreateNode(ElementType NewData)
{
	Node* NewNode = (Node*)malloc(sizeof(Node)); // 자유 저상소 생성

	NewNode->Data = NewData;  // 데이터 저장
	NewNode->NextNode = NULL; // 다음 노드에 대한 포인터는 NULL 로 초기화

	return NewNode;           //노드의 주소 반환
}
```

노드를 소멸시키는 방법은 free( )함수를 사용하면 된다.

```c
//코드 소멸
void SLL_DestroyNode(Node* Node){
	free(Node);
}
```

### 노드 추가

노드 추가 연산은 연결 리스트의 테일 노드 뒤에 새로운 노드를 만들어 연결하는 것을 말한다.

SLL_CreateNode( ) 함수를 이용하여 자유 저장소에 노드를 생성 후 새로 생성한 노드의 주소를 테일의 NextNode 포인터에 대입하면 된다. 노드 추가 연산을 수행하는  SLL_AppendNode( ) 함수는 다음과 같이 구현하고 사용할수 있다..

```c
// 노드 추가
void SLL_AppendNode (Node** Head, Node* NewNode)
{
	// 헤드 노드가 NULL이라면 새로운 노드가 Head
	if ((*Head) == NULL){
		*Head = NewNode;
	}
	else{
	// 테일을 찾아 NewNode를 연결한다.
		Node* Tail = (*Head);
		while (Tail->NextNode != NULL){
			Tail = Tail->NextNode;
		}
		Tail->NextNode = NewNode;
	}
}

int main()
{
	Node* list = NULL;
	Node* NewNode = NULL;
	NewNode = SLL_CreateNode(117);
	SLL_AppendNode(&list, NewNode);
}
```

여기서 void SLL_AppendNode (Node** Head, Node* NewNode) 에서 파라미터로 Node* Head로 받는것이 아닌 Node** Head로 받는다는 것인데 왜 그런 것 인지 알아보자 ( 여기서 나는 멘탈이 나갔다 )

Node* Head로 받는 경우를 보자

```c
void SLL_AppendNode (Node* Head, Node* NewNode)
{
	if ((Head) == NULL) {
		Head = NewNode;
	}
	...
} /* *** 이 함수가 끝나면 Head의 값은 제거된다*** */

```

void SLL_AppendNode (Node* Head, Node* NewNode)를 선언 한다는 것은 SLL_AppendNode의 매개 변수(Node* Head, Node* NewNode)가 각각 자동 메모리가 생성되고 

이렇게 사용할 경우 우리가 원하는 결과는 list 주소에 NewNode의 주소값을 대입하는 것인데 밑에 결과값을 본다면 여전히 list에는 NULL값이 들어가 있는것을 확인할수 있다

<img src="./assets/img/posts/20210125/2021_08_11_Head1.png" alt=""/>


반면  Node** Head로 받는경우에는 SLL_AppendNode( ) 함수를 다음과 같이 사용할수 있다

```c
	Node* list = NULL;
	Node* NewNode = NULL;
	NewNode = SLL_CreateNode(117);
	SLL_AppendNode(&list, NewNode);
```

위 코드의 SLL_AppendNode( ) 함수를 사용한다면 우리가 원했던 list 주소에 NewNode의 주소값을 대입하여 같은 값인것을 확인할 수 있을것이다.

<img src="./assets/img/posts/20210125/2021_08_11_Head2.png" alt=""/>

### 노드 탐색

연결 리스트에서 원하는 노드를 탐색하기 위해서는 헤드부터 시작하여 테일까지 하나하나 찾아가 원하는 노드를 접근할수 있다. 이는 시간이 많이 소요될 수 있으며 연결 리스트가 갖는 단점중에 하나이다

연결 리스트를 반환하는 함수 SLL_GetNodeAt( )함수를 구현해 보겠다.

```c
// 노드 탐색
Node* SLL_GetNodeAt(Node* Head, int Location)
{
    Node* Current = Head; // 노드의 시작부분

    while (Current != NULL && (--Location) >= 0) // 원하는 Location이 될때까지 카운트
        Current = Current->NextNode;

    return Current;
}
```

### 노드 삭제

노드 삭제는 연결 리스트안에 있는 임의의 노드를 제거하는 연산으로 제거하고자 하는 노드를  찾은 후 해당 노드의 다음 노드와 이전 노드의 NextNode 포인터에 연결하면 된다.

삭제 연산을 수행하는 SLL_RemoceNode( ) 함수이다

```c
/* 노드 제거 */
void SLL_RemoveNode(Node** Head, Node* Remove)
{
    if (*Head == Remove) //헤드가 삭제하려고 하는 노드와 같다면
        *Head = Remove->NextNode;

    else
    {
        Node* Current = *Head; 
				/* 삭제하고 싶은 노드까지 검색 */
        while (Current != NULL && Current->NextNode != Remove)
            Current = Current->NextNode;
        if (Current != NULL) // 제거
            Current->NextNode = Remove->NextNode;
    }
}
```

 

### 노드  삽입

노드 삽입은 노드와 노드 사이에 새로운 노드를 넣는 연산이다.

임임의 노드를 삽입을 하려고 한다면 3가지 경우가 있을것이다.

1. 노드와 노드사이 중간에 삽입하는 경우
2. 노드가 없어 헤드로 삽입하는경우
3. 마지막 꼬리부분 다음에 삽입하는 경우

3가지 경우를 하나하나 살펴보자

먼저 노드와 노드 사이에 삽입하는 경우로 원하는 노드 뒤를 삽입하는 함수를 구현해보자
함수는 SLL_InsertAfter( ) 이다.

```c
void SLL_InsertAfter(Node* Current, Node* NewNode){
    NewNode->NextNode = Current->NextNode;
    Current->NextNode = NewNode;
}
```

<img src="./assets/img/posts/20210125/2021_08_11_InsertAfter.png" alt=""/>

그림으로 설명하면 이런식으로 표현할수 있다.

또한 원하는 노드 앞도 삽입할수 있을것이다 이 경우를 함수로 구현한다면 다음과 같다

함수는 SLL_InsertBefore( ) 이다.

```c
void SLL_InsertBefore(Node** Head, Node* Current, Node* NewHead)
{
		/* 삽입할 위치가 헤드부분이라면 */
    if ((*Head) == Current)
        SLL_InsertNewHead(Head, NewHead)

    else {
        Node* BeforeNode = (*Head);
				/* 원하는 다음 부분의 노드를 구해 
						SLL_InsertAfter를 사용한다 */
        while (BeforeNode->NextNode != Current) 
            BeforeNode = BeforeNode->NextNode;
        SLL_InsertAfter(BeforeNode, NewHead);
    }
}
```

이제 헤드 앞으로 추가하고 싶을때 하는 연산이다

연산함수는 SLL_InsertNewHead( ) 이다

```c
void SLL_InsertNewHead(Node** Head, Node* NewHead)
{
		/* 헤드가 없다면 추가한 노드를 헤드로 한다*/
    if (*Head == NULL)
        (*Head) = NewHead;

    else {
		/* 새로 추가한 NewHead의 다음 주소를 Head로 연결한다 */
        NewHead->NextNode = (*Head);
        (*Head) = NewHead;
    }
}
```

꼬리다음 부분을 추가하는 방법은 새로운 노드를 추가하고 SLL_AppendNode( )로 이어주면 된다.

### 모든 노드 제거

한번에 모든 노드를 제거하는 연산이다.

연산함수는 SLL_DestroyAllNodes( ) 이다

```c
void SLL_DestroyAllNodes(Node** List)
{
    Node* Remove = *List;
    while (Remove != NULL) {
        *List = NULL;
        Remove = Remove->NextNode;
        *List = Remove;
    }
}// Remove는 자동메모리이므로 함수를 종료하면 자동으로 제거된다
```

이제 예제코드를 보면서 테스트를 해보자

```c
int main(void)
{
    int i = 0;
    int count = 0;
    Node* List = NULL;
    Node* Current = NULL;
    Node* NewNode = NULL;

    // 노드 5개 추가
    for (i = 0; i < 5; i++) {
        NewNode = SLL_CreateNode(i);
        SLL_AppendNode(&List, NewNode);
    }

    NewNode = SLL_CreateNode(-1);
    SLL_InsertNewHead(&List, NewNode);

    NewNode = SLL_CreateNode(-2);
    SLL_InsertNewHead(&List, NewNode);

    /* 리스트 출력 */
    count = SLL_GetNodeCount(List);
    for (i = 0; i < count; i++) {
        Current = SLL_GetNodeAt(List, i);
        printf("List [%d] : %d\n", i, Current->Data);
    }

    /* 리스트의 첫번째 노드 앞에 새 노드 삽입 */
    printf("\nInserting 3000 After [0] ... \n\n");

    Current = SLL_GetNodeAt(List, 0);
    NewNode = SLL_CreateNode(1111);

    SLL_InsertAfter(Current, NewNode);

    /* 리스트 출력 */
    count = SLL_GetNodeCount(List);
    for (i = 0; i < count; i++) {
        Current = SLL_GetNodeAt(List, i);
        printf("List [%d] : %d\n", i, Current->Data);
    }

    printf("\n\n");

    /* 리스트의 다섯번째 노드 앞에 새 노드 삽입 */
    printf("\nInserting 5555 Before [4] ... \n\n");

    Current = SLL_GetNodeAt(List, 4);
    NewNode = SLL_CreateNode(5555);

    SLL_InsertBefore(&List, Current, NewNode);

    /* 리스트 출력 */
    count = SLL_GetNodeCount(List);
    for (i = 0; i < count; i++) {
        Current = SLL_GetNodeAt(List, i);
        printf("List [%d] : %d\n", i, Current->Data);
    }

    /* 모든 노드를 메모리에서 제거 */
    printf("\nDestroying List...\n\n");

    SLL_DestroyAllNodes(&List);
    void SLL_DestroyNode(Node * Node);

    /* 리스트 출력 */
    count = SLL_GetNodeCount(List);
    for (i = 0; i < count; i++) {
        Current = SLL_GetNodeAt(List, i);
        printf("List [%d] : %d\n", i, Current->Data);
    }
    return 0;
}
```
