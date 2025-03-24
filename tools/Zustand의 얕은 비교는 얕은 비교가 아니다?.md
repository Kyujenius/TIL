## Zustand의 얕은 비교는 얕은 비교가 아니다?
React 개발자라면 상태 관리 라이브러리를 선택할 때 고민이 많다. Redux는 너무 무겁고, Context API는 성능이 걱정되고... 그러다 만난 Zustand! Redux를 축약시켜놓은 느낌과 더불어, 가볍고 직관적이라 많은 개발자들의 사랑을 받고 있다. 하지만 Zustand를 사용하다 보면 `shallow`라는 단어를 자주 마주치게 된다. 이 단어의 직역은 ‘얕음‘ 이다. 그러나 그 의미는 결국, "얕은 비교"에서 시작되었다. 그러나 JS에서의 '얕은 비교'와 Zustand 의 '얕은 비교'는 조금 다르다.

> (👨🏻‍🏫 : "안녕하세요! 오늘은 Zustand의 shallow에 대한 오해와 진실에 대해 알아볼 거예요. 저도 처음에는 이름만 보고 '얕은 비교겠지' 하고 넘어갔다가, 예상치 못 한 시기에 랜더링이 발생하며, 성능이 안 좋아질 때가 있어서, 큰코 다친 경험이 있답니다!")
> 

## 💡급하신 분들을 위해서 결론 먼저!

1. Zustand의 `shallow`는 완전한 깊은 비교도, 완전한 얕은 비교도 아닌 **"한 단계 더 깊은 비교"**를 수행한다.
2. 객체의 **최상위 속성만** 비교하며, 중첩된 객체의 내부까지는 비교하지 않는다.
3. 기본적으로 Zustand는 `Object.is`를 사용하여 참조 비교를 하지만, `shallow`를 사용하면 객체의 최상위 속성까지 비교한다.
4. 복잡한 중첩 객체를 다룰 때는 `shallow`만으로는 충분하지 않을 수 있다.
5. 비교를 편하게 해주기 위해서 평탄화된 구조를 택하면, 선택적 업데이트와 비교가 더 쉽다.

## 1. Zustand란?

Zustand는 React 애플리케이션을 위한 작고 빠른 상태 관리 라이브러리다. Redux와 같은 다른 상태 관리 라이브러리보다 보일러플레이트 코드가 적고, 직관적인 API를 제공한다.

### 주요 특징

- **미니멀리즘**: 최소한의 API와 보일러플레이트로 상태 관리를 단순화한다.
- **훅 기반 API**: React 훅을 활용하여 상태에 쉽게 접근할 수 있다.
- **성능 최적화**: 불필요한 리렌더링을 방지하기 위한 다양한 기능을 제공한다.
- **유연성**: 전역 상태와 로컬 상태 모두를 관리할 수 있다.

간단한 Zustand 스토어 예제를 살펴보자:

```jsx
import { create } from 'zustand'

const useStore = create((set) => ({
  bears: 0,
  increasePopulation: () => set((state) => ({ bears: state.bears + 1 })),
  removeAllBears: () => set({ bears: 0 }),
}))
```

이렇게 생성한 스토어는 컴포넌트에서 다음과 같이 사용할 수 있다:

```jsx
function BearCounter() {
  const bears = useStore((state) => state.bears)
  return <h1>{bears} bears around here...</h1>
}

function Controls() {
  const increasePopulation = useStore((state) => state.increasePopulation)
  return <button onClick={increasePopulation}>one up</button>
}
```

출처: https://zustand.docs.pmnd.rs/

## 2. Zustand의 shallow 기능에 대한 오해

Zustand를 사용하다 보면 `shallow` 함수를 자주 접하게 된다. 이름만 보면 "얕은 비교"를 수행할 것 같지만, 실제로는 조금 다르게 동작한다.

### shallow의 실제 동작 방식

`shallow`는 두 객체를 비교할 때 **최상위 속성만** 비교한다. 이는 완전한 얕은 비교도, 완전한 깊은 비교도 아닌 중간 단계의 비교 방식이다.

```jsx
*// 출처: https://zustand.docs.pmnd.rs/apis/shallow*
const objectLeft = {
  firstName: 'John',
  lastName: 'Doe',
  age: 30,
}

const objectRight = {
  firstName: 'John',
  lastName: 'Doe',
  age: 30,
}

Object.is(objectLeft, objectRight) *// -> false (참조가 다름)*
shallow(objectLeft, objectRight) *// -> true (최상위 속성이 같음)*
```

하지만 중첩된 객체가 있는 경우에는 다르게 동작한다:

```jsx
const objectLeft = {
  firstName: 'John',
  lastName: 'Doe',
  address: {
    city: 'New York'
  }
}

const objectRight = {
  firstName: 'John',
  lastName: 'Doe',
  address: {
    city: 'New York'
  }
}

Object.is(objectLeft, objectRight) *// -> false*
shallow(objectLeft, objectRight) *// -> false (address 객체의 참조가 다름)*
```

출처: https://zustand.docs.pmnd.rs/apis/shallow

> (👨🏻‍🏫 : "여기서 주목할 점은 address 객체의 내용은 같지만, 참조가 다르기 때문에 shallow는 두 객체를 다르다고 판단한다는 거예요. 이게 바로 많은 개발자들이 혼란스러워하는 부분이랍니다! 이제 C++ 을 배우신 분이라면 조금 이해가 빠를 텐데, 이에 대해서는 조금 더 찾아보세요! ~~이것만 얘기해도 글을 하나 더 써야해요!!~~ ")
> 

## 3. 얕은 비교란? 깊은 비교란?

`shallow`의 동작을 더 잘 이해하기 위해 얕은 비교와 깊은 비교의 개념을 명확히 알아보자.

### 얕은 비교(Shallow Compare)

얕은 비교는 두 객체의 **최상위 속성만** 비교한다. 객체의 참조가 같은지, 또는 원시 값이 같은지만 확인한다.

```jsx
const person1 = {
  name: 'John',
  age: 42,
};

const person2 = {
  name: 'John',
  age: 42,
};

const person3 = person1;

console.log(person1 === person2); *// false (참조가 다름)*
console.log(person1 === person3); *// true (같은 참조)*
```

출처: [https://phuoc.ng/collection/this-vs-that/deep-compare-vs-shallow-compare](https://phuoc.ng/collection/this-vs-that/deep-compare-vs-shallow-compare/)

### 깊은 비교(Deep Compare)

깊은 비교는 객체의 모든 속성을 재귀적으로 비교한다. 중첩된 객체의 내용까지 모두 비교하여 구조적으로 동일한지 확인한다.

```jsx
*// 깊은 비교 예시*
function deepEqual(obj1, obj2) {
  if (obj1 === obj2) return true;
  
  if (typeof obj1 !== 'object' || obj1 === null ||
      typeof obj2 !== 'object' || obj2 === null) {
    return false;
  }
  
  const keys1 = Object.keys(obj1);
  const keys2 = Object.keys(obj2);
  
  if (keys1.length !== keys2.length) return false;
  
  for (const key of keys1) {
    if (!keys2.includes(key)) return false;
    if (!deepEqual(obj1[key], obj2[key])) return false;
  }
  
  return true;
}

const obj1 = { a: 1, b: { c: 2 } };
const obj2 = { a: 1, b: { c: 2 } };

console.log(obj1 === obj2); *// false (얕은 비교)*
console.log(deepEqual(obj1, obj2)); *// true (깊은 비교)*
```

### Zustand의 shallow는?

Zustand의 `shallow`는 완전한 얕은 비교도, 완전한 깊은 비교도 아니다. 객체의 **최상위 속성만** 비교하는 "한 단계 더 깊은 비교"를 수행한다.

```jsx
const objectLeft = {
  firstName: 'John',
  lastName: 'Doe',
  age: 30,
}

const objectRight = {
  firstName: 'John',
  lastName: 'Doe',
  age: 30,
}

Object.is(objectLeft, objectRight) *// -> false*
shallow(objectLeft, objectRight) *// -> true*
```

## 4. Zustand, shallow 언제 잘 적용되는지 알고 쓰자

`shallow`는 특정 상황에서 매우 유용하지만, 모든 상황에 적합한 것은 아니다. 언제 `shallow`를 사용해야 할까?

### 적합한 사용 사례

1. **여러 상태 값을 선택할 때**:

```jsx
import { shallow } from "zustand/shallow";

const { name, age } = useUserStore(
  ({ user }) => ({ name: user.name, age: user.age }),
  shallow
);
```

**이 경우 `shallow`를 사용하면 `name`이나 `age`가 변경될 때만 컴포넌트가 리렌더링된다.**

1. **원시 타입이 아닌 값을 비교할 때**:

```jsx
const setLeft = new Set([1, 2, 3])
const setRight = new Set([1, 2, 3])

Object.is(setLeft, setRight) *// -> false*
shallow(setLeft, setRight) *// -> true*
```

Set, Map 같은 컬렉션 타입도 `shallow`로 비교할 수 있다.

출처*:* https://zustand.docs.pmnd.rs/apis/shallow

출처: https://www.bigbinary.com/blog/upgrading-react-state-management-with-zustand

### 적합하지 않은 사용 사례

1. **중첩된 객체 구조**:

```jsx
const objectLeft = {
  firstName: 'John',
  address: {
    city: 'New York'
  }
}

const objectRight = {
  firstName: 'John',
  address: {
    city: 'New York'
  }
}

shallow(objectLeft, objectRight) *// -> false*
```

중첩된 객체의 내용이 같더라도 참조가 다르면 `shallow`는 `false`를 반환한다.

1. **성능이 중요한 경우**:

`shallow` 비교는 `Object.is`보다 비용이 더 많이 든다. 불필요한 곳에서 `shallow`를 사용하면 성능 저하가 발생할 수 있다.

> (👨🏻‍🏫 : "React.memo나 useMemo를 사용할 때와 마찬가지로, 꼭 필요한 경우에만 shallow를 사용하는 것이 좋아요. 무분별하게 사용하면 오히려 성능이 저하될 수 있답니다!")
> 

## 5. 사용 가능한 다른 방법

`shallow`가 적합하지 않은 상황에서는 다른 방법을 고려해볼 수 있다.

### 선택자 함수 최적화

복잡한 객체 구조에서는 선택자 함수를 최적화하여 필요한 값만 정확히 선택하는 것이 좋다:

```jsx
*// 전체 user 객체를 선택하는 대신*
const user = useUserStore(state => state.user);

*// 필요한 속성만 선택*
const city = useUserStore(state => state.user.address.city);
```

이렇게 하면 `city` 값이 변경될 때만 컴포넌트가 리렌더링된다.

출처: https://www.bigbinary.com/blog/upgrading-react-state-management-with-zustand

### getState 활용

컴포넌트 렌더링 주기 외부에서 상태에 접근해야 할 때는 `getState`를 활용할 수 있다:

```jsx

const { getState } = useUserStore;

function handleClick() {
  const currentState = getState();
  *// 상태 값 사용*
}
```

이 방법은 최신 상태 값을 얻을 수 있지만, 상태 변화에 따른 자동 리렌더링은 발생하지 않는다.

### 상태 구조 재설계

중첩된 객체 구조가 많은 경우, 상태 구조를 평탄화(flattening)하는 것을 고려해볼 수 있다:

```jsx
*// 중첩된 구조*
const useStore = create((set) => ({
  user: {
    profile: {
      name: 'John',
      age: 30
    },
    settings: {
      theme: 'dark'
    }
  }
}));

*// 평탄화된 구조*
const useStore = create((set) => ({
  userName: 'John',
  userAge: 30,
  userTheme: 'dark'
}));
```

평탄화된 구조는 선택적 업데이트와 비교가 더 쉽다.

## 6. 결론

Zustand의 `shallow` 기능은 이름과 달리 완전한 얕은 비교가 아니라 "한 단계 더 깊은 비교"를 수행한다. 이는 객체의 최상위 속성만 비교하며, 중첩된 객체의 내용까지는 비교하지 않는다.

`shallow`를 효과적으로 사용하려면:

1. **상태 구조를 단순하게 유지**하라.
2. **필요한 경우에만** `shallow`를 사용하라.
3. 중첩된 객체가 많은 경우 **상태 구조를 재설계**하는 것을 고려하라.
4. `shallow`의 한계를 이해하고, 필요에 따라 **다른 방법을 활용**하라.
5. 이 모든 것은 성능과 직결된다. 

Zustand는 간결하고 효율적인 상태 관리 라이브러리지만, 그 기능을 제대로 이해하고 사용해야 최상의 결과를 얻을 수 있다. `shallow`라는 이름에 현혹되지 말고, 그 실제 동작 방식을 이해하고 적절히 활용하자.

> (👨🏻‍🏫 : “결국엔 왜 안될까에 대해서 오랜 기간 고민을 하며, 딥하게 찾다보면, 공식문서 + 기본적인 배경 지식의 결핍이 문제점이더라고요..! ㅎㅎ 튼튼한 지식을 쌓고서 개발을 시작한다면 정말 좋겠지만, 그렇다면 개발을 안 했을지도 모르겠다는 생각이 드는 거 같아요. 여러분은 그런 시행착오를 겪지 않았으면 좋겠습니다..! ")
> 

> 🙇🏻 글 내에 틀린 점, 오탈자, 비판, 공감 등 모두 적어주셔도 됩니다. 감사합니다..! 🙇🏻
>
