## JS로 구현하고 BigO로 알아보는 큐(Queue)

오늘은 **큐(Queue)** 자료구조에 대해 알아본다. 큐는 우리 일상에서 흔히 볼 수 있는 줄서기와 같다. 먼저 온 사람이 먼저 서비스를 받는 것처럼, 큐도 **FIFO(First In First Out)** 원칙을 따른다.

(👨🏻‍🏫 : 롤 하시는 분들이라면 꼭 들어보셨을 “야 큐 돌린다?” 라는 문장에서의 ‘큐’가 이 Queue 였다는 사실! 가장 먼저 돌린 사람이, 가장 먼저 잡히겠죠? 이를 통해서, 예상 매칭 시간 또한 계산하는 것이랍니다.)

자바스크립트로 큐를 구현하는 방법은 여러 가지가 있지만, 오늘은 가장 기본적인 방법부터 시작해 성능 최적화된 방법까지 단계별로 살펴보고, 그리고 각 구현 방식의 시간 복잡도(Big O)를 비교해 어떤 상황에서 어떤 구현 방식이 적합한지 알아보자.

## 💡급하신 분들을 위해서 결론 먼저!

1. **배열로 구현한 큐**는 간단하지만 `shift()` 연산의 O(n) 시간 복잡도로 인해 대규모 데이터에 비효율적이다.
2. **객체를 사용한 큐** 구현은 모든 연산이 O(1)로 매우 효율적이다.
3. **연결 리스트 기반 큐**는 메모리 효율성이 좋고 모든 연산이 O(1)이다.
4. **대규모 데이터 처리 시 객체 기반 구현**이 배열 기반보다 최대 100배 빠르다.
5. 실무에서는 `denque`, `bull`, `bee-queue` 같은 검증된 라이브러리 사용을 권장한다.

## 1. 가장 쉬운 Queue 구현법

자바스크립트에서 큐를 구현하는 가장 간단한 방법은 배열을 사용하는 것이다. 배열의 내장 메서드를 활용하면 몇 줄의 코드로 큐를 구현할 수 있다.

```jsx
*// 배열로 구현한 간단한 큐*
const queue = [];

*// 요소 추가 (enqueue)*
queue.push(1);
queue.push(2);
queue.push(3);

console.log(queue); *// [1, 2, 3]// 요소 제거 (dequeue)*
const firstItem = queue.shift();
console.log(firstItem); *// 1*
console.log(queue); *// [2, 3]// 맨 앞 요소 확인 (peek)*
console.log(queue[0]); *// 2*
```

이 방법은 직관적이고 사용하기 쉽다. `push()` 메서드로 큐의 뒤에 요소를 추가하고, `shift()` 메서드로 큐의 앞에서 요소를 제거한다. 또한 인덱스 ``을 사용해 큐의 맨 앞 요소를 확인할 수 있다.

(👨🏻‍🏫 : 저는 처음에 이 방법만 알고 있었는데, 알고리즘 문제를 풀다 보니 이 방법이 생각보다 느리다는 걸 알게 되었답니다. 특히 큐의 크기가 커질수록요!)

## 배열 기반 큐의 연산

1. **Enqueue (요소 추가)**: `array.push(element)`
2. **Dequeue (요소 제거)**: `array.shift()`
3. **Peek (맨 앞 요소 확인)**: `array`
4. **isEmpty (큐가 비었는지 확인)**: `array.length === 0`
5. **Size (큐의 크기)**: `array.length`

## 2. 기본 함수를 사용했을 시의, 문제점 (BigO 관점)

배열로 큐를 구현하는 방법은 간단하지만, 성능 측면에서 큰 문제가 있다. 특히 `shift()` 메서드는 시간 복잡도가 **O(n)**이라는 점이 가장 큰 문제다.

## 배열 기반 큐의 시간 복잡도

| 연산            | 시간 복잡도 | 설명                                                     |
| --------------- | ----------- | -------------------------------------------------------- |
| Enqueue (push)  | O(1)        | 배열 끝에 요소 추가는 상수 시간                          |
| Dequeue (shift) | O(n)        | 배열 앞에서 요소 제거 시 모든 요소를 한 칸씩 이동해야 함 |
| Peek            | O(1)        | 인덱스로 직접 접근하므로 상수 시간                       |
| isEmpty/Size    | O(1)        | length 속성 확인은 상수 시간                             |

`shift()` 메서드의 시간 복잡도가 O(n)인 이유는 배열의 첫 번째 요소를 제거한 후 나머지 모든 요소의 인덱스를 재조정해야 하기 때문이다. 예를 들어, 100만 개의 요소가 있는 큐에서 `shift()`를 호출하면 99만 9999개의 요소를 한 칸씩 앞으로 이동시켜야 한다.

```jsx
*// 성능 테스트: 배열 기반 큐*
const arrayQueue = [];
const n = 100000;

console.time('Array Queue');
*// n개 요소 추가*
for (let i = 0; i < n; i++) {
  arrayQueue.push(i);
}
*// n개 요소 제거*
while (arrayQueue.length > 0) {
  arrayQueue.shift();
}
console.timeEnd('Array Queue');
*// 결과: Array Queue: ~500ms (n=100,000 기준)*
```

이 코드를 실행하면 n이 커질수록 실행 시간이 기하급수적으로 증가하는 것을 볼 수 있다. 이는 `shift()` 연산의 O(n) 시간 복잡도 때문이다.

(👨🏻‍🏫 : 코테를 위해 글을 찾아보다가 발견하셨다면, 더더욱 중요시 여겨야겠죠?)

## 3. 결국엔 직접 class 형식으로 구현해야하는 Queue

배열 기반 큐의 성능 문제를 해결하기 위해 두 가지 최적화된 큐 구현 방법을 살펴보자.

## 객체를 사용한 큐 구현

객체를 사용하면 인덱스 재조정 없이 요소를 추가하고 제거할 수 있어 모든 연산의 시간 복잡도를 O(1)로 유지할 수 있다.

```jsx
class Queue {
  constructor() {
    this.items = {};
    this.frontIndex = 0;
    this.backIndex = 0;
  }

  *// 요소 추가*
  enqueue(item) {
    this.items[this.backIndex] = item;
    this.backIndex++;
    return item;
  }

  *// 요소 제거*
  dequeue() {
    if (this.isEmpty()) {
      return null;
    }
    const item = this.items[this.frontIndex];
    delete this.items[this.frontIndex];
    this.frontIndex++;
    return item;
  }

  *// 맨 앞 요소 확인*
  peek() {
    if (this.isEmpty()) {
      return null;
    }
    return this.items[this.frontIndex];
  }

  *// 큐가 비었는지 확인*
  isEmpty() {
    return this.backIndex - this.frontIndex === 0;
  }

  *// 큐의 크기*
  size() {
    return this.backIndex - this.frontIndex;
  }
}

*// 사용 예*
const queue = new Queue();
queue.enqueue(1);
queue.enqueue(2);
queue.enqueue(3);
console.log(queue.dequeue()); *// 1*
console.log(queue.peek()); *// 2*
console.log(queue.size()); *// 2*
```

이 구현에서는 객체를 사용해 요소를 저장하고, `frontIndex`와 `backIndex`를 사용해 큐의 앞과 뒤를 추적한다. 요소를 제거할 때 실제로 객체에서 해당 키를 삭제하지만, 인덱스는 계속 증가한다. 이 방식은 모든 연산이 O(1)의 시간 복잡도를 가진다.

## 연결 리스트를 사용한 큐 구현

연결 리스트를 사용한 구현은 메모리 관리 측면에서 더 효율적일 수 있다.

```jsx
class Node {
  constructor(value) {
    this.value = value;
    this.next = null;
  }
}

class LinkedListQueue {
  constructor() {
    this.head = null;
    this.tail = null;
    this.size = 0;
  }

  *// 요소 추가*
  enqueue(value) {
    const newNode = new Node(value);
    if (this.isEmpty()) {
      this.head = newNode;
      this.tail = newNode;
    } else {
      this.tail.next = newNode;
      this.tail = newNode;
    }
    this.size++;
    return value;
  }

  *// 요소 제거*
  dequeue() {
    if (this.isEmpty()) {
      return null;
    }
    const value = this.head.value;
    this.head = this.head.next;
    this.size--;
    if (this.isEmpty()) {
      this.tail = null;
    }
    return value;
  }

  *// 맨 앞 요소 확인*
  peek() {
    if (this.isEmpty()) {
      return null;
    }
    return this.head.value;
  }

  *// 큐가 비었는지 확인*
  isEmpty() {
    return this.size === 0;
  }

  *// 큐의 크기*
  getSize() {
    return this.size;
  }
}

*// 사용 예*
const linkedQueue = new LinkedListQueue();
linkedQueue.enqueue(1);
linkedQueue.enqueue(2);
linkedQueue.enqueue(3);
console.log(linkedQueue.dequeue()); *// 1*
console.log(linkedQueue.peek()); *// 2*
console.log(linkedQueue.getSize()); *// 2*
```

연결 리스트 기반 큐는 각 노드가 다음 노드를 가리키는 포인터를 가지고 있어 요소 추가와 제거가 모두 O(1)의 시간 복잡도를 가진다. 또한 메모리를 동적으로 할당하므로 메모리 사용 측면에서도 효율적이다.

## 4. 퍼포먼스 차이

이제 세 가지 구현 방식의 성능을 비교해보자. 다음은 각 구현 방식으로 100만 개의 요소를 추가하고 제거하는 데 걸리는 시간을 측정한 결과다.

```jsx
*// 성능 비교 테스트*
const n = 1000000;

*// 배열 기반 큐*
console.time('Array Queue');
const arrayQueue = [];
for (let i = 0; i < n; i++) {
  arrayQueue.push(i);
}
while (arrayQueue.length > 0) {
  arrayQueue.shift();
}
console.timeEnd('Array Queue');

*// 객체 기반 큐*
console.time('Object Queue');
const objectQueue = new Queue();
for (let i = 0; i < n; i++) {
  objectQueue.enqueue(i);
}
while (!objectQueue.isEmpty()) {
  objectQueue.dequeue();
}
console.timeEnd('Object Queue');

*// 연결 리스트 기반 큐*
console.time('LinkedList Queue');
const linkedQueue = new LinkedListQueue();
for (let i = 0; i < n; i++) {
  linkedQueue.enqueue(i);
}
while (!linkedQueue.isEmpty()) {
  linkedQueue.dequeue();
}
console.timeEnd('LinkedList Queue');
```

실행 결과는 다음과 같다:

| 구현 방식           | 실행 시간 (n=1,000,000) |
| ------------------- | ----------------------- |
| 배열 기반 큐        | ~10-15초                |
| 객체 기반 큐        | ~100-150ms              |
| 연결 리스트 기반 큐 | ~200-250ms              |

(👨🏻‍🏫 : 제 컴퓨터에서는 배열 기반 큐가 무려 12초나 걸렸어요! 반면 객체 기반은 120ms 정도로 약 100배 차이가 났답니다. 정말 놀랍죠? 😮)

이 결과에서 볼 수 있듯이, 객체 기반 큐와 연결 리스트 기반 큐는 배열 기반 큐보다 훨씬 빠르다. 특히 객체 기반 큐는 가장 빠른 성능을 보여준다. 이는 `shift()` 연산의 O(n) 시간 복잡도 때문에 배열 기반 큐가 크게 불리하기 때문이다.

실제로 GitHub에 공개된 벤치마크 결과에 따르면, 요소 수가 증가할수록 배열과 객체 기반 구현의 성능 차이는 더 커진다:

| 요소 수   | 배열 (ms) | 객체 (ms) |
| --------- | --------- | --------- |
| 1,000,000 | 측정불가  | 131.25    |
| 500,000   | 측정불가  | 62.55     |
| 250,000   | 측정불가  | 31.60     |
| 100,000   | 497.67    | 12.31     |
| 50,000    | 321.56    | 4.55      |
| 25,000    | 73.09     | 2.28      |
| 10,000    | 11.38     | 0.91      |
| 5,000     | 2.05      | 0.47      |
| 2,500     | 0.43      | 0.25      |
| 1,000     | 0.1       | 0.1       |

출처: [GitHub Gist - Comparing JavaScript Queue implementations](https://gist.github.com/heymoose/30189d18efc1d8f582068f0bdd21854f)

## 5. 관련 라이브러리

실무에서는 직접 큐를 구현하는 대신 검증된 라이브러리를 사용하는 것이 좋다. 다음은 자바스크립트에서 사용할 수 있는 인기 있는 큐 라이브러리들이다.

## Denque

Denque는 자바스크립트에서 가장 빠른 양방향 큐(deque) 구현 중 하나로, Redis, MongoDB, MariaDB, MySQL 등의 공식 Node.js 라이브러리에서 사용된다.

```jsx
const Denque = require('denque');
const queue = new Denque([1, 2, 3, 4]);

queue.push(5);      *// 뒤에 추가*
queue.shift();      *// 앞에서 제거*
queue.unshift(0);   *// 앞에 추가*
queue.pop();        *// 뒤에서 제거*
```

출처: [GitHub - invertase/denque](https://github.com/invertase/denque)

## Bull (Bool 아닙니다 헷갈리시면 안 돼요! )

Bull은 Redis를 기반으로 한 강력한 큐 관리 라이브러리로, 작업 처리와 모니터링 기능을 제공한다.

```jsx
const Queue = require('bull');
const videoQueue = new Queue('video transcoding');

*// 작업 추가*
videoQueue.add({ video: 'video1.mp4' });

*// 작업 처리*
videoQueue.process(async (job) => {
  *// 비디오 처리 로직*
  return { processed: true };
});
```

출처: [NPM - bull](https://www.npmjs.com/package/bull)
