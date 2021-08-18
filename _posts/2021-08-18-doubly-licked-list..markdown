---
layout: post
read_time: true
show_date: true
title: 이중 연결 리스트(c언어)
date: '2021-08-18 21:55:40 -0600'
description: 이중 연결 리스트(c언어)
img: posts/20210818/arnold-francisca-f77Bh3inUpE-unsplash.jpg
tags:
  - [coding, 자료구조, c언어]
author: Younguk Kim
github: null
mathjax: 'yes'
toc: yes
---
이중 연결 리스트(Doubly Linked List)는 연결 리스트의 탐색 기능을 개선한 자료구조로 연결리스트는 헤드에서 테일 방향으로만 탐색을 할 수 있는데 이중 연결 리스트는 양방향으로 탐색이 가능하게 구현한 자료구조이다.

이중 연결 리스트는 단순 연결 리스트와 다르게 앞과 뒤에 연결한 노트의 포인터를 가지고 있어서 안과 뒤로 이동할수 있다

<img src="./assets/img/posts/20210125/D2004_i1.jpg" alt=""/>
이것을 C언어 구조체롤 선언한다면 다음과  같이 선언할 수 있다.

```c
tyedef struct tagNode
{
	int Data;
  struct tagNode* preNode; // 이전 노드를 가리키는 포인터
	struct tadnode* nextNode; // 다음 노드를 가리키는 포인터
}
```

### 이중 연결 리스트의 주요 연산

이중 연결 리스트이라 해봐야 단순 연결리스트에 이전 노드를 가리키는 포인터를 추가할 뿐이니 전에 구현 해놨던 단순 연결 리스트에 이전 노드를 가리키는 포인터를 추가하면 된다.

### 노드의 생성과 소멸

전에 포스트 구현 했던 SLL_CreateNode()함수를 빌려와 전 노드를 가리키는 포인터를 추가해 DLL_CreateNode()함수를 구현해보겠다.

```c
//노드 생성
Node* DLL_CreateNode(int)
{
	Node* NewNode = (Node*)malloc(sizeof(Node)); // 자유 저상소 생성

	NewNode->Data = NewData;  // 데이터 저장
	NewNode->NextNode = NULL; // 다음 노드에 대한 포인터는 NULL 로 초기화
	NewNode->preNode = NULL;  // 전 노드에 대한 포인터도  NULL로 초기화
	return NewNode;           //노드의 주소 반환
}
```

 소멸하는 것은 연결리스트와 동일 합니다

```c
//코드 소멸
void SLL_DestroyNode(Node* Node){
	free(Node);
}
```

### 노드 추가

단순 연결 리스트는 새로 추가된 테일을 가리키도록 하면 끝나지만 이중 연결 리스트는 추가된 노드의 PreNode가  기존 테일의 주소값을 가지고 있어야 한다.

```c
// 노드 추가
void DLL_AppendNode (Node** Head, Node* NewNode)
{
	// 헤드 노드가 NULL이라면 새로운 노드가 Head
	if ((*Head) == NULL){
		*Head = NewNode;
	}
	else{
	// 테일을 찾아 NewNode를 연결한다.
		Node* Tail = (*Head);
		while (Tail->NextNode != NULL){    //테일의 값을 찾는다
			Tail = Tail->NextNode;
		}
		Tail->NextNode = NewNode; //기존 테일의 NextNode에 테일뒤에 새로 추가된 노드를 가리킨다
		NewNode->PreNode = Tail;  //새로 추가된 노드의 PreNode가 기존 테일노드를 가리킨다
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

노드 탐색

헤드부터 테일로 탐색하는 함수는 단순 연결 리스트와 다르지않고 테일에서 헤드로 탐색하는 함수를 구현해 보겠다

```c
// 노드 탐색
Node* DLL_GetNodeAt(Node* Head, int Location)
{
    Node* Current = Head; // 노드의 시작부분
		Node* Tail = (*Head);
		while (Tail->NextNode != NULL){    //테일의 값을 찾는다
			Tail = Tail->NextNode;
		}
    while (Current != NULL && (--Location) >= 0) // 원하는 Location이 될때까지 카운트
        Current = Current->PreNode;              // 테일에서부터 헤드로 역방향으로 노드를 탐색한다

    return Current;
}
```

노드 삭제

우리가 처리해야 할 포인터는 4개가 있다 삭제할 노드의 양쪽 포인터, 삭제 할 노드의 이전 노드의 NextNode의 포인터 삭제할 노드의 다음 노드의 PreNode의 포인터 이다

그리고 삭제할 노드의 포인터들은 NULL로 초기화후 삭제한다

```c
/* 노드 제거 */
void DLL_RemoveNode(Node** Head, Node* Remove)
{
    if (*Head == Remove) //헤드가 삭제하려고 하는 노드와 같다면
        *Head = PreNode->NULL;
				
				// 삭제할 노드의 포인터들은 NULL로 초기화
				Remove->PreNode = NULL;
				Remove->NextNode = NULL;

    else
    {
        Node* Temp= *Remove;

				Remove->PreNode->NextNode = Temp->nextNode;
        if (Remove->Next != NULL)
            Remove->PreNode->NextNode = Temp->nextNode;

				// 삭제할 노드의 포인터들은 NULL로 초기화
				Remove->PreNode = NULL;
				Remove->NextNode = NULL;
    }
}
```