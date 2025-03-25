
## React의 근간, Flux 패턴 - 현대 상태관리 라이브러리와 비교하며
"지금 내 데이터는 어디에 있을까?" 🤔 React 개발을 하다 보면 누구나 한 번쯤 이런 의문에 빠진다. 컴포넌트가 늘어나고 상태가 복잡해질수록 데이터의 흐름을 추적하기 어려워지고, 결국 디버깅은 악몽이 된다. 이런 상황에서 Facebook이 내놓은 해결책이 바로 **Flux 패턴**이다. **단방향 데이터 흐름**이라는 단순한 원칙으로 복잡한 React 애플리케이션을 명확하게 관리할 수 있게 해주는 이 패턴은 2014년 소개된 이후 React 생태계의 기본이 되었다.

> (👨🏻‍🏫 : "제가 처음 React를 배울 때는 Recoil을 썼었어요. 해당 상태관리 라이브러리는 Redux의 경량화 버전 느낌이었고, 굳이 Redux는 왜이리 복잡하게 관리하지? 라는 생각을 하던 찰나에, 보다 더 큰 프로젝트를 해보게 되었고, 그 규모가 커질수록 Redux의 근간인 Flux의 단방향 흐름이 얼마나 고마운 것인지 깨달았어요!")
> 

오늘은 React 애플리케이션의 데이터 흐름을 관리하는 **Flux 패턴**에 대해 알아보고, 이 패턴이 왜 React와 찰떡궁합인지 살펴보자.

## 💡급하신 분들을 위해서 결론 먼저!

1. **Flux는 Facebook이 개발한 단방향 데이터 흐름 아키텍처 패턴**으로, 복잡한 UI 애플리케이션의 상태 관리를 단순화한다.
2. **Action**, **Dispatcher**, **Store**, **View**의 네 가지 주요 구성 요소로 이루어져 있으며, 데이터는 항상 한 방향으로만 흐른다.
3. 그 방향은 Action → Dispathcer → Store → View 이다.
4. React의 컴포넌트 기반 접근 방식과 완벽하게 호환되며, 예측 가능한 상태 관리를 가능하게 한다.
5. 양방향 데이터 바인딩과 달리 연쇄적인 업데이트를 방지하여 애플리케이션의 동작을 예측하기 쉽게 만든다.
6. Redux, MobX 등 현대적인 상태 관리 라이브러리의 기반이 되었으며, 그 핵심 원칙은 여전히 유효하다.

## 1. Flux란 무엇인가?

Flux는 Facebook이 React와 함께 사용하기 위해 개발한 애플리케이션 아키텍처 패턴이다. 이는 단방향 데이터 흐름을 강조하여 애플리케이션의 상태를 예측 가능하게 만든다.

### Flux의 탄생 배경

Facebook 개발팀은 기존의 MVC(Model-View-Controller) 패턴으로 대규모 애플리케이션을 개발하면서 여러 문제에 직면했다. 특히 양방향 데이터 바인딩이 연쇄적인 업데이트를 발생시켜 애플리케이션의 동작을 예측하기 어렵게 만들었다.

>![](https://velog.velcdn.com/images/kyujenius/post/bcebe91a-fa22-47db-ab03-0127b478ea8a/image.png)
양방향 데이터 패턴의 문제점을 나타내는 사진입니다.


> (👨🏻‍🏫 : "양방향 데이터 바인딩은 마치 도미노와 같아요. 하나가 쓰러지면 연쇄적으로 다른 것들도 쓰러지죠. 이런 상황에서 어떤 도미노가 먼저 쓰러졌는지 추적하기 정말 어렵답니다! 따라서, 돌아가야하는 게 항상 불편하더라도 단방향을 택한다면, 예측하기가 쉬워지죠")
> 



이러한 문제를 해결하기 위해 Facebook은 단방향 데이터 흐름을 가진 새로운 아키텍처 패턴인 Flux를 도입했다. 이 패턴은 2014년 F8 컨퍼런스에서 처음 소개되었다.

### Flux의 핵심 원칙

Flux 아키텍처는 세 가지 핵심 원칙을 기반으로 한다:

1. **단방향 데이터 흐름(Unidirectional Data Flow)**: 데이터는 항상 한 방향으로만 흐른다. 이는 양방향 데이터 바인딩과 달리 데이터의 변화가 예측 가능한 경로를 따라 전파되도록 한다.
2. **단일 진실 소스(Single Source of Truth)**: 애플리케이션의 상태는 스토어에서 관리되며, 이는 디버깅을 단순화하고 애플리케이션의 상태를 쉽게 파악할 수 있게 한다.
3. **액션을 통한 상태 변경(State Mutation via Actions)**: 애플리케이션의 상태는 오직 액션을 통해서만 변경된다. 이 액션들은 컴포넌트에 의해 디스패치되어 상태 업데이트를 위한 통제된 메커니즘을 생성한다.

![](https://velog.velcdn.com/images/kyujenius/post/a9ad51d6-3aff-4259-9b80-b4e29857aac8/image.png)


## 2. Flux의 구성 요소

Flux 아키텍처는 네 가지 주요 구성 요소로 이루어져 있다: **Actions**, **Dispatcher**, **Stores**, **Views**. 이 구성 요소들이 어떻게 상호작용하는지 이해하는 것이 Flux 패턴을 이해하는 핵심이다.

출처: https://www.linkedin.com/pulse/flux-pattern-react-js-example-sumit-mishra-mqnkc

### Actions

**Actions**는 사용자 상호작용이나 이벤트를 나타내며, 상태 변경을 트리거한다. 각 액션은 일반적으로 타입과 페이로드를 포함하는 자바스크립트 객체이다.

![](https://velog.velcdn.com/images/kyujenius/post/a3a0d2f8-bdb4-466e-b8b4-95149c3ad4fb/image.png)

### Dispatcher

**Dispatcher**는 Flux 아키텍처의 중앙 허브로, 모든 데이터 흐름을 관리한다. 액션이 발생하면 디스패처는 이를 등록된 모든 스토어에 전달한다. 중요한 점은 **디스패처는 스토어가 일관된 순서로 업데이트되도록 보장**한다는 것이다.

![](https://velog.velcdn.com/images/kyujenius/post/73f7fb8e-694f-482a-af55-4c32f59143de/image.png)

### Stores

**Stores**는 애플리케이션의 상태와 상태를 변경하는 로직을 포함한다. 스토어는 디스패처에 등록되어 액션에 응답하고, 상태가 변경되면 뷰에 알린다.

![](https://velog.velcdn.com/images/kyujenius/post/3cb59e67-86c6-4f4c-b2f7-6672eafa1cc1/image.png)

### Views (React Components)

**Views**는 스토어의 변경사항을 구독하고 그에 따라 업데이트된다. 또한 사용자 상호작용에 응답하여 액션을 디스패치한다.

![](https://velog.velcdn.com/images/kyujenius/post/8b1c1268-900d-4d36-8ee4-c253918406ab/image.png)


## 3. Flux와 React의 시너지

Flux는 React와 함께 사용할 때 가장 큰 효과를 발휘한다. 왜 그런지 두 기술이 어떻게 서로를 보완하는지 살펴보자.

### React와 Flux의 호환성

React는 UI를 구축하기 위한 컴포넌트 기반 라이브러리이며, Flux는 데이터 흐름을 관리하기 위한 아키텍처 패턴이다. 이 둘은 서로 다른 문제를 해결하지만, 함께 사용할 때 강력한 시너지를 발휘한다.

React의 선언적 렌더링 모델은 Flux의 단방향 데이터 흐름과 자연스럽게 어울린다. 스토어가 상태를 변경하면 React 컴포넌트는 이를 감지하고 자동으로 UI를 업데이트한다.

> (👨🏻‍🏫 : "React와 Flux는 마치 피아노와 피아니스트 같아요. 피아노(React)는 훌륭한 악기지만, 피아니스트(Flux)가 있어야 아름다운 음악을 만들어낼 수 있답니다!")
> 

### 단방향 데이터 흐름의 이점

Flux의 단방향 데이터 흐름은 다음과 같은 이점을 제공한다:

1. **예측 가능성**: 데이터가 항상 한 방향으로만 흐르기 때문에 애플리케이션의 동작을 예측하기 쉽다.
2. **디버깅 용이성**: 문제가 발생했을 때 데이터의 흐름을 추적하기 쉬워 디버깅이 간단해진다.
3. **유지보수성**: 코드베이스가 커져도 데이터 흐름의 패턴이 일관되게 유지되어 유지보수가 용이하다.
4. **테스트 용이성**: 각 구성 요소가 명확하게 분리되어 있어 단위 테스트가 쉽다.

## 4. Flux 구현 예제

이론을 이해했으니 이제 간단한 Todo 애플리케이션을 통해 Flux 패턴을 실제로 구현해보자.

### 기본 설정

먼저 필요한 의존성을 설치한다:

```bash
npm install flux
*# 또는*
yarn add flux
```

### 전체 구현

이제 Flux 패턴의 각 구성 요소를 구현해보자:

1. **Actions 정의**:

```jsx
*// actions.js*
import Dispatcher from './dispatcher';

export const ActionTypes = {
  ADD_TODO: 'ADD_TODO',
  TOGGLE_TODO: 'TOGGLE_TODO',
  REMOVE_TODO: 'REMOVE_TODO',
};

export const ActionCreators = {
  addTodo: (text) => {
    Dispatcher.dispatch({
      type: ActionTypes.ADD_TODO,
      payload: text,
    });
  },
  toggleTodo: (id) => {
    Dispatcher.dispatch({
      type: ActionTypes.TOGGLE_TODO,
      payload: id,
    });
  },
  removeTodo: (id) => {
    Dispatcher.dispatch({
      type: ActionTypes.REMOVE_TODO,
      payload: id,
    });
  },
};
```

1. **Dispatcher 생성**:

```jsx
*// dispatcher.js*
import { Dispatcher } from 'flux';

export default new Dispatcher();
```

1. **Store 구현**:

```jsx
*// todoStore.js*
import { EventEmitter } from 'events';
import Dispatcher from './dispatcher';
import { ActionTypes } from './actions';

class TodoStore extends EventEmitter {
  constructor() {
    super();
    this.todos = [];
    this.nextId = 1;
  }

  getAll() {
    return this.todos;
  }

  handleActions(action) {
    switch (action.type) {
      case ActionTypes.ADD_TODO:
        this.todos.push({
          id: this.nextId++,
          text: action.payload,
          completed: false,
        });
        this.emit('change');
        break;
        
      case ActionTypes.TOGGLE_TODO:
        this.todos = this.todos.map(todo => 
          todo.id === action.payload 
            ? { ...todo, completed: !todo.completed } 
            : todo
        );
        this.emit('change');
        break;
        
      case ActionTypes.REMOVE_TODO:
        this.todos = this.todos.filter(todo => todo.id !== action.payload);
        this.emit('change');
        break;
        
      default:
        *// Do nothing*
    }
  }
}

const todoStore = new TodoStore();
Dispatcher.register(todoStore.handleActions.bind(todoStore));
export default todoStore;
```

1. **React 컴포넌트 구현**:

```jsx
*// TodoApp.jsx*
import React, { useState, useEffect } from 'react';
import { ActionCreators } from './actions';
import TodoStore from './todoStore';

const TodoApp = () => {
  const [todos, setTodos] = useState(TodoStore.getAll());
  const [newTodo, setNewTodo] = useState('');

  useEffect(() => {
    TodoStore.on('change', updateTodos);
    return () => {
      TodoStore.removeListener('change', updateTodos);
    };
  }, []);

  const updateTodos = () => {
    setTodos(TodoStore.getAll());
  };

  const handleAddTodo = (e) => {
    e.preventDefault();
    if (newTodo.trim()) {
      ActionCreators.addTodo(newTodo);
      setNewTodo('');
    }
  };

  const handleToggleTodo = (id) => {
    ActionCreators.toggleTodo(id);
  };

  const handleRemoveTodo = (id) => {
    ActionCreators.removeTodo(id);
  };

  return (
    <div className="todo-app">
      <h1>Todo List</h1>
      
      <form onSubmit={handleAddTodo}>
        <input
          type="text"
          value={newTodo}
          onChange={(e) => setNewTodo(e.target.value)}
          placeholder="What needs to be done?"
        />
        <button type="submit">Add Todo</button>
      </form>
      
      <ul className="todo-list">
        {todos.map((todo) => (
          <li key={todo.id} className={todo.completed ? 'completed' : ''}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => handleToggleTodo(todo.id)}
            />
            <span>{todo.text}</span>
            <button onClick={() => handleRemoveTodo(todo.id)}>Remove</button>
          </li>
        ))}
      </ul>
    </div>
  );
};

export default TodoApp;
```

이 예제에서 우리는 Flux 패턴의 모든 주요 구성 요소를 구현했다. Actions는 사용자의 상호작용을 나타내고, Dispatcher는 이러한 액션을 Store로 전달하며, Store는 애플리케이션의 상태를 관리한다. 마지막으로 React 컴포넌트(View)는 Store의 변경사항을 구독하고 UI를 업데이트한다.

> (👨🏻‍🏫 : "이렇게 구현하면 데이터의 흐름이 한 방향으로만 진행되는 것을 볼 수 있어요. 사용자 액션 → Dispatcher → Store → View 의 순서로요. 이런 명확한 흐름 덕분에 애플리케이션의 동작을 예측하고 디버깅하기가 훨씬 쉬워진답니다!")
> 

## 5. Flux의 현대적 변형과 대안들

Flux 패턴은 React 생태계에 큰 영향을 미쳤지만, 시간이 지나면서 다양한 변형과 대안들이 등장했다. 이들은 Flux의 핵심 원칙을 유지하면서도 더 편리하고 강력한 기능을 제공한다.

### Redux

출처: https://redux.js.org/introduction/getting-started

Redux는 Flux의 아이디어를 가장 충실하게 따르지만, 차이점은, 단일 스토어를 사용하고 그 스토어를 순수 함수인 리듀서라는 상태를 업데이트한다. Redux의 예측 가능한 상태 컨테이너는 많은 개발자들에게 사랑받고 있다. 또한, 비동기 액션 처리 등을 위한 미들웨어 시스템을 도입하여 Flux의 개념을 확장했습니다.

```jsx
import { createStore } from 'redux'

function counterReducer(state = { value: 0 }, action) {
  switch (action.type) {
    case 'counter/incremented':
      return { value: state.value + 1 }
    case 'counter/decremented':
      return { value: state.value - 1 }
    default:
      return state
  }
}

let store = createStore(counterReducer)

store.subscribe(() => console.log(store.getState()))

store.dispatch({ type: 'counter/incremented' })
*// {value: 1}*
store.dispatch({ type: 'counter/incremented' })
*// {value: 2}*
store.dispatch({ type: 'counter/decremented' })
*// {value: 1}*
```

### MobX

출처: https://mobx.js.org/README.html

MobX는 반응형 프로그래밍 모델을 사용하여 상태 관리를 단순화한다. Flux의 명시적인 액션 디스패치 대신, MobX는 관찰 가능한 상태와 자동 추적을 사용한다. 가장 큰 특징으로 관찰 가능한 객체로 만들어 자동으로 변경을 감지하고 업데이트합니다. MobX에서는 액션이 직접 상태를 업데이트할 수 있습니다

```jsx
import { makeAutoObservable } from "mobx"

class Timer {
    secondsPassed = 0

    constructor() {
        makeAutoObservable(this)
    }

    increase() {
        this.secondsPassed += 1
    }

    reset() {
        this.secondsPassed = 0
    }
}

const myTimer = new Timer()
```

### Context API와 useReducer

출처: https://react.dev/reference/react/useReducer

React 16.3에서 공식적으로 도입된 Context API와 useReducer 훅을 조합하면 Flux와 유사한 패턴을 구현할 수 있다. 이는 작은 규모의 애플리케이션에서 외부 라이브러리 없이 상태 관리를 할 수 있게 해준다. useReducer는 React 내장 훅으로, Redux의 핵심 개념을 React 컴포넌트 내부로 가져왔습니다.

```jsx

import React, { useReducer } from 'react';

const initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```

### Recoil

출처: https://recoiljs.org/docs/introduction/getting-started

Facebook이 개발한 Recoil은 React를 위한 상태 관리 라이브러리로,Flux의 스토어 대신 작고 독립적인 원자(atom) 이라는 단위로 상태를 관리합니다. 따라서, 상태를 더 작은 단위로 관리할 수 있게 해준다. 이는 React의 내부 메커니즘에 더 가깝게 설계되었습니다. Context API를 기반으로 구현되어 React의 내부 동작과 더 잘 통합됩니다. 

```jsx
import { atom, useRecoilState } from 'recoil';

const counterState = atom({
  key: 'counterState',
  default: 0,
});

function Counter() {
  const [count, setCount] = useRecoilState(counterState);

  return (
    <div>
      <button onClick={() => setCount(count - 1)}>-</button>
      <span>{count}</span>
      <button onClick={() => setCount(count + 1)}>+</button>
    </div>
  );
}
```

이러한 현대적인 상태 관리 솔루션들은 Flux의 핵심 아이디어를 계승하면서도, 각각의 특성에 맞는 추가적인 이점을 제공한다. 개발자는 프로젝트의 규모와 요구사항에 따라 적절한 도구를 선택할 수 있다.

### Zustand

출처: https://github.com/pmndrs/zustand

Zustand는 단순화된 Flux 원칙을 사용하는 작고 빠르며 확장 가능한 상태 관리 솔루션이다. Zustand는 Flux의 복잡한 디스패처(dispatcher) 개념을 제거하고, 상태와 이를 업데이트하는 함수를 하나의 중앙 스토어(Store)에 통합한다. 훅 기반의 편리한 API를 제공하며 보일러플레이트가 적고 독단적이지 않다는 특징이 있다. useStore와 같은 훅을 통해 상태를 쉽게 구독하고 업데이트할 수 있어 React 개발에 자연스럽게 어울린다. 또한 Zustand는 특정 상태만 구독하는 기능을 제공하여 불필요한 리랜더링을 방지한다. 
> (👨🏻‍🏫 : "제가 적은 글인데! Zustand도 막 쓰면 성능이 전혀 좋아지질 않으니! 잘 알고서 사용하셔야해요! 
https://velog.io/@kyujenius/Zustand%EC%9D%98-%EC%96%95%EC%9D%80-%EB%B9%84%EA%B5%90%EB%8A%94-%EC%96%95%EC%9D%80-%EB%B9%84%EA%B5%90%EA%B0%80-%EC%95%84%EB%8B%88%EB%8B%A4")


```jsx

import { create } from 'zustand'

const useBearStore = create((set) => ({
  bears: 0,
  increasePopulation: () => set((state) => ({ bears: state.bears + 1 })),
  removeAllBears: () => set({ bears: 0 }),
}))

function BearCounter() {
  const bears = useBearStore((state) => state.bears)
  return <h1>{bears} around here ...</h1>
}

function Controls() {
  const increasePopulation = useBearStore((state) => state.increasePopulation)
  return <button onClick={increasePopulation}>one up</button>
}
```

Zustand는 Redux와 달리 단순하고 독단적이지 않으며, 컴포넌트를 Context Provider로 감싸지 않아도 된다. 또한 Context API와 달리 상태 변경 시 불필요한 리렌더링이 발생하지 않고 변경된 상태만 업데이트된다. 중앙 집중식 단일 스토어 구조를 활용하면서도 상태를 정의하고 사용하는 방법이 단순하다.

Zustand는 Flux 패턴을 따르면서도 보일러플레이트 코드를 최소화하여 개발자 경험을 향상시킨다. 특히 작은 규모의 프로젝트나 빠른 개발이 필요한 경우에 Redux의 좋은 대안이 될 수 있다.

> (👨🏻‍🏫 : "상태 관리 라이브러리들은 마치 요리 도구 같아요. 안성재 셰프가 운영하는 ‘모수’ 라는 식당에서는 대형 믹서기(Redux)가 필요할 수 있지만, 작은 카페에서는 핸드 믹서(useReducer)로도 충분할 수 있죠. 제 집에서는 그 마저도 필요 없을 수도 있어요! ~~제 주먹이 불 주먹이거든요!~~ 중요한 건 여러분의 '요리'에 맞는 도구를 선택하는 거예요! 회사에선 이 능력을 확인하기 위해서 매번 질문하죠. OO씨는 왜 이 기술을 택했나요? 혹은, 포트폴리오에 있는 이 기술을 왜 사용하셨죠? 와 같이 말이죠. 결국엔 필요한 요구사항에 맞게 잘 선택했고, 잘 이해하고 있나가 포인트입니다.")
> 

## 6. 결론

Flux 패턴은 React 애플리케이션의 상태 관리에 혁명을 일으켰다. MVC의 양방향 데이터 흐름에서, 단방향 데이터 흐름으로 처음엔 복잡했으나, 점차 단순해지며 이 아이디어는 현대 웹 개발의 기초가 되었다. 비록 Redux, MobX, Recoil, Zustand 등 다양한 대안들이 등장했지만, 이들 모두 결국엔 Flux의 핵심 원칙을 기반으로 하고 있다. 

Flux를 이해하는 것은 단순히 하나의 패턴을 배우는 것 이상의 의미가 있다. 이는 복잡한 UI 애플리케이션의 상태를 효과적으로 관리하는 방법에 대한 통찰을 제공한다. 따라서 React 개발자라면 Flux 패턴을 깊이 이해하고, 이를 바탕으로 다양한 상태 관리 솔루션을 적재적소에 활용할 수 있어야 한다.

> 🙇🏻 글 내에 틀린 점, 오탈자, 비판, 공감 등 모두 적어주셔도 됩니다. 감사합니다..! 🙇🏻
>
