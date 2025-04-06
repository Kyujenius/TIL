![](https://velog.velcdn.com/images/kyujenius/post/962d304c-f0f9-4348-8098-dd55cf9fd2b5/image.png)

## 💡급하신 분들을 위해서 결론 먼저!

1. 클래스형 컴포넌트와 함수형 컴포넌트의 성능을 비교하는 것은 의미가 없다. 그러나 **함수형 컴포넌트는 렌더링된 값을 캡처한다는 점을 미루어보아 관리 측면에서는 더 효율적이다.**
2. 함수형 컴포넌트는 입력(props)에 따른 출력(UI)이 예측 가능해 순수 함수의 특성을 자연스럽게 따른다.
3. 클래스 컴포넌트는 this와 생명주기 메서드로 인해 상태 관리가 복잡하고 사이드 이펙트가 발생하기 쉽다.
4. 함수형 컴포넌트는 클로저를 활용해 렌더링 시점의 값을 캡처하여 일관성을 유지한다.
5. 함수형 컴포넌트는 테스트와 디버깅이 용이하며 코드 최적화에도 유리하다.

순수성(Purity)이라는 개념은 함수형 프로그래밍의 핵심인데, 리액트의 함수형 컴포넌트는 이 순수성을 자연스럽게 유지하도록 설계되어 있다. 오늘은 왜 함수형 컴포넌트가 클래스 컴포넌트보다 순수성을 직관적으로 유지하기 쉬운지 왜 그렇게 표준이 되었는지 알아보자. (순수 함수에 대해서 잘 모르신다면 이전 시리즈를 참고해주세요)

> (👨🏻‍🏫 : 저는 처음에 리엑트를 배울 때 클래스 컴포넌트로 배웠답니다. 군대에서 리액트를 처음 배우던 날, 남들이 **장병개발지원금** 으로 운동화를 살 때, 큰 맘 먹고 산 리엑트 책 속에는 클래스형 컴포넌트 관련 내용으로 꽉차있었고, 열심히 공부했지만 전부 갖다 버리게 된 기억이 있어요…! 그래도 이렇게 쓰이는 날이 오네요 ㅎㅎ 그때는 함수형이 뭔지도 몰랐죠. 오랜만에 그 생각이 나네요. 🥲)

## 1. 순수 함수와 리액트 컴포넌트

### 순수 함수란 무엇인가?

순수 함수(Pure Function)는 다음 두 가지 특성을 가진 함수를 말한다:

1. 동일한 입력에 대해 항상 동일한 출력을 반환한다.
2. 함수 외부의 상태를 변경하지 않는다(사이드 이펙트가 없다. 정확히는 부수효과가 없다).

```jsx
// 순수 함수의 예
function add(a, b) {
  return a + b;
}

// 비순수 함수의 예
let total = 0;
function addToTotal(value) {
  total += value; // 외부 변수 변경 (사이드 이펙트)
  return total;
}
```

이전 시리즈 참고: https://velog.io/@kyujenius/react-pure-component

### 리액트와 순수성의 관계

리액트의 철학은 **UI를 순수 함수처럼 다루는 것**이다. 즉, 같은 props가 주어지면 항상 같은 UI를 렌더링해야 한다. 이것이 리액트의 선언적 프로그래밍 방식의 핵심이다.

> (👨🏻‍🏫 : 리액트 공식 문서에서도 컴포넌트를 '순수하게 유지하라'고 강조한답니다. 그만큼 중요하다는 거죠!)

리액트 공식 문서에서는 다음과 같이 말한다:

**“Keeping Components Pure”**

![](https://velog.velcdn.com/images/kyujenius/post/a41c3e83-7eb8-4418-b0ed-408f5f79ce44/image.png)

출처: [React 공식 문서 - 컴포넌트 순수성 유지하기](https://react.dev/learn/keeping-components-pure)

---

## 2. 클래스 컴포넌트의 복잡성

### this 키워드의 혼란

클래스 컴포넌트에서는 **this** 키워드가 많은 혼란을 야기한다. JavaScript의 this는 호출 컨텍스트에 따라 달라지기 때문에, 이벤트 핸들러에서 this를 올바르게 바인딩하지 않으면 예상치 못한 버그가 발생한다.

```jsx
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
    // 이벤트 핸들러에 this를 바인딩해야 함
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState({ count: this.state.count + 1 });
  }

  render() {
    return (
      <button onClick={this.handleClick}>Count: {this.state.count}</button>
    );
  }
}
```

### 생명주기 메서드의 복잡성

클래스 컴포넌트의 **생명주기 메서드**는 코드를 여러 메서드에 분산시키고, 관련 없는 로직이 한 메서드에 섞이는 문제를 야기한다.

```jsx
class DataFetcher extends React.Component {
  constructor(props) {
    super(props);
    this.state = { data: null, loading: true };
  }

  componentDidMount() {
    // 데이터 가져오기
    fetchData(this.props.id).then((data) => {
      this.setState({ data, loading: false });
    });

    // 이벤트 리스너 등록 (관련 없는 로직)
    window.addEventListener("resize", this.handleResize);
  }

  componentDidUpdate(prevProps) {
    if (prevProps.id !== this.props.id) {
      // id가 변경되면 데이터 다시 가져오기 (중복이죠)
      this.setState({ loading: true });
      fetchData(this.props.id).then((data) => {
        this.setState({ data, loading: false });
      });
    }
  }

  componentWillUnmount() {
    // 이벤트 리스너 제거
    window.removeEventListener("resize", this.handleResize);
  }

  handleResize = () => {
    console.log("Window resized");
  };

  render() {
    const { data, loading } = this.state;
    return loading ? <p>Loading...</p> : <div>{data}</div>;
  }
}
```

이 예제에서 데이터 가져오기 로직이 `componentDidMount`와 `componentDidUpdate`에 중복되어 있고, 완전히 관련 없는 리사이즈 이벤트 처리 로직도 섞여 있다. 이런 구조는 **관심사 분리(Separation of Concerns)** 원칙에 위배된다.

---

## 3. 함수형 컴포넌트와 훅(hook)의 등장

### 함수형 컴포넌트의 단순성

함수형 컴포넌트는 **props를 받아 UI를 반환하는 순수 함수**처럼 작동한다. 이는 **순수 함수의 개념과 자연스럽게 일치**한다.

```jsx
function Greeting(props) {
  return <h1>Hello, {props.name}!</h1>;
}
```

이 간단한 컴포넌트는 props에 따라 예측 가능한 출력을 생성하며, 외부 상태를 변경하지 않는다. 완벽한 순수 함수다! 🌟

### 훅(Hooks)을 통한 상태 관리

React 16.8에서 도입된 **훅(Hooks)**은 함수형 컴포넌트에서도 상태와 생명주기 기능을 사용할 수 있게 해주면서, 로직을 관심사별로 분리할 수 있게 해준다.

```jsx
function DataFetcher({ id }) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // 데이터 가져오기 로직
    setLoading(true);
    fetchData(id).then((result) => {
      setData(result);
      setLoading(false);
    });
  }, [id]); // id가 변경될 때만 실행

  // 리사이즈 이벤트 처리 (사이즈 관련 로직 따로 관심사 분리)
  useEffect(() => {
    const handleResize = () => console.log("Window resized");
    window.addEventListener("resize", handleResize);
    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []); // 마운트/언마운트 시에만 실행

  return loading ? <p>Loading...</p> : <div>{data}</div>;
}
```

출처: [React 공식 문서 - Effect Hook 사용하기](https://react.dev/reference/react/hooks)

클래스 컴포넌트와 비교하면, 함수형 컴포넌트에서는:

1. 하나의 로직 - 하나의 훅으로 그룹화된다.
2. 의존성 배열 [] 로 효과의 실행 시점을 명확하게 제어할 수 있다.
3. 클린업 함수가 효과와 함께 정의되어, 시기와 의존성이 명확하다. (즉, 예측 가능하다.)

> (👨🏻‍🏫 : 훅이 등장했을 때 정말 혁명적이었답니다! 코드가 훨씬 깔끔해졌죠.)

---

## 4. 클로저와 값의 캡처 (어려워도 진짜 중요해요!!)

### 클래스 컴포넌트의 this 문제

클래스 컴포넌트에서는 `this.props`와 `this.state`가 항상 최신 값을 참조하기 때문에, 비동기 작업에서 예상치 못한 버그가 발생할 수 있다.

```jsx
class ProfilePage extends React.Component {
  showMessage = () => {
    // 3초 후에 메시지 표시
    setTimeout(() => {
      alert("You followed " + this.props.user);
    }, 3000);
  };

  render() {
    return <button onClick={this.showMessage}>Follow</button>;
  }
}
```

만약 사용자가 버튼을 클릭한 후 다른 프로필로 이동하면, `this.props.user`는 새 사용자를 참조하게 되어 원래 의도한 사용자가 아닌 다른 사용자 이름이 표시된다.

### 함수형 컴포넌트의 값 캡처

함수형 컴포넌트는 **클로저**를 통해 렌더링 시점의 props 값을 캡처한다. 이는 더 예측 가능한 동작을 제공한다.

```jsx
function ProfilePage({ user }) {
  const showMessage = () => {
    // 렌더링 시점의 user 값이 '캡처'됨
    setTimeout(() => {
      alert("You followed " + user);
    }, 3000);
  };

  return <button onClick={showMessage}>Follow</button>;
}
```

출처: [Function 컴포넌트와 Class Components의 차이점](https://overreacted.io/how-are-function-components-different-from-classes/)

이 컴포넌트는 버튼을 표시하고, **`setTimeout`**으로 네트워크 요청을 시뮬레이션한 다음 확인 알림을 표시합니다. 예를 들어 **`props.user`**가 'Dan'이면 3초 후에 'Followed Dan'을 표시합니다. 간단하죠? 이 예제에서는 버튼을 클릭한 시점의 `user` 값이 **클로저에 의해 캡처**되어, 나중에 다른 프로필로 이동하더라도 원래 의도한 사용자 이름이 표시된다. 이는 **순수 함수의 특성**과 일치하는 예측 가능한 동작이다.

### **차이점 이해하기**

두 버튼으로 다음 동작 시퀀스를 시도해보면 순서는 이렇다

1. Follow 버튼 중 하나를 **클릭한다**.
2. 3초가 지나기 전에 선택된 프로필을 **변경한다**.
3. 알림 텍스트를 **읽는다**.

이상한 차이점을 발견할 수 있습니다:

- **함수형** **`ProfilePage`**에서는 Dan의 프로필에서 Follow를 클릭한 다음 Sophie로 이동해도 여전히 'Followed Dan'이라고 알림이 표시됩니다.
- **클래스형** **`ProfilePage`**에서는 'Followed Sophie'라고 알림이 표시됩니다.

### 왜 이런 차이가 발생할까?

클래스의 `showMessage` 메서드를 자세히 살펴보자.

```jsx
class ProfilePage extends React.Component {
  showMessage = () => {
    alert('Followed ' + this.props.user);
  };

```

이 클래스 메서드는 `this.props.user`에서 읽는다. React에서 props는 불변이므로 변경될 수 없다.

**그러나 `this`는 항상 변경 가능하다.** 사실, 이것이 클래스에서 `this`의 주요한 목적이다. React는 시간이 지남에 따라 `this`를 변경하여 `render` 및 라이프 사이클내에서 최신 값을 읽을 수 있도록 한다. 따라서 요청이 진행 중인 동안 컴포넌트가 다시 렌더링되면 `this` 자체가 바뀌어`this.props`가 변경된다. `showMessage` 메서드는 ‘완전히 새로운’ props에서 `user`를 읽게 된다.

> (👨🏻‍🏫 : 이것은 UI의 본질에 대한 흥미로운 결과를 보여줍니다. **UI는 = 현재 애플리케이션 상태의 함수**라고 말한다면, **이벤트 핸들러는 렌더링 결과의 일부인데요?** 이벤트 핸들러는 props와 state에 의한 특정 렌더링에 속한다고도 볼 수 있습니다! )

이해를 위한 시각화

```jsx
UI = f(props) {
 props 와 state 사용해서 랜더링 ()
 이벤트 핸들러에 의한 변경으로 다시 props와 state를 사용해서 랜더링()
}
Event Handler => 이벤트 핸들러가 생성된 시점의 props와 state 값과 연결되어 있어야 함.
```

즉, 특정 시점의 애플리케이션 상태(state와 props)가 주어지면, React는 그에 해당하는 UI를 렌더링한다. 상태가 변경되면 UI도 그에 맞게 업데이트된다. 그러나 `this.props`를 읽는 `setTimeout` 콜백을 예약하면 그 연결이 끊어진다. `showMessage` 콜백은 특정 렌더링에 연결되지 않으므로 **올바른 props를 잃어버린다.** `this`에서 읽으면 그 연결이 끊어진다.

### 함수형 컴포넌트의 해결책

함수형 컴포넌트는 이 문제를 어떻게 해결할까? 다시 함수형으로 구현한 코드를 살펴보자:

```jsx
function ProfilePage(props) {
  const showMessage = () => {
    alert('Followed ' + props.user);
  };
```

함수형 컴포넌트에는 `this`가 없다. **함수의 props는 React에 의해 변경되지 않고 항상 해당 렌더링과 연결된 값을 유지한다.** 이것은 함수 내부의 모든 코드(이벤트 핸들러 포함)가 특정 렌더링에서의 props와 state를 ‘볼 수 있음’을 의미한다.

### 클로저의 역할

JavaScript 클로저는 이 문제를 해결하는 데 도움이 된다. 클로저는 종종 시간이 지남에 따라 변경될 수 있는 값을 생각하기 어렵기 때문에 피하는 경우가 많다. 하지만 React에서 props와 state는 불변이다! 이것은 클로저의 주요 단점을 제거한다. 특정 렌더링에서 props나 state를 클로저로 감싸면, 항상 동일하게 유지된다고 확신할 수 있다:

```jsx
function ProfilePage(props) {
  // props는 렌더링 시점에 캡처됨!
  const showMessage = () => {
    alert('Followed ' + props.user);
  };

```

**렌더링 시 props를 "캡처"했기 때문에** 해당 내부의 모든 코드(showMessage 포함)는 특정 렌더링의 props를 볼 수 있습니다. React는 더 이상 우리의 예상을 벗어나지 않는다.

### 클래스에서도 이 문제를 해결할 수 있을까?

물론이다! 클래스 컴포넌트에서도 클로저를 활용할 수 있다:

```jsx
class ProfilePage extends React.Component {
  render() {
    // props를 캡처!
    const props = this.props;

    // 주의: 우리는 render 내부에 있습니다.
    // 지금 클래스 메서드가 아닙니다.
    const showMessage = () => {
      alert("Followed " + props.user);
    };

    const handleClick = () => {
      setTimeout(showMessage, 3000);
    };

    return <button onClick={handleClick}>Follow</button>;
  }
}
```

이렇게 하면 특정 렌더링의 props를 "캡처"하여 모든 코드가 해당 props를 볼 수 있도록 할 수 있다. 하지만 이 접근 방식은 render() 코드를 항상 써야하고, 그렇다면 매번 동일하게 사용해야하지만, 중복된 코드가 많아지게 된다. 따라서 클래스라는 ‘껍데기’ 를 제거하여 코드를 다음과 같이 단순화할 수 있다:

```jsx
function ProfilePage(props) {
  const showMessage = () => {
    alert("Followed " + props.user);
  };

  const handleClick = () => {
    setTimeout(showMessage, 3000);
  };

  return <button onClick={handleClick}>Follow</button>;
}
```

### 함수형 컴포넌트와 Hooks

Hooks를 사용하면 state에도 동일한 원칙이 적용된다:

```jsx
function MessageThread() {
  const [message, setMessage] = useState("");

  const showMessage = () => {
    alert("You said: " + message);
  };

  const handleSendClick = () => {
    setTimeout(showMessage, 3000);
  };

  const handleMessageChange = (e) => {
    setMessage(e.target.value);
  };

  return (
    <>
      <input value={message} onChange={handleMessageChange} />
      <button onClick={handleSendClick}>Send</button>
    </>
  );
}
```

이 함수형 컴포넌트의 `message`는 "Send" 버튼을 클릭했을 때 input에 있던 상태를 캡처한다.

### 항상 최신 값이 필요한 경우

때로는 특정 렌더링에 속하지 않는 최신 props나 state를 읽어야 할 수도 있다. 이런 경우엔 어떻게 할까?? 정답은 바로 `ref` 이다.

```jsx
function MessageThread() {
  const [message, setMessage] = useState('');
  const latestMessage = useRef('');

  const showMessage = () => {
    alert('You said: ' + latestMessage.current);
  };

  const handleSendClick = () => {
    setTimeout(showMessage, 3000);
  };

  const handleMessageChange = (e) => {
    setMessage(e.target.value);
    latestMessage.current = e.target.value;
  };

```

`ref`는 클래스의 인스턴스 필드와 유사한 역할을 한다. 이것은 변경 가능한 명령형 프로그래밍으로의 탈출구인 셈이다.

> (👨🏻‍🏫 : “클래스 컴포넌트의 단점인 this 바인딩으로 인한 라이프 사이클 주기 내의 코드 중복과, Closure와 상태값 캡쳐의 불편함) 을 극복하기 위해 함수형 컴포넌트를 택하고, 클래스 컴포넌트에서의 이점을 남기기 위해서 ref 를 통해 인스턴스 필드와 같은 역할을 따로 빼두어 남겨두었다” 정도로 한 줄 요약하면 깔끔하지 않을까요? )

## 5. 함수형 컴포넌트의 실용적 이점

### 테스트 용이성

함수형 컴포넌트는 입력(props)에 따른 출력(UI)이 예측 가능하므로 **테스트하기 쉽다**. 복잡한 생명주기 메서드나 내부 상태에 의존하지 않기 때문에 단위 테스트가 간단해진다.

```jsx
// 함수형 컴포넌트 테스트
test("Greeting displays correct name", () => {
  const { getByText } = render(<Greeting name="John" />);
  expect(getByText("Hello, John!")).toBeInTheDocument();
});
```

출처: [React Testing Library 문서](https://testing-library.com/docs/react-testing-library/intro/)

## 결론

함수형 컴포넌트는 리액트의 **선언적 프로그래밍 모델**과 **순수 함수의 개념**을 자연스럽게 결합한다. 이를 통해 코드는 더 예측 가능하고, 테스트하기 쉬우며, 유지보수가 용이해진다.

리액트의 미래는 함수형 프로그래밍의 원칙을 더욱 깊이 받아들이는 방향으로 나아가고 있다. 순수성을 유지하는 것은 단순히 코드 스타일의 문제가 아니라, 버그를 줄이고 코드 품질을 높이는 핵심 요소이다. 함수형 컴포넌트는 이러한 순수성을 자연스럽게 장려하며, 더 나은 리액트 애플리케이션을 만들 수 있게 도와준다.

> (👨🏻‍🏫 : 클래스 컴포넌트 형식 또한 여전히 유효한 방식이지만, 함수형 컴포넌트는 순수성을 직관적으로 유지하기 쉽다는 점에서 현대 리액트 개발의 주류가 되었답니다!. 왜 함수를 쓰듯이 컴포넌트를 제작하게 되었는지, 그 근간에는 순수 함수의 개념이 필수적이예요! 그 근본적인 전후 사정에 대해서 알고보면 이렇게 조금 이해가 수월해졌길 바랍니다 ㅎㅎ)

> 🙇🏻 글 내에 틀린 점, 오탈자, 비판, 공감 등 모두 적어주셔도 됩니다. 감사합니다..! 🙇🏻
