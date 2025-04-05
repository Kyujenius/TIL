## React에서의 순수성: 예측 가능한 UI의 비밀
![](https://velog.velcdn.com/images/kyujenius/post/724613bb-985e-47b0-a99d-2d64688dd8fa/image.png)

리액트 세계에서 순수함수는 뗄래야 뗄 수가 없다. 항상 같은 상황에서 같은 반응을 보여준다는 확신은 곧 디버깅과 유지 보수성을 이끈다. 이런 순수함수의 특성이 리액트의 핵심 철학이 된 이유는 무엇일까? 복잡한 UI 개발 환경에서 순수함수는 어떻게 빛을 발할까?

> "(👨🏻‍🏫 : 리액트 공식문서 내에서도 항상 순수성에 대한 중요성을 강조한답니다. 그 이유에 대해서 같이 알아볼까요?)"
> 

리액트 공식 문서에서는 다음과 같이 말한다:

**“Keeping Components Pure”**

![](https://velog.velcdn.com/images/kyujenius/post/ea6aebbd-1bf9-4f33-8ae3-9e3c713cd021/image.png)

출처: [React 공식 문서 - 컴포넌트 순수성 유지하기](https://react.dev/learn/keeping-components-pure)

리액트에서 정의하는 순수성은 전통적인 함수형 프로그래밍에서의 순수 함수 본질은 같으나 세부적인 개념은 약간 다릅니다. 리액트에서는 컴포넌트가 다음 조건을 만족하면 순수하다고 본다:

1. 렌더링 과정에서 사이드 이펙트가 없어야 함 - 외부 변수를 수정하거나 DOM을 직접 조작하지 않음
2. 동일한 입력(props, state, context)에 대해 항상 동일한 JSX 결과를 반환해야 함

주로 참고한 공식 문서: https://react.dev/learn/keeping-components-pure

## 💡급하신 분들을 위해서 결론 먼저!

1. 리액트 컴포넌트는 순수함수처럼 동작할 때 예측 가능하고 디버깅이 용이하다.
2. 리액트의 렌더링 프로세스는 순수성을 기반으로 최적화되어 있다.
3. 순수 컴포넌트는 애플리케이션의 안정성과 성능을 크게 향상시킨다.

---

## 1. 리액트 컴포넌트가 순수해야 하는 이유

### 순수한 컴포넌트

리액트 컴포넌트가 **순수함수처럼 동작한다면, 같은 props와 state에 대해 항상 같은 UI를 렌더링**한다. 이는 UI의 **예측 가능성**을 크게 높인다.

```jsx
function Recipe({ drinkers }) {
  return (
    <ol>    
      <li>Boil {drinkers} cups of water.</li>
      <li>Add {drinkers} spoons of tea and {0.5 * drinkers} spoons of spice.</li>
      <li>Add {0.5 * drinkers} cups of milk to boil and sugar to taste.</li>
    </ol>
  );
}

export default function App() {
  return (
    <section>
      <h1>Spiced Chai Recipe</h1>
      <h2>For two</h2>
      <Recipe drinkers={2} />
      <h2>For a gathering</h2>
      <Recipe drinkers={4} />
    </section>
  );
}

```

> drinkers에 따라 Input이 같다면 출력되는 Ouput인 컴포넌트가 동일하다.
> 
> 
![](https://velog.velcdn.com/images/kyujenius/post/570f9d74-bd56-4578-8783-6df401158576/image.png)
> 

### 테스트 용이성

순수 컴포넌트는 외부 상태에 의존하지 않기 때문에 테스트하기 매우 쉽다. 특정 props를 전달하고 예상되는 출력을 확인하는 것만으로 충분하다.

### 디버깅 간소화

컴포넌트가 순수하다면, 버그가 발생했을 때 입력(props와 state)만 확인하면 된다. 외부 요인을 고려할 필요가 없어 디버깅 과정이 크게 단순화된다.

> "(👨🏻‍🏫 : 디버깅이 쉬워진다는 것만으로도 순수함수는 사랑받을 자격이 있답니다! 밤새 버그 찾느라 고생해보신 분들은 공감하실 거예요. 😅)"
> 

### 비순수 컴포넌트

```jsx
let guest = 0;

function Cup() {
  // Bad: changing a preexisting variable!
  guest = guest + 1;
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <>
      <Cup />
      <Cup />
      <Cup />
    </>
  );
}
```

>![](https://velog.velcdn.com/images/kyujenius/post/c509082e-6548-42cf-ac0d-f4896be5e10e/image.png)
이를 랜더링 하면 다음과 같이 나온다. 같은 `<Cup/>` 이라는 컴포넌트를 랜더링해도, 컴포넌트는 다르게 나온다. 

> "(👨🏻‍🏫 : 변수도 매번 바뀌니까 이게 순수함수인가? 라는 헷갈린 점들이 조금은 해소 되셨나요? 예측 가능한 컴포넌트와 예측 불가능한 컴포넌트에 대해서는 동일한 prop 과 state를 넣었는데 동일한 Output이 나오나? 로 확인해볼 수 있습니다. )"
> 

---

## 2. 리액트의 렌더링 프로세스와 순수성의 관계

### 렌더 단계의 순수성

리액트의 렌더링 프로세스는 크게 **렌더 단계**와 **커밋 단계**로 나뉜다. 렌더 단계에서 리액트는 컴포넌트를 순수 함수처럼 취급하며, 이전 렌더링과 결과를 비교한다. 이 때, `Strict Mode` 를 설정해두었다면, 리액트의 컴포넌트들이 순수함수임이 보장되어, 이 과정이 매끄럽게 진행된다.

### 렌더링 최적화

컴포넌트가 순수하다면, 리액트는 불필요한 렌더링을 건너뛸 수 있다. 같은 입력에 대해 항상 같은 출력이 나온다는 것을 알기 때문에, 입력이 변경되지 않았다면 **이전 결과를 재사용할 수 있다.**

React.memo를 사용한 최적화 예시는 다음과 같습니다:

```jsx
// 데이터 테이블의 행 컴포넌트 최적화
const DataRow = React.memo(
  function DataRow({ data }) {
    console.log("Row rendering");
    return <tr><td>{data.id}</td><td>{data.value}</td></tr>;
  });

// 사용자 목록 컴포넌트 최적화
const UserList = React.memo(
  function UserList({ users, filterCriteria }) {
    const filteredAndSortedUsers = React.useMemo(() => {
      const filteredUsers = users.filter(user => user.age > filterCriteria.minAge);
        return filteredUsers.sort((a, b) => a.age - b.age);
  }, [users, filterCriteria]);
  
  return (
    <div className="user-list">
      {filteredAndSortedUsers.map(user => (
        <div key={user.id}>
          <h4>{user.name}</h4>
          <p>Age: {user.age}</p>
        </div>
      ))}
    </div>
  );
});

// Todo 리스트 컴포넌트 최적화
const Todo = React.memo(function Todo({ list }) {
  console.log("Todo component rendered");
  return (
    <ul>
      {list.map((item) => (
        <TodoItem key={item.id} item={item} />
      ))}
    </ul>
  );
});

```

이러한 예시들은 React.memo를 사용하여 부모 컴포넌트가 리렌더링될 때 props가 변경되지 않은 자식 컴포넌트의 불필요한 리렌더링을 방지합니다. 특히 리스트 렌더링, 데이터 테이블, 필터링된 목록과 같이 데이터를 표시하는 컴포넌트에서 유용합니다.

### 엄격 모드(Strict Mode)와 순수성

리액트의 **엄격 모드**(Strict Mode)는 컴포넌트의 순수성을 검증하는 데 도움을 준다. 개발 모드에서 컴포넌트를 두 번 렌더링하여 부수 효과를 찾아내는 방식이다.

```jsx
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

> "(👨🏻‍🏫 : 엄격 모드는 마치 엄한 부모처럼 여러분의 컴포넌트가 순수한지 철저히 검사한답니다. 덕분에 더 좋은 코드를 작성하게 되죠! 또한 **`React.memo`**는 오해할 수 있어서 말씀드리지만, 순수 컴포넌트를 정의하는 것이 아니라, **이미 순수한 컴포넌트의 성능을 최적화하는** 데 사용됩니다. **`memo`**는 컴포넌트의 props가 변경되지 않았다면 리렌더링을 건너뛰게 해주는 **고차 컴포넌트(HOC)**입니다. **순수 컴포넌트는 `memo` 사용 여부와 관계없이, 동일한 입력(props)에 대해 항상 동일한 출력(JSX)을 반환하고 부수 효과가 없는 컴포넌트를 의미**합니다.)"
> 

---

## 3. 순수 컴포넌트가 애플리케이션 안정성에 미치는 영향

### 예측 가능한 상태 관리

순수 컴포넌트는 상태 변화가 예측 가능하게 이루어진다. 이는 복잡한 애플리케이션에서도 **상태 관리**를 용이하게 만든다.

### 동시성 모드 지원

리액트의 **동시성 모드**(Concurrent Mode)는 순수 컴포넌트를 기반으로 한다. 컴포넌트가 순수하다면, **리액트는 동시성 모드를 사용할 수 있는데, 이는 리액트가 렌더링 작업을 중단, 재개, 심지어 폐기할 수 있게 해주는 기능**입니다. 이는 브라우저의 메인 스레드를 차단하지 않고 백그라운드에서 컴포넌트 트리의 여러 버전을 준비할 수 있게 해줍니다.

### 버그 감소

순수 컴포넌트는 부수 효과가 없기 때문에 예상치 못한 버그가 발생할 가능성이 크게 줄어든다. 이는 전체 애플리케이션의 **안정성**을 높인다.

```jsx
// 비순수 컴포넌트 예시 (피해야 함)
function BadComponent() {
  // 🚫 렌더링 중 직접 DOM 조작
  document.title = 'Updated Page';
  return Hello World;
}

// 순수한 접근 방식
function GoodComponent() {
  // ✅ 부수 효과를 useEffect로 분리 (다 다음 글에서 다뤄볼게요)
  React.useEffect(() => {
    document.title = 'Updated Page';
  }, []);
  return Hello World;
}
```

> "(👨🏻‍🏫 : 순수 컴포넌트는 마치 든든한 팀원과 같아요. 자기 일만 책임지고, 다른 사람의 일을 방해하지 않죠. 그런 팀원이 많을수록 프로젝트는 성공하기 마련이랍니다! 다음에는 `함수형 컴포넌트가 순수성과 왜 더 가까운가?` 에 대해서 배워볼게요)"
> 

---
> 🙇🏻 글 내에 틀린 점, 오탈자, 비판, 공감 등 모두 적어주셔도 됩니다.  감사합니다..! 🙇🏻
> 

