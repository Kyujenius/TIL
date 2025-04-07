## 리액트에서 순수성을 유지하기 위한 부수 효과 관리법 (feat. 멱등성)

![](https://velog.velcdn.com/images/kyujenius/post/381578f1-30e2-4a4f-8b9e-f88d3feee896/image.png)

리액트 개발을 하다 보면 **순수성(Purity)**이라는 개념과 자주 마주치게 된다. 함수형 프로그래밍의 핵심 원칙인 순수성은 리액트의 기본 철학이기도 하다. 하지만 실제 애플리케이션을 개발할 때는 네트워크 요청, DOM 조작, 로컬 스토리지 접근 등 다양한 **부수 효과(Side Effects)**를 다뤄야 한다.

> (👨🏻‍🏫 : 근데 저는 처음에 이 개념을 이해하는 데 꽤 애를 먹었답니다. UI만을 나타내는 컴포넌트라면 괜찮지만, **"순수하게 개발하라고? 그럼 실제 데이터는 어떻게 가져오나?" 싶은** 의문이 가장 먼저 들었거든요!)
> 

오늘은 리액트에서 **순수성을 유지**하면서도 **부수 효과를 효과적으로 관리**하는 방법에 대해 알아보자. 이 글을 통해 리액트의 선언적 패러다임을 더 잘 이해하고, 예측 가능하고 유지보수하기 쉬운 코드를 작성하는 데 도움이 되길 바란다.

## 💡급하신 분들을 위해서 결론 먼저!

1. 컴포넌트와 Hook은 항상 **멱등성**을 유지해야 한다.
2. 부수 효과는 반드시 **렌더링 외부**에서 처리한다. **(return 문 밖에서)**
3. 컴포넌트 내부의 **지역 변경**은 허용된다.
4. **Props는 불변**으로 취급하고, **state는 setter 함수**로만 변경한다.
5. Hook의 **반환값과 인수**는 불변이다.
6. JSX로 전달된 값은 **불변**으로 취급한다.

---

## 1. 모든 컴포넌트와 Hook은 멱등해야 한다

### 멱등성이란 무엇인가?

**멱등성(Idempotence)**이란 동일한 입력에 대해 항상 동일한 출력을 반환하는 특성을 말한다. 리액트에서는 컴포넌트와 Hook이 동일한 props와 state에 대해 항상 동일한 JSX와 동작을 보여야 한다는 의미다.

```jsx
// 멱등성을 유지하는 좋은 예
function ProfileCard({ name, age }) {
  return (
    <div className="card">
      <h2>{name}</h2>
      <p>나이: {age}</p>
    </div>
  );
}

// 멱등성을 위반하는 나쁜 예
function RandomProfileCard({ name, age }) {
  // 매 렌더링마다 다른 결과 생성
  const randomColor = ['red', 'blue', 'green'][Math.floor(Math.random() * 3)];
  
  return (
    <div className="card" style={{ backgroundColor: randomColor }}>
      <h2>{name}</h2>
      <p>나이: {age}</p>
    </div>
  );
}

```

> (👨🏻‍🏫 : 랜덤 값을 사용하고 싶다면 어떻게 해야 할까요? 그럴 땐 useState와 useEffect를 활용해 컴포넌트가 처음 마운트될 때만 랜덤 값을 생성하면 됩니다!)
> 

---

## 2. 사이드 이펙트는 렌더링 외부에서 실행되어야 한다

### 이벤트 핸들러를 활용한 부수 효과 관리

**이벤트 핸들러**는 렌더링 과정 외부에서 실행되므로, 부수 효과를 처리하기에 적합한 장소다.

```jsx
function SaveButton({ data }) {
  // 좋은 예: 이벤트 핸들러 내에서 부수 효과 처리
  const handleSave = () => {
    localStorage.setItem('savedData', JSON.stringify(data));
    alert('저장되었습니다!');
  };

  return <button onClick={handleSave}>저장</button>;
}

```

### useEffect를 활용한 부수 효과 관리

**useEffect**는 렌더링 이후에 실행되므로, 렌더링을 방해하지 않고 부수 효과를 처리할 수 있다.

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // 컴포넌트 마운트 또는 userId 변경 시 데이터 가져오기
    async function fetchUser() {
      setLoading(true);
      try {
        const response = await fetch(`/api/users/${userId}`);
        const userData = await response.json();
        setUser(userData);
      } catch (error) {
        console.error('사용자 정보를 가져오는 데 실패했습니다', error);
      } finally {
        setLoading(false);
      }
    }
    
    fetchUser();
  }, [userId]);

  if (loading) return <p>로딩 중...</p>;
  if (!user) return <p>사용자를 찾을 수 없습니다</p>;
  
  return (
    <div>
      <h2>{user.name}</h2>
      <p>이메일: {user.email}</p>
    </div>
  );
}

```

> (👨🏻‍🏫 : useEffect의 의존성 배열을 잘 관리하는 것이 중요합니다! 빈 배열이면 마운트 시에만, 값이 있으면 해당 값이 변경될 때마다 실행됩니다.)
> 

---

## 3. 지역 변경에 한해서는 허용한다

### 렌더링 중 지역 변수 사용하기

컴포넌트 함수 내부에서 생성되고 외부로 노출되지 않는 **지역 변수**는 자유롭게 변경해도 된다.

```jsx
function FilteredList({ items }) {
  // 지역 변수 사용 - 허용됨
  let filteredItems = [];
  
  for (const item of items) {
    if (item.active) {
      filteredItems.push(item);
    }
  }
  
  return (
    <ul>
      {filteredItems.map(item => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
}

```

### 메모이제이션을 활용한 성능 최적화

**useMemo**와 **useCallback**을 사용하면 지역 계산 결과를 캐싱하여 불필요한 재계산을 방지할 수 있다.

```jsx
function ExpensiveCalculation({ data }) {
  // 비용이 많이 드는 계산 결과를 메모이제이션
  const processedData = useMemo(() => {
    console.log('비용이 많이 드는 계산 실행');
    return data.map(item => item * 2).filter(item => item > 10);
  }, [data]);
  
  return (
    <div>
      <h2>처리된 데이터</h2>
      <ul>
        {processedData.map((item, index) => (
          <li key={index}>{item}</li>
        ))}
      </ul>
    </div>
  );
}

```

---

## 4. 받아온 Props은 불변해야하며, state는 Setter를 이용해서 변경해야한다

### Props의 불변성 유지하기

**Props**는 상위 컴포넌트에서 전달받은 데이터로, 절대 직접 수정해서는 안 된다.

```jsx
// 나쁜 예: props 직접 수정
function BadComponent({ user }) {
  // 🚫 절대 하지 말아야 할 일
  user.name = '수정된 이름';
  
  return <div>{user.name}</div>;
}

// 좋은 예: props를 기반으로 새 객체 생성
function GoodComponent({ user }) {
  const updatedUser = { ...user, displayName: `${user.name}님` };
  
  return <div>{updatedUser.displayName}</div>;
}

```

### State 올바르게 업데이트하기

**State**는 반드시 제공된 **setter 함수**를 통해서만 업데이트해야 한다.

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  
  // 나쁜 예: state 직접 수정
  const badIncrement = () => {
    // 🚫 작동하지 않음
    count = count + 1;
  };
  
  // 좋은 예: setter 함수 사용
  const goodIncrement = () => {
    setCount(count + 1);
  };
  
  // 더 좋은 예: 함수형 업데이트 사용
  const bestIncrement = () => {
    setCount(prevCount => prevCount + 1);
  };
  
  return (
    <div>
      <p>카운트: {count}</p>
      <button onClick={goodIncrement}>증가</button>
      <button onClick={bestIncrement}>안전하게 증가</button>
    </div>
  );
}

```

> (👨🏻‍🏫 : 함수형 업데이트를 사용하면 이전 상태를 기반으로 안전하게 업데이트할 수 있어요. 특히 여러 상태 업데이트가 연속으로 일어날 때 유용합니다!)
> 

---

## 5. Hook의 반환값과 인수는 불변입니다

### Hook 반환값의 불변성

Hook이 반환하는 값은 직접 수정하지 말고, 항상 제공된 API를 통해 조작해야 한다.

```jsx
function UserForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: ''
  });
  
  // 나쁜 예: 반환값 직접 수정
  const badHandleChange = (e) => {
    // 🚫 작동하지 않음
    formData.name = e.target.value;
  };
  
  // 좋은 예: setter 함수와 새 객체 사용
  const goodHandleChange = (e) => {
    setFormData({
      ...formData,
      [e.target.name]: e.target.value
    });
  };
  
  return (
    <form>
      <input
        name="name"
        value={formData.name}
        onChange={goodHandleChange}
      />
      <input
        name="email"
        value={formData.email}
        onChange={goodHandleChange}
      />
    </form>
  );
}

```

### Hook 인수의 불변성

Hook에 전달하는 인수도 불변으로 취급해야 한다.

```jsx
function SearchComponent() {
  const [searchParams, setSearchParams] = useState({
    query: '',
    filters: { category: 'all', sortBy: 'relevance' }
  });
  
  // 좋은 예: 중첩 객체도 불변성 유지
  const updateFilter = (filterName, value) => {
    setSearchParams(prev => ({
      ...prev,
      filters: {
        ...prev.filters,
        [filterName]: value
      }
    }));
  };
  
  return (
    <div>
      <input
        value={searchParams.query}
        onChange={e => setSearchParams({
          ...searchParams,
          query: e.target.value
        })}
      />
      <select
        value={searchParams.filters.category}
        onChange={e => updateFilter('category', e.target.value)}
      >
        <option value="all">전체</option>
        <option value="books">도서</option>
        <option value="electronics">전자기기</option>
      </select>
    </div>
  );
}

```

---

## 6. JSX로 전달된 값은 불변이다

### JSX 속성의 불변성

JSX에 전달하는 모든 값은 불변으로 취급되어야 한다.

```jsx
function ProductCard({ product, onAddToCart }) {
  // 좋은 예: 이벤트 핸들러에서 새 객체 생성
  const handleAddToCart = () => {
    const productWithQuantity = {
      ...product,
      quantity: 1
    };
    onAddToCart(productWithQuantity);
  };
  
  return (
    <div className="product-card">
      <h3>{product.name}</h3>
      <p>${product.price}</p>
      <button onClick={handleAddToCart}>장바구니에 추가</button>
    </div>
  );
}

```

### 자식 컴포넌트로 전달되는 함수의 안정성

자식 컴포넌트에 전달하는 함수는 **useCallback**을 사용하여 안정성을 보장하는 것이 좋다.

```jsx
function ParentComponent() {
  const [items, setItems] = useState([]);
  
  // useCallback으로 함수 메모이제이션
  const addItem = useCallback((newItem) => {
    setItems(prevItems => [...prevItems, newItem]);
  }, []);
  
  return (
    <div>
      <h2>아이템 목록</h2>
      <ItemList items={items} />
      <AddItemForm onAddItem={addItem} />
    </div>
  );
}

```

> (👨🏻‍🏫 : useCallback을 사용하면 불필요한 자식 컴포넌트 리렌더링을 방지할 수 있어요. 특히 상황에 따라서, React.memo와 함께 사용하면 더 효과적입니다!)
> 

---

지금까지 리액트에서 **순수성을 유지**하면서 **부수 효과를 관리**하는 방법에 대해 알아보았다. 이러한 원칙을 지키면 컴포넌트의 예측 가능성이 높아지고, 디버깅이 쉬워지며, 성능 최적화도 용이해진다.

> (👨🏻‍🏫 : 리액트에서 항상 훅을 통해서 데이터를 관리하는 방식에 대해 근본적인 의문이 조금 해소됐나요? 리액트의 선언적 패러다임을 따르는 것은 처음에는 어색할 수 있지만, 그 근간에는 이런 시간이 지날수록 코드의 품질과 유지보수성이 크게 향상되는 것을 경험할 수 있을 것이예요! 특히 팀 프로젝트에서는 이러한 원칙을 따르는 것이 협업의 효율성을 높이는 데 큰 도움이 됩니다.)
> 

> 🙇🏻 글 내에 틀린 점, 오탈자, 비판, 공감 등 모두 적어주셔도 됩니다. 감사합니다..! 🙇🏻
>
