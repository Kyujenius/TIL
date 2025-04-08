## 💡급하신 분들을 위해서 결론 먼저!

1. Redux 리듀서는 반드시 순수함수여야 하며, 이는 상태의 예측 가능성을 보장한다.
2. Context API 사용 시 상태 업데이트 로직을 순수함수로 분리하고 Provider를 분할하면 성능과 유지보수성이 향상된다.
3. Zustand는 간결한 API로 순수성을 유지하면서도 보일러플레이트를 줄일 수 있다.
4. 이 모든 기반은 **불변성을 지키면 참조 비교만으로 객체가 변경됐는지 빠르게 확인할 수 있기 때문이다. 이는** **React의 렌더링 최적화에 매우 중요한 개념이다.**
5. 불변성과 순수함수는 상호보완적 관계로, 함께 사용할 때 최대 효과를 발휘한다.
6. 모든 현대 상태관리 도구는 순수함수와 불변성을 기반으로 하며, 이를 지키는 것이 중요하다.

여러분은 React 애플리케이션에서 상태관리를 할 때 이런 경험이 있지 않은가? 분명 상태를 업데이트했는데 UI가 변경되지 않거나, 예상치 못한 버그가 발생하거나, 디버깅이 어려워 머리를 쥐어뜯은 경험 말이다. 이런 문제의 원인 중 하나는 **상태 관리의 순수성**을 지키지 않았기 때문일 가능성이 높다.

> (👨🏻‍🏫 : 잘 모르겠으면 무조건 const 를 써라, 불변성이 중요하다. 랜더링 주기에 대해서 알아야 한다. 그 속엔 불변성이 존재한다. 리엑트는 순수함수를 사용한 함수형 프로그래밍이다. 등등 별로 연관이 없어보이는 개념들이 이어진다는 것을 이런 문제를 겪고나서야 알게 되었죠. 아, 이게 바로 불변성이구나... 하고 말이죠!)

현대 상태관리 라이브러리들(Redux, Context API, Zustand 등)은 모두 **함수형 프로그래밍**의 핵심 원칙인 **순수함수**와 **불변성**에 기반하고 있다. 이 두 개념은 상태관리를 예측 가능하고, 디버깅하기 쉽게 만드는 핵심 요소다. 오늘은 이 두 개념이 각 상태관리 도구에서 어떻게 적용되는지 살펴보자.

[**순수함수 및 리엑트에서 이를 어떻게 유도했는지, 그 과정에서 어떤 선택지가 있었는지, 결국엔 hook이란 것을 통해서 상태를 유지하게 된 것 까지에 대한 전반적인 지식이 있으면 더 재미있을 거예요!**](https://velog.io/@kyujenius/series/%EB%A6%AC%EC%97%91%ED%8A%B8%EC%99%80-%EC%88%9C%EC%88%98%ED%95%A8%EC%88%98%EC%9D%98-%EA%B4%80%EA%B3%84)

---

## 1. Redux 리듀서와 순수함수의 관계

Redux는 상태관리 라이브러리 중에서도 가장 엄격하게 순수함수와 불변성을 강조한다. Redux의 세 가지 원칙 중 두 가지가 바로 **"상태는 읽기 전용이다(State is read-only)"**와 **"변화는 순수 함수로 작성되어야 한다(Changes are made with pure functions)"**이다.

### 리듀서의 순수함수 특성

Redux에서 리듀서는 다음과 같은 특성을 가진 순수함수여야 한다:

1. **동일한 입력에는 항상 동일한 출력을 반환한다**
2. **외부 상태를 변경하지 않는다(사이드 이펙트가 없다)**
3. **입력으로 받은 상태를 직접 수정하지 않고 새로운 상태 객체를 반환한다**

```jsx
// 순수함수인 리듀서의 예
function counterReducer(state = 0, action) {
  switch (action.type) {
    case "INCREMENT":
      return state + 1; // 새로운 상태 반환 (+1)
    case "DECREMENT":
      return state - 1; // 새로운 상태 반환 (-1)
    default:
      return state; // 관련 없는 액션은 기존 상태 반환
  }
}
```

> (👨🏻‍🏫 : 리듀서가 순수함수가 아니라면 어떻게 될까요? 예를 들어 API 호출이나 랜덤값 생성 같은 사이드 이펙트가 있다면, 같은 액션에 대해 다른 결과가 나올 수 있어요. 그럼 애초에 일방향 데이터 관리를 하려는 목적도 사라지고(flux 패턴이라고 하죠!), 순서에 따른 순차적인 디버깅 같은 Redux의 강력한 기능을 사용할 수 없게 됩니다!)

### 불변성 유지 방법

Redux에서 불변성을 유지하는 방법은 다음과 같다:

1. **스프레드 연산자(...)** 사용하기
2. **Object.assign()** 사용하기
3. **배열 메서드**에서 원본을 변경하는 메서드(push, pop 등) 대신 새 배열을 반환하는 메서드(map, filter, concat 등) 사용하기
4. **Immer** 같은 불변성 라이브러리 사용하기

### Redux 스토어 및 액선 생성 함수

```jsx
// 스토어 생성
import { createStore } from "redux";
const store = createStore(userReducer);

// 액션 생성 함수
const updateName = (name) => ({
  type: "UPDATE_NAME",
  payload: name,
});

const updateAge = (age) => ({
  type: "UPDATE_AGE",
  payload: age,
});

// 스토어 구독하여 상태 변화 감지
store.subscribe(() => {
  console.log("현재 상태:", store.getState());
});

// 액션 디스패치하여 상태 업데이트
store.dispatch(updateName("김철수"));
store.dispatch(updateAge(25));
```

### Redux 내에서의 Reducder 구현

```jsx
// 객체 상태 업데이트 예시
function userReducer(state = { name: "", age: 0 }, action) {
  switch (action.type) {
    case "UPDATE_NAME":
      // 스프레드 연산자로 불변성 유지
      return {
        ...state,
        name: action.payload,
      };
    case "UPDATE_AGE":
      // Object.assign으로 불변성 유지
      return Object.assign({}, state, {
        age: action.payload,
      });
    default:
      return state;
  }
}
```

이를 이제 아래와 같이 사용한다.

```jsx
// React 컴포넌트에서 사용 예시
import React from "react";
import { useSelector, useDispatch } from "react-redux";

function UserProfile() {
  // 상태 가져오기
  const user = useSelector((state) => state);
  const dispatch = useDispatch();

  // 이벤트 핸들러
  const handleNameChange = (e) => {
    dispatch(updateName(e.target.value));
  };

  const handleAgeChange = (e) => {
    dispatch(updateAge(Number(e.target.value)));
  };

  return (
    <div>
      <h2>사용자 프로필</h2>
      <div>
        <label>이름: </label>
        <input type="text" value={user.name} onChange={handleNameChange} />
      </div>
      <div>
        <label>나이: </label>
        <input type="number" value={user.age} onChange={handleAgeChange} />
      </div>
      <div>
        <p>이름: {user.name}</p>
        <p>나이: {user.age}</p>
      </div>
    </div>
  );
}

export default UserProfile;
```

> (👨🏻‍🏫 : 조~금 복잡하죠..? ㅎㅎ 그래도 Redux에 대한 이해도가 높다면, 어떤 상태관리를 택하더라도 어려움이 없으니, 이에 대해서 알아두면 정말 도움이 많이 될 거예요!)

---

## 2. Context API 사용 시 순수성 유지하기

Context API는 Redux와 달리 상태 관리를 위한 패턴을 강제하지 않는다. 그러나 Context API를 사용할 때도 순수함수와 불변성 원칙을 적용하면 더 예측 가능하고 유지보수하기 쉬운 코드를 작성할 수 있다. 일반적으로 사용하던 기본 hooks (useState, useEffect) 등을 사용하여 진입 장벽이 낮은 것이 특징이다.

### Provider 상태 분할하기

Context API를 사용할 때 성능 최적화를 위해 Provider를 분할하는 것이 좋다. **각 Provider는 특정 기능이나 애플리케이션 영역**에 집중해야 한다.

```jsx
// 상태를 분리한 Context Provider 예시
const UserContext = React.createContext();
const ThemeContext = React.createContext();

function App() {
  const [user, setUser] = useState({ name: "", role: "" });
  const [theme, setTheme] = useState("light");

  return (
    <UserContext.Provider value={{ user, setUser }}>
      <ThemeContext.Provider value={{ theme, setTheme }}>
        <MainContent />
      </ThemeContext.Provider>
    </UserContext.Provider>
  );
}
// 출처: React 공식 문서
```

> (👨🏻‍🏫 : **모든 상태를 하나의 Context에 넣으면 어떤 상태가 변경되든 해당 Context를 사용하는 모든 컴포넌트가 리렌더링됩니다.** 분리해두면 필요한 컴포넌트만 리렌더링되니 성능이 좋아지죠!)

### 상태 업데이트 로직을 순수함수로 분리하기

Context API에서도 상태 업데이트 로직을 순수함수로 분리하면서, useReducer를 함께 사용하는 패턴의 예시입니다. 이 패턴은 React 애플리케이션에서 상태 관리를 위해 널리 사용됩니다.

```jsx
function CountProvider({ children }) {
  // 상태 업데이트 로직을 순수함수로 분리한 예시
  const initialState = { count: 0 };

  // 순수함수로 상태 업데이트 로직 분리
  function countReducer(state, action) {
    switch (action.type) {
      case "increment":
        return { count: state.count + 1 };
      case "decrement":
        return { count: state.count - 1 };
      default:
        return state;
    }
  }

  const [state, dispatch] = useReducer(countReducer, initialState);

  return (
    <CountContext.Provider value={{ state, dispatch }}>
      {children}
    </CountContext.Provider>
  );
}
```

아래에서 이렇게 사용할 수 있습니다.

```jsx
function Counter() {
  const { state, dispatch } = useContext(CountContext);

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
      <button onClick={() => dispatch({ type: "decrement" })}>-</button>
    </div>
  );
}
```

`useState`와 `useReducer`는 동일한 방식이나, 각각 다른 상황에 적합한 상태 관리 방식이다.

### useState vs useReducer

### useState가 적합한 경우:

- **단순한 상태 관리**: 카운터, 토글, 폼 입력값 등 간단한 상태 (위의 예시정도라면 간단히 useState 로도 가능)
- **독립적인 상태들**: 서로 연관되지 않은 여러 상태
- **상태 업데이트 로직이 단순할 때**: `setState(newValue)` 형태의 간단한 업데이트

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
    </div>
  );
}
```

### useReducer가 적합한 경우:

- **복잡한 상태 로직**: 여러 하위 값을 포함하는 복잡한 상태 객체
- **연관된 상태 변화**: 여러 상태가 함께 변경되어야 하는 경우
- **상태 변화에 일관된 패턴이 필요할 때**: 여러 컴포넌트에서 동일한 상태 변화 로직을 재사용
- **상태 변화를 명확하게 추적하고 싶을 때**: 액션 타입으로 상태 변화를 문서화

### Context API와 useReducer를 함께 사용하는 이유

1. **관심사 분리**: 상태 업데이트 로직(reducer)과 상태 제공(Context)을 분리한다.
2. **테스트 용이성**: 순수 함수인 reducer는 독립적으로 테스트하기 쉽다.
3. **디버깅 용이성**: 액션 타입을 통해 상태 변화를 추적하기 쉽다.
4. **확장성**: 애플리케이션이 커져도 일관된 패턴으로 상태 관리가 가능하다.
5. **미들웨어 패턴**: 로깅, 비동기 처리 등을 위한 미들웨어 패턴 적용이 가능하다.

이 패턴은 특히 중간 규모 이상의 애플리케이션이나, 여러 개발자가 함께 작업하는 프로젝트에서 유용하다. Redux와 유사한 패턴을 제공하면서도 외부 라이브러리 없이 React 내장 기능만으로 구현할 수 있다는 장점이 있다.

```jsx
// 사용 예시
function App() {
  return (
    <CountProvider>
      <SomeComponent />
      <AnotherComponent />
    </CountProvider>
  );
}

function SomeComponent() {
  const { state, dispatch } = useContext(CountContext);

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
    </div>
  );
}
```

물론 간단한 상태 관리라면 useState로 충분하다. 상황에 맞게 적절한 도구를 선택하는 것이 중요하다.

> (👨🏻‍🏫 : 복잡한 폼이나 게임 상태 같은 경우 결국엔, useState로 관리하면 코드가 지저분해지고 버그가 생기기 쉬워요. useReducer를 쓰면 "어떤 액션이 발생했는지"와 "그에 따라 상태가 어떻게 변해야 하는지"를 명확히 분리할 수 있답니다!)

---

## 3. Zustand 사용 시 순수성 유지하기

Zustand는 Redux의 보일러플레이트를 줄이면서도 예측 가능한 상태 관리를 제공하는 라이브러리다. Zustand는 액션이나 리듀서 없이 간단한 함수 기반 스토어를 사용한다.

### Zustand의 상태 업데이트 방식

Zustand에서는 `set` 함수를 사용해 상태를 업데이트한다. 이는 마치, useState와 똑같이 작용한다. 이때 불변성을 유지하는 방식으로 업데이트해야 한다.

```jsx
import { create } from "zustand";

// Zustand 스토어 생성
const useStore = create((set) => ({
  count: 0,
  // 순수함수처럼 작동하는 상태 업데이트 함수
  increase: () => set((state) => ({ count: state.count + 1 })),
  decrease: () => set((state) => ({ count: state.count - 1 })),
}));

function Counter() {
  const { count, increase, decrease } = useStore();
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increase}>+</button>
      <button onClick={decrease}>-</button>
    </div>
  );
}
```

> (👨🏻‍🏫 : Zustand의 set 함수는 내부적으로 불변성을 보장해주기 때문에 편리합니다. 하지만 복잡한 객체를 다룰 때는 여전히 불변성에 신경써야 해요!)

### Zustand에서 순수성 유지하기

Zustand에서도 상태 업데이트 로직을 순수함수처럼 작성하는 것이 중요하다:

1. **상태 업데이트 함수는 예측 가능해야 한다**
2. **외부 상태에 의존하지 않아야 한다**
3. **사이드 이펙트를 포함하지 않아야 한다**

```jsx
// 좋은 예: 순수함수처럼 작동하는 상태 업데이트
const useUserStore = create((set) => ({
  user: { name: "", age: 0 },
  updateName: (name) =>
    set((state) => ({
      user: { ...state.user, name },
    })),
  updateAge: (age) =>
    set((state) => ({
      user: { ...state.user, age },
    })),
}));

// 나쁜 예: API 호출 같은 사이드 이펙트를 포함
const useBadStore = create((set) => ({
  data: null,
  fetchData: async () => {
    const response = await fetch("/api/data");
    const data = await response.json();
    set({ data });
  },
}));
```

---

## 4. 불변성(Immutability)과 순수함수의 관계

불변성과 순수함수는 상호보완적인 관계에 있다. 순수함수가 제대로 작동하기 위해서는 불변성이 보장되어야 하며, 불변성을 유지하기 위해서는 순수함수를 사용하는 것이 효과적이다.

### 불변성이란?

불변성은 한번 생성된 데이터가 변경되지 않는 특성을 말한다. JavaScript에서는 객체와 배열이 참조 타입이기 때문에, 불변성을 유지하려면 원본 데이터를 직접 수정하는 대신 새로운 데이터를 생성해야 한다.

```jsx
// 불변성을 지키지 않는 예
const user = { name: "John", age: 30 };
user.age = 31; // 원본 객체 직접 수정 (불변성 위반)

// 불변성을 지키는 예
const user = { name: "John", age: 30 };
const updatedUser = { ...user, age: 31 }; // 새로운 객체 생성
// 출처: JavaScript 불변성 패턴
```

> (👨🏻‍🏫 : **불변성을 지키면 참조 비교만으로 객체가 변경됐는지 빠르게 확인할 수 있어요.** **React의 렌더링 최적화에 매우 중요한 개념**이죠!)

### 순수함수와 불변성의 시너지

순수함수와 불변성이 함께 사용될 때 얻을 수 있는 이점은 다음과 같다:

1. **예측 가능성**: 동일한 입력에 항상 동일한 출력을 보장한다
2. **테스트 용이성**: 외부 의존성이 없어 테스트하기 쉽다
3. **디버깅 용이성**: 상태 변화를 추적하기 쉽다
4. **동시성**: 데이터 레이스 컨디션을 방지한다
5. **시간 여행 디버깅**: 이전 상태로 쉽게 돌아갈 수 있다

```jsx
// 순수함수와 불변성을 함께 사용한 예
function updateUserName(user, newName) {
  // 불변성을 지키면서 새로운 객체 반환
  return { ...user, name: newName };
}

const user = { name: "John", age: 30 };
const updatedUser = updateUserName(user, "Jane");

console.log(user); // { name: 'John', age: 30 } - 원본 유지
console.log(updatedUser); // { name: 'Jane', age: 30 } - 새 객체
```

---

## 5. 결론

현대 상태관리 도구들은 모두 **순수함수**와 **불변성**이라는 함수형 프로그래밍의 핵심 원칙에 기반하고 있다. 이 원칙들을 지키면 다음과 같은 이점을 얻을 수 있다:

1. **예측 가능한 상태 변화**
2. **디버깅 용이성**
3. **성능 최적화**
4. **테스트 용이성**
5. **유지보수성 향상**

각 상태관리 도구마다 구현 방식은 다르지만, 결국엔 그들간의 공통점은 바로 ‘순수함수와 불변성을 지키는 것’이다. Redux는 리듀서를 통해, Context API는 상태 업데이트 로직 분리를 통해, Zustand는 useState와 같은 방식을 상태 관리방식을 통해 이 원칙들을 구현한다.

> (👨🏻‍🏫 : 결국 어떤 상태관리 도구를 선택하든, 순수함수와 불변성을 지키는 것이 중요합니다. 이 두 개념은 함수형 프로그래밍의 기본이자 현대 프론트엔드 개발의 필수 요소가 되었어요!)

상태관리 도구를 선택할 때는 프로젝트의 복잡성과 팀의 친숙도를 고려하되, 어떤 도구를 선택하든 순수함수와 불변성 원칙을 지키는 것이 중요하다. 이 원칙들을 지키면 더 안정적이고 유지보수하기 쉬운 애플리케이션을 만들 수 있다.

> 🙇🏻 글 내에 틀린 점, 오탈자, 비판, 공감 등 모두 적어주셔도 됩니다. 감사합니다..! 🙇🏻
