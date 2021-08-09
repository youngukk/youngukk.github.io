---
layout: post
read_time: true
show_date: true
title: 연결리스트 C언어로 표현하기
date: '2021-08-09 13:32:20 -0600'
description: 연결리스트 C언어로 표현하기.
img: posts/20210125/Perceptron.jpg
tags:
  - machine learning
  - coding
  - neural networks
author: Armando Maynez
github: amaynez/Perceptron/
mathjax: 'yes'
published: true
---
# 연결(LInked) 리스트

링크드 리스트(Linked List)는 리스트를 구현하는 여러가지 기법 중에서도 간단한 방법으로 사용되는 자료구조 이다. 

리스트 내의 각 요소는 노드(Node)라고 부른다. 링크드 리스트는 '노드를 연결해서 만드는 리스트'라고 해서 붙여진 이름이다. 

리스트의 첫 번째 노드를 헤드(Head)라 부르며 마지막 노드를 테일(Tail)이라고 부른다.

연결 리스트는 사이에 새로운 노드를 끼워 넣거나 제거하는 것도 아주 쉽다. 해당 노드를 가리키는 포인터만 교환하면 되기 때문이다.

연결리스트의 간단한 설명은 이 정도로 하고 본격적으로  C언어로 구현하면서 차근차근 이야기하겠다.

## C언어로 표현하는 링크드 리스트의 노드

먼저 연결 리스트의 기본 연산을 하기전에 노드를 표현해야 하는데 연결 리스트의 노드는 C언어로 표현하면 다음과 같은 구조체로 표현할수 있다.

```c
typedef struct tagNode{
	int Data;              // 데이터 필드
	struct Node* NextNode; // 다음 노드를 가리키는 포인터
} Node;
```

## 연결 리스트의 주요 연산

기본적으로 연결 리스트를 구축하고 연결 리스트에 있는 자료를 사용하기 위해 필요한 연산은 다섯 가지로 표현할수 있다.

- 노드 생성/소멸
- 노드 추가
- 노드 탐색
- 노드 삭제
- 노드 삽입

노드의 생성/소멸, 추가, 삭제, 삽입은 연결 리스트 자료구조를 구축하기 위한 연산이고, 리스트 탐색은 구축되어 있는 연결 리스트의 데이터를 활용하기 위한 연산이다.

## 노드 생성/소멸

C언어로 작성된 프로그램은 세 가지 종류의 메모리 영역을 가진다. 

하나는 전역변수나 정적 변수 등이 저장되는 정적 메모리(Static Memoty), 또 다른 하나는 지역 변수가 저장되는 자동 메모리 (Automatic Memory), 그리고 마지막 하나는 자유 저장소(Free Store) 이다.

정적 메모리는 프로그램이 실행하면서 프로그램에서 사용될 전역 변수/정적 변수를 메모리에 할당한 후 프로그램이 종료될 때 해제하는 영역이기 때문에, 일단 머릿속 한쪽 켠에 잘 보관해 두기만 하면 된다.

자동 메모리는 스택 구조로 이루어져 있어 이곳에 저장된 변수는 코드 블록 ('{'와'}'의 괄호로 이루어진 블록) 이 종료된에 따라 4차원의 세계로 사라진다. 예를들면 다음과 같이 코드 블록안에서 선언된 변수들은 선언 당시에 자동 메모리에 저장되었다가 코드 블록의 끝에서 모두 제거 된다.

 

```c
{ // 코드블록 시작
	int a = 37;
	Node MyNode; // Node는 위 struct 로 정의해 두었다
} // 코드 블록 끝. 여기에서 a와  MyNode는 자동 메모리에서 제거
```

그리고 이것은 함수에도 동일하게 적용된다

```c
int Plus(int a, int b)  //a와 b도 자동 메모리에 저장
{
	int c = a + b;        //c도 물론 자동 메모리에 저장
	return c;
} // a, b, c모두 자동ㅇ 메모리에서 제거
```

이처럼 자동 메모리는 코드 블록이 끝나는 곳에서 자동으로 메모리를 해체하기 때문에 자동 메모라라고 붙여진 것이다.

자유 저장소는 자동 메모리와는 달리 프로그래머가 직접 메모리를 관리하는 메모리 영역이다. 자유 저장소의 '자유'는 자동 메모리 영역에서 코드 블록이라는 한계로부터의 해방을 의미한다. 그러나 이것은 책임이 따르는 일이고 프로그래머는 자유롭게 메모리를 할당해서 사용할수 있지만 그 메모리를 안전하게 해체하는 것도 프로그래머가 책임져야 하는 것이다.

노드의 생성과 소멸에 어울리는 곳은 어디일까 자동 메모리와 자유 저장소가 있다

먼저 자동 메모리에 노드를 생성한다면 다음과 같은 함수로 표현할수 있을것이다.

```c
// 노드 생성(자동메모리의 문제버전)
Node* SLL_CreateNode(int NewData)
{
	node NewNode; // 자동 메모리에 새로운 노드 생성
	NewNode.Data = NewData;
  NewNode.NextNode = NULL;

	return &NewNode; // NewNode가 생성된 메모리의 주소를 반환
} // 함수가 종료되면서 NewNode는 자동 메모리에서 제거된다
...
Node* MyNode = SLL_CreateNode(117); // MyNode는 할당되지 않은 메모리를 가리킨다
```

즉 SLL_CreateNode( ) 함수 안에서 생성된 NewNode는 함수가 종료하면서 메모리에서 제거된다. 하지만 SLL_CreateNode( ) 함수는 NewNode가 존재했던 메모리의 주소를 반환단다. 이것은 MyNode 포인터는 상관없는 메모리를 가리키고 문제를 발생 시킨다.

자동 메모리를 쓸 수 없다는 것을 알았으니 자유 저장소를 알아보자

자유 저장소에 메모리를 할당하려면 malloc( ) 함수가 필요하다

```c
void* malloc( size_t size);
```

malloc( ) 함수의 반환형인 void*는 만능 포인터라고도 불린다. 어떤 형이라도 가리킬 수 있어 자유 저장소에 메모리 주소를 어떤 형의 포인터로도 가리킬 수 있다는 것을 의미한다. 그리고 molloc( )함수의 유일한 매개 변수의 자료형이자 sizeof 연산자의 반환형이기도 한 size_t는 typedef문을 이용하여 uinsigned int 의 별칭으로 선언되어 잇다. 간단히 말해 size_t 는 곧 unsigned int형이다.

malloc( )함수를 사용하여 노드를 자유 저장소에 생성하는 예제는 다음과 같다.

```c
//노드 생성
Node* SLL_CreateNode(ElementType NewData)
{
	Node* NewNode = (Node*)malloc(sizeof(Node));

	NewNode->Data = NewData;  // 데이터 저장
	NewNode->NextNode = NULL; // 다음 노드에 대한 포인터는 NULL 로 초기화

	return NewNode;           //노드의 주소 반환
}
```

노드를 소멸시키는 방법은 free( )함수를 사용하면 된다.

```c
//코드 소멸
void sLL_DestroyNode(Node* Node){
	free(Node);
}
```