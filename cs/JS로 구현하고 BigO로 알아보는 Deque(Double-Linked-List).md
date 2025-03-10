# JS로 구현하고 BigO로 알아보는 덱(Deque)

데이터 구조를 공부하다 보면 스택, 큐 다음으로 만나게 되는 것이 바로 덱(Deque)이다. 덱은 양쪽 끝에서 삽입과 삭제가 모두 가능한 자료 구조로, 스택과 큐의 특성을 모두 가지고 있어 유연하게 활용할 수 있다.

> (👨🏻‍🏫 : 저는 처음에 동기들끼리 덱 덱 거리길래, 'deck'인 줄 알고 카드 덱을 찾아봤죠... 😅 알고보니 Deque 였고, 내부 의미도 달랐어요. 그런데 생각보다 그 의미가 일맥상통 하다는 점…! Deque 는 줄임말인데, Double Ended Queue 랍니다.)

오늘은 JavaScript로 덱을 구현하고, 각 연산의 시간 복잡도(Big O)를 알아보며 실제 활용 사례까지 살펴보도록 하자.

## 💡급하신 분들을 위해서 결론 먼저!

1. 덱(Deque)은 **양쪽 끝에서 삽입과 삭제**가 모두 가능한 자료 구조다.
2. JavaScript에서 배열로 구현할 경우 `unshift()`의 시간 복잡도는 O(n)이므로 주의가 필요하다. (코테나 라이브러리를 제작 하시는 분이라면 더더욱!!)
3. 연결 리스트로 구현 시 모든 기본 연산(삽입, 삭제)의 시간 복잡도는 O(1)이다.
4. 덱은 작업 훔치기 알고리즘, 회문 검사, 슬라이딩 윈도우 등 다양한 알고리즘에 활용된다.

---

## 덱(Deque)이란?

덱(Deque)은 'Double-Ended Queue'의 줄임말로, 양쪽 끝에서 삽입과 삭제가 모두 가능한 자료 구조이다. 일반적인 큐(Queue)는 한쪽 끝(rear)에서만 삽입이 가능하고 다른 쪽 끝(front)에서만 삭제가 가능한 반면, 덱은 양쪽에서 모두 삽입과 삭제가 가능하다.

## 덱의 주요 연산

덱의 기본 연산은 다음과 같다:

- **addHead(value)**: 덱의 앞쪽에 요소 추가
- **addTail(value)**: 덱의 뒤쪽에 요소 추가
- **removeHead()**: 덱의 앞쪽에서 요소 제거 및 반환
- **removeTail()**: 덱의 뒤쪽에서 요소 제거 및 반환
- **peekHead()**: 덱의 앞쪽 요소 조회
- **peekTail()**: 덱의 뒤쪽 요소 조회
- **isEmpty()**: 덱이 비어있는지 확인
- **size()**: 덱의 크기 반환

> (👨🏻‍🏫 : 용어가 좀 헷갈리시죠? addHead는 스택의 push와 비슷하고, removeHead는 스택의 pop과 비슷하답니다. 반면 addTail는 큐의 enqueue와, removeTail는 큐의 dequeue와 유사하죠! 사실 이 일부 용어는 비슷하지만, 다르게 쓰인답니다. 제일 중요한 건 각 역할이 어떤 역할인지 파악하는 것.)

## JavaScript로 덱 구현하기

JavaScript에서 덱을 구현하는 방법은 크게 두 가지가 있다: **배열 기반 구현**과 **연결 리스트 기반 구현**. 각각의 방법에 따라 시간 복잡도가 달라지므로 상황에 맞게 선택하는 것이 중요하다.

## 배열 기반 구현

가장 간단한 방법은 JavaScript의 기본 배열을 활용하는 것이다:

```jsx
class Deque {
  constructor() {
    this.items = [];
  }

  addHead(element) {
    this.items.unshift(element);
  }

  addTail(element) {
    this.items.push(element);
  }

  removeHead() {
    if (this.isEmpty()) {
      return "Underflow";
    }
    return this.items.shift();
  }

  removTail() {
    if (this.isEmpty()) {
      return "Underflow";
    }
    return this.items.pop();
  }

  peekHead() {
    if (this.isEmpty()) {
      return "No elements in the Deque";
    }
    return this.items[0];
  }

  peekTail() {
    if (this.isEmpty()) {
      return "No elements in the Deque";
    }
    return this.items[this.items.length - 1];
  }

  isEmpty() {
    return this.items.length === 0;
  }

  size() {
    return this.items.length;
  }
}
```

이 구현은 간단하지만, `unshift()`와 `shift()` 메서드의 시간 복잡도가 O(n)이라는 단점이 있다. 왜냐하면 배열의 앞쪽에 요소를 추가하거나 제거할 때 모든 요소를 한 칸씩 이동시켜야 하기 때문이다. 배열로 구현을 한다면, **index 가 생김으로써 편안하지만, 동시에 신경써야하는 제약이 생김에 따라 시간 복잡도가 높아지는 것이다.**

## 연결 리스트 기반 구현

더 효율적인 방법은 이중 연결 리스트(Doubly Linked List)를 사용하는 것이다:

```jsx
class Node {
  constructor(data) {
    this.data = data;
    this.next = null;
    this.prev = null;
  }
}

class Deque {
  constructor() {
    this.count = 0;
    this.head = null;
    this.tail = null;
  }

  addHead(value) {
    const node = new Node(value);
    if (!this.head) {
      this.head = node;
      this.tail = node;
    } else {
      const next = this.head; //next라는 값에 head의 값을 넣는다.
      this.head = node; // head에다가 node의 값을 넣는다.
      this.head.next = next; //head의 next에 head의 값을 넣는다.
      next.prev = node; // 기존의 head 였던 것과, 지금의 head를 연결한다.
    }
    return ++this.count;
  }

  addTail(value) {
    const node = new Node(value);
    if (!this.head) {
      this.head = node;
      this.tail = node;
    } else {
      node.prev = this.tail;
      this.tail.next = node;
      this.tail = node;
    }
    return ++this.count;
  }

  removeHead() {
    if (!this.head) {
      return false;
    }
    const data = this.head.data;
    if (this.count === 1) {
      this.head = null;
      this.tail = null;
    } else {
      this.head = this.head.next;
      this.head.prev = null;
    }
    this.count--;
    return data;
  }

  removeTail() {
    if (!this.head) {
      return false;
    }
    const data = this.tail.data;
    if (this.count === 1) {
      this.head = null;
      this.tail = null;
    } else {
      this.tail = this.tail.prev;
      this.tail.next = null;
    }
    this.count--;
    return data;
  }

  peekHead() {
    return this.head && this.head.data;
  }

  peekTail() {
    return this.tail && this.tail.data;
  }

  isEmpty() {
    return this.count === 0;
  }

  size() {
    return this.count;
  }
}
```

출처: [자료구조 Deque 구현 (자바스크립트, JavaScript)](https://velog.io/@young_pallete/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-Deque-%EA%B5%AC%ED%98%84-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-JavaScript)

이중 연결 리스트를 사용하면 모든 기본 연산(삽입, 삭제)의 시간 복잡도가 O(1)이 된다. 이는 배열 기반 구현보다 훨씬 효율적이다.

## 덱의 시간 복잡도 분석

덱의 시간 복잡도는 구현 방식에 따라 달라진다. 각 구현 방식별 시간 복잡도를 비교해보자:

## 배열 기반 구현의 시간 복잡도

| 연산               | 시간 복잡도 |
| ------------------ | ----------- |
| addTail (push)     | O(1)        |
| removeTail (pop)   | O(1)        |
| addHead (unshift)  | O(n)        |
| removeHead (shift) | O(n)        |
| peekFront          | O(1)        |
| peekRear           | O(1)        |
| isEmpty            | O(1)        |
| size               | O(1)        |

배열 기반 구현에서는 `addHead`와 `removeHead` 연산이 O(n)의 시간 복잡도를 가진다. 이는 배열의 앞쪽에 요소를 추가하거나 제거할 때 모든 요소를 이동시켜야 하기 때문이다.

## 연결 리스트 기반 구현의 시간 복잡도

| 연산       | 시간 복잡도 |
| ---------- | ----------- |
| addHead    | O(1)        |
| addTail    | O(1)        |
| removeHead | O(1)        |
| removeTail | O(1)        |
| peekFront  | O(1)        |
| peekRear   | O(1)        |
| isEmpty    | O(1)        |
| size       | O(1)        |

연결 리스트 기반 구현에서는 모든 기본 연산이 O(1)의 시간 복잡도를 가진다. 이는 연결 리스트에서 노드를 추가하거나 제거할 때 포인터만 변경하면 되기 때문이다.

> (👨🏻‍🏫 : 이 차이가 엄청나게 중요하답니다! 데이터가 많을수록 O(n)과 O(1)의 차이는 하늘과 땅 차이가 되죠. 10만 개의 데이터를 다룬다고 생각해보세요!)

## 성능 비교: 배열 vs 연결 리스트

실제로 배열과 연결 리스트 기반 구현의 성능 차이를 확인해보자:

```jsx
const loop = 100000;

function arrayUnshiftTest() {
  const q = [];
  for (let i = 0; i <= loop; i++) {
    q.unshift(i);
  }

  for (let i = 0; i <= loop; i++) {
    q.shift();
  }
}

function dequeUnshiftTest() {
  const deque = new Deque();
  for (let i = 0; i <= loop; i++) {
    deque.addHead(i);
  }

  for (let i = 0; i <= loop; i++) {
    deque.removeHead(i);
  }
}

console.time("Array Unshift Time Measure");
arrayUnshiftTest();
console.timeEnd("Array Unshift Time Measure");

console.time("Deque Unshift Time Measure");
dequeUnshiftTest();
console.timeEnd("Deque Unshift Time Measure");
```

출처: [Array vs Queue vs Deque](https://nyol.tistory.com/190)

이 테스트 결과, 10만 개의 데이터를 처리할 때 배열 기반 구현은 연결 리스트 기반 구현보다 약 200배 느린 것으로 나타났다. 이는 이론적인 시간 복잡도 분석과 일치하는 결과이다.

## 덱의 활용 사례

덱은 다양한 알고리즘과 문제 해결에 활용된다:

### 1. 작업 훔치기 알고리즘(Work Stealing Algorithm)

여러 프로세서의 작업 스케줄링을 구현할 때 덱을 사용한다. 각 프로세서는 자신의 덱에서 작업을 가져오고, 자신의 덱이 비어있으면 다른 프로세서의 덱에서 작업을 "훔쳐온다".

### 2. 회문(Palindrome) 검사

문자열이 회문인지 검사할 때 덱을 활용할 수 있다. 문자열의 앞과 뒤에서 동시에 문자를 제거하며 비교하는 방식으로 구현한다.

### 3. 슬라이딩 윈도우 알고리즘

고정 크기의 윈도우를 유지하면서 배열이나 리스트를 순회할 때 덱을 사용하면 효율적이다.

### 4. 브라우저 히스토리

웹 브라우저의 뒤로 가기/앞으로 가기 기능을 구현할 때 덱을 활용할 수 있다.

## 결론

덱(Deque)은 양쪽 끝에서 삽입과 삭제가 가능한 유연한 자료 구조로, 스택과 큐의 특성을 모두 가지고 있다. JavaScript에서 덱을 구현할 때는 배열 기반 구현보다 연결 리스트 기반 구현이 더 효율적이다. 특히 대량의 데이터를 처리할 때 그 차이가 두드러진다.

**핵심 포인트**:

- FIFO(First In First Out) 자료구조를 활용하기 위해서는 배열 대신 큐를 활용하자.
- 앞, 뒤로 데이터가 추가되고 삭제되는 자료구조의 경우에는 큐 대신 덱을 활용하자.
- 덱을 구현할 때는 연결 리스트 기반 구현이 모든 연산에서 O(1)의 시간 복잡도를 보장한다.

> 👨🏻‍🏫 : 물론 npm에는 이미 구현된 덱 라이브러리가 있답니다! 실무에서는 `@datastructures-js/deque`와 같은 라이브러리를 사용하는 것도 좋은 방법이죠. 그러나 코테, CS 등 기초에 대해서 공부를 해야한다면 부딪혀보는 게 답인 거 같아요..!

🙇🏻 **글 내에 틀린 점, 오탈자, 비판, 공감 등 모두 적어주셔도 됩니다. 감사합니다..!** 🙇🏻
