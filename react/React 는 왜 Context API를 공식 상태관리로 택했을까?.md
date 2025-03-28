![](https://velog.velcdn.com/images/kyujenius/post/7f97ac05-4b00-4680-9a20-5b0e4a342a07/image.png)

"이 props는 어디서 왔을까?" 🤔 React 개발자라면 누구나 한 번쯤 겪는 혼란이다. 컴포넌트가 깊어질수록 props를 전달하는 과정은 계속 뚫고 내려간다는 의미에서 prop drilling 이라는 문제점이라고 정의하기도 한다. 이런 고통을 해결하기 위해 React는 **Context API**를 공식적으로 채택했다. 단순히 외부 라이브러리를 추천하는 것이 아니라, 프레임워크 자체에 상태 관리 솔루션을 내장한 것이다. 왜 React 팀은 수많은 상태 관리 방법 중 Context API를 내세웠을까?

> (👨🏻‍🏫 : "제가 React를 처음 배울 때는 props drilling이 너무 고통스러워서 Redux부터 배웠답니다. Flux 단방향 패턴과 가장 비슷한 구조로 한 상태관리인데, Zustand 와 같이 간소화 한 상태관리도 있는 걸 알고 바꿔봤으나, 작은 사이즈의 앱에선 굳이 다른 걸 쓰지 않고 context API 정도로도 충분하다는 걸 알았습니다. ")
> 

## 💡급하신 분들을 위해서 결론 먼저!

1. Context API는 React의 컴포넌트 구조와 자연스럽게 통합되어 추가 라이브러리 없이 상태 관리가 가능하다.
2. 간결한 API로 학습 곡선이 낮고 보일러플레이트 코드가 적어 개발자 경험이 우수하다.
3. 작은 규모의 애플리케이션이나 특정 영역의 상태 관리에 최적화되어 있다.
4. React의 철학인 선언적 프로그래밍과 컴포넌트 기반 아키텍처에 완벽하게 부합한다.
5. 다른 상태 관리 라이브러리와 함께 사용할 수 있는 유연성을 제공한다.

## 1. Context API의 탄생 배경

React가 처음 등장했을 때, 상태 관리는 주로 컴포넌트의 로컬 상태와 props를 통한 데이터 전달에 의존했다. 그러나 애플리케이션이 복잡해지면서 이러한 방식의 한계가 분명해졌다.

### Props Drilling의 문제

**Props drilling**은 상위 컴포넌트에서 하위 컴포넌트로 데이터를 전달하기 위해 중간에 있는 여러 컴포넌트를 통과해야 하는 현상을 말한다. 이는 코드의 가독성을 떨어뜨리고 유지보수를 어렵게 만든다.

```jsx
*// Props drilling 예시*
function App() {
  const [theme, setTheme] = useState('light');
  
  return (
    <Header theme={theme} />
  );
}

function Header({ theme }) {
  return (
    <nav>
      <Logo />
      <UserMenu theme={theme} />
    </nav>
  );
}

function UserMenu({ theme }) {
  return (
    <div className={`menu ${theme}`}>
      <Avatar theme={theme} />
    </div>
  );
}

function Avatar({ theme }) {
  return <img className={`avatar ${theme}`} />;
}
```

이 예시에서 `theme` 상태는 `App`에서 생성되어 `Header`, `UserMenu`를 거쳐 `Avatar`까지 전달된다. 중간의 `Header`와 `UserMenu`는 실제로 `theme`를 사용하지 않고 단순히 전달만 하고 있다.

### 외부 상태 관리 라이브러리의 등장

이러한 문제를 해결하기 위해 **Redux**, **MobX** 등의 외부 상태 관리 라이브러리가 등장했다. 이들은 전역 상태 관리를 효과적으로 처리할 수 있었지만, 추가적인 의존성과 복잡한 설정이 필요했다.

### Context API의 도입

React 팀은 이러한 상황을 인식하고 React 16.3에서 새로운 **Context API**를 공식적으로 도입했다. 이는 외부 라이브러리 없이도 React 내에서 전역 상태를 효과적으로 관리할 수 있는 방법을 제공했다.

> (👨🏻‍🏫 : "Context API가 처음 나왔을 때 '드디어 Redux 없이도 상태 관리가 가능하구나!'라고 생각했어요. 물론 각각의 도구는 서로 다른 용도가 있지만, 간단한 상태 관리에는 Context API가 정말 편리하답니다!")
> 

## 2. Context API의 핵심 특징

Context API가 React의 공식 상태 관리 솔루션으로 채택된 이유를 이해하기 위해 그 핵심 특징을 살펴보자.

### 간결한 API 디자인

Context API는 매우 간결하고 직관적인 API를 제공한다. 주요 구성 요소는 단 세 가지다:

1. **React.createContext()**: Context 객체를 생성한다.
2. **Context.Provider**: 하위 컴포넌트에 Context 값을 제공한다.
3. **useContext()** 훅: 함수형 컴포넌트에서 Context 값을 사용한다.

```jsx
*// Context API 기본 사용법*
import { createContext, useContext, useState } from 'react';

*// 1. Context 생성*
const ThemeContext = createContext('light');

*// 2. Provider 컴포넌트*
function App() {
  const [theme, setTheme] = useState('light');
  
  return (
    <ThemeContext.Provider value={theme}>
      <Header />
      <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
        Toggle Theme
      </button>
    </ThemeContext.Provider>
  );
}

*// 3. Context 사용*
function Header() {
  return <UserMenu />;
}

function UserMenu() {
  return <Avatar />;
}

function Avatar() {
  *// useContext 훅으로 값 사용*
  const theme = useContext(ThemeContext);
  return <img className={`avatar ${theme}`} />;
}
```

이 예시에서 `theme` 상태는 `App`에서 생성되어 `ThemeContext.Provider`를 통해 제공된다. 그리고 `Avatar` 컴포넌트는 중간 컴포넌트들을 거치지 않고 직접 `useContext`를 통해 `theme` 값에 접근한다.

### React 철학과의 일치

Context API는 React의 핵심 철학과 완벽하게 일치한다:

1. **선언적 프로그래밍**: Context는 상태와 그 상태를 사용하는 컴포넌트 간의 관계를 선언적으로 정의한다.
2. **컴포넌트 기반**: Context Provider 자체가 컴포넌트이며, React의 컴포넌트 트리에 자연스럽게 통합된다.
3. **단방향 데이터 흐름**: Context를 통해 데이터는 여전히 상위에서 하위로 흐른다.

### 내장 기능으로서의 이점

Context API가 React에 내장되어 있다는 것은 여러 이점을 제공한다:

1. **추가 의존성 없음**: 외부 라이브러리를 설치할 필요가 없다.
2. **버전 호환성 보장**: React 버전이 업데이트되어도 Context API는 항상 호환된다.
3. **최적화된 통합**: React의 내부 메커니즘과 최적화된 방식으로 통합되어 있다.

## 3. Context API vs 다른 상태 관리 솔루션

Context API가 React의 공식 상태 관리 솔루션으로 자리잡았지만, 다른 상태 관리 라이브러리들과 비교했을 때 어떤 특징이 있는지 살펴보자.

### Redux와의 비교

**Redux**는 가장 널리 사용되는 상태 관리 라이브러리 중 하나다.

**Redux의 장점**:

- 예측 가능한 상태 흐름
- 강력한 개발자 도구
- 미들웨어를 통한 비동기 작업 처리
- 대규모 애플리케이션에 적합

### *Redux 예시*

```jsx

*// Redux 예시*
import { createStore } from 'redux';
import { Provider, connect } from 'react-redux';

*// 리듀서*
function reducer(state = { theme: 'light' }, action) {
  switch (action.type) {
    case 'TOGGLE_THEME':
      return { ...state, theme: state.theme === 'light' ? 'dark' : 'light' };
    default:
      return state;
  }
}

*// 스토어 생성*
const store = createStore(reducer);

*// 컴포넌트*
function App() {
  return (
    <Provider store={store}>
      <ConnectedHeader />
    </Provider>
  );
}

function Header({ theme, toggleTheme }) {
  return (
    <div>
      <h1 className={theme}>Header</h1>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </div>
  );
}

*// Redux와 연결*
const mapStateToProps = state => ({ theme: state.theme });
const mapDispatchToProps = dispatch => ({
  toggleTheme: () => dispatch({ type: 'TOGGLE_THEME' })
});
const ConnectedHeader = connect(mapStateToProps, mapDispatchToProps)(Header);
```

### MobX와의 비교

**MobX**는 반응형 프로그래밍 패러다임을 기반으로 한 상태 관리 라이브러리다.

**MobX의 장점**:

- 자동 상태 추적 및 업데이트
- 적은 보일러플레이트 코드
- 객체 지향적 접근 방식

### *MobX 예시*

```jsx
// MobX 예시
import { makeAutoObservable } from 'mobx';
import { observer } from 'mobx-react';

// 스토어 생성
class ThemeStore {
  theme = 'light';
  
  constructor() {
    makeAutoObservable(this);
  }
  
  toggleTheme() {
    this.theme = this.theme === 'light' ? 'dark' : 'light';
  }
}

const themeStore = new ThemeStore();

// 컴포넌트
const Header = observer(({ store }) => {
  return (
    <div>
      <h1 className={store.theme}>Header</h1>
      <button onClick={() => store.toggleTheme()}>Toggle Theme</button>
    </div>
  );
});

function App() {
  return <Header store={themeStore} />;
}

```

### 그럼 언제 Context API를 사용해야 할까?

Context API는 다음과 같은 상황에 적합하다:

1. **중소규모 애플리케이션**: 복잡한 상태 관리가 필요하지 않은 애플리케이션
2. **테마, 인증, 언어 설정** 등의 전역 상태 관리
3. **특정 컴포넌트 트리 내에서만 공유되는 상태** 관리
4. **빠른 프로토타이핑**이나 간단한 프로젝트

> (👨🏻‍🏫 : "저는 항상 'Context API로 충분한가?'를 먼저 생각해 봅니다. 대부분의 경우 Context API만으로도 충분하더라고요. 복잡한 상태 로직이나 성능 최적화가 필요할 때만 Redux 같은 라이브러리를 고려하는 편이에요! 최근에는 서버 상태관리를 해주는 Tanstack-Query를 사용하며, 더더욱 유저 상태관리는 Context API로 충분하다고 느껴요!")
> 

## 4. Context API의 실제 활용 사례

Context API는 다양한 실제 상황에서 효과적으로 활용될 수 있다. 몇 가지 대표적인 사례를 살펴보자.

### 테마 관리

가장 일반적인 Context API의 사용 사례 중 하나는 애플리케이션의 테마를 관리하는 것이다.

```jsx
*// 출처: https://blog.logrocket.com/react-context-tutorial/// 테마 관리 예시*
import { createContext, useContext, useState } from 'react';

const ThemeContext = createContext();

function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  
  const toggleTheme = () => {
    setTheme(theme === 'light' ? 'dark' : 'light');
  };
  
  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

function useTheme() {
  return useContext(ThemeContext);
}

*// 사용 예시*
function App() {
  return (
    <ThemeProvider>
      <Layout />
    </ThemeProvider>
  );
}

function Layout() {
  const { theme } = useTheme();
  return (
    <div className={`app ${theme}`}>
      <Header />
      <Main />
      <Footer />
    </div>
  );
}

function Header() {
  const { theme, toggleTheme } = useTheme();
  return (
    <header>
      <h1>My App</h1>
      <button onClick={toggleTheme}>
        Switch to {theme === 'light' ? 'dark' : 'light'} mode
      </button>
    </header>
  );
}
```

### 사용자 인증

사용자 인증 상태는 애플리케이션 전체에서 필요한 정보이므로 Context API를 통해 관리하기 적합하다.

```jsx
*// 인증 관리 예시*
import { createContext, useContext, useState } from 'react';

const AuthContext = createContext();

function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  
  const login = (username, password) => {
    *// 로그인 로직*
    setUser({ username });
  };
  
  const logout = () => {
    setUser(null);
  };
  
  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

function useAuth() {
  return useContext(AuthContext);
}

*// 사용 예시*
function App() {
  return (
    <AuthProvider>
      <Layout />
    </AuthProvider>
  );
}

function Layout() {
  const { user } = useAuth();
  return (
    <div>
      <Header />
      {user ? <Dashboard /> : <LoginPage />}
    </div>
  );
}

function Header() {
  const { user, logout } = useAuth();
  return (
    <header>
      {user ? (
        <div>
          Welcome, {user.username}!
          <button onClick={logout}>Logout</button>
        </div>
      ) : (
        <div>Please log in</div>
      )}
    </header>
  );
}
```

### 다국어 지원

여러 언어를 지원하는 애플리케이션에서 현재 선택된 언어와 번역 데이터를 관리하는 데 Context API가 유용하다.

```jsx
*// 다국어 지원 예시*
import { createContext, useContext, useState } from 'react';

const translations = {
  en: {
    greeting: 'Hello',
    welcome: 'Welcome to our app'
  },
  ko: {
    greeting: '안녕하세요',
    welcome: '우리 앱에 오신 것을 환영합니다'
  }
};

const LanguageContext = createContext();

function LanguageProvider({ children }) {
  const [language, setLanguage] = useState('en');
  
  const t = (key) => translations[language][key];
  
  const changeLanguage = (lang) => {
    setLanguage(lang);
  };
  
  return (
    <LanguageContext.Provider value={{ language, t, changeLanguage }}>
      {children}
    </LanguageContext.Provider>
  );
}

function useLanguage() {
  return useContext(LanguageContext);
}

*// 사용 예시*
function App() {
  return (
    <LanguageProvider>
      <Layout />
    </LanguageProvider>
  );
}

function Layout() {
  const { t } = useLanguage();
  return (
    <div>
      <Header />
      <main>
        <h1>{t('welcome')}</h1>
      </main>
    </div>
  );
}

function Header() {
  const { language, changeLanguage } = useLanguage();
  return (
    <header>
      <select 
        value={language} 
        onChange={(e) => changeLanguage(e.target.value)}
      >
        <option value="en">English</option>
        <option value="en">English</option>
        <option value="ko">한국어</option>
      </select>
    </header>
  );
}
```

### 복합 Context 사용하기

실제 애플리케이션에서는 여러 Context를 함께 사용하는 경우가 많다. 이를 효과적으로 구성하는 방법을 살펴보자.

```jsx
// 여러 Context 조합 예시
import { createContext, useContext, useState } from 'react';

// 여러 Context 정의
const ThemeContext = createContext();
const AuthContext = createContext();
const LanguageContext = createContext();

// 통합 Provider 컴포넌트
function AppProvider({ children }) {
  return (
    <ThemeProvider>
      <AuthProvider>
        <LanguageProvider>
          {children}
        </LanguageProvider>
      </AuthProvider>
    </ThemeProvider>
  );
}

// 사용 예시
function App() {
  return (
    <AppProvider>
      <Layout />
    </AppProvider>
  );
}

function UserProfile() {
  // 여러 Context 함께 사용
  const { theme } = useTheme();
  const { user } = useAuth();
  const { t } = useLanguage();
  
  return (
    <div className={theme}>
      <h2>{t('profile')}</h2>
      <p>{t('welcome')}, {user.name}!</p>
    </div>
  );
}
```

> (👨🏻‍🏫 : "실무에서는 이렇게 여러 Context를 함께 사용하는 경우가 많아요. 처음에는 복잡해 보일 수 있지만, 각 Context가 명확한 책임을 가지고 있으면 코드가 훨씬 정리되고 유지보수하기 쉬워진답니다!")
> 

## 5. Context API의 한계와 최적화

Context API가 많은 장점을 가지고 있지만, 모든 상황에 완벽한 해결책은 아니다. Context API의 한계와 이를 극복하기 위한 최적화 방법을 알아보자.

### 성능 이슈

Context API의 가장 큰 한계 중 하나는 성능과 관련이 있다. Context 값이 변경되면 해당 Context를 사용하는 모든 컴포넌트가 리렌더링된다.

```jsx
// 성능 이슈 예시
const AppContext = createContext();

function AppProvider({ children }) {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState('light');
  const [notifications, setNotifications] = useState([]);
  
  // 모든 상태가 하나의 Context에 포함됨
  const value = { user, setUser, theme, setTheme, notifications, setNotifications };
  
  return (
    <AppContext.Provider value={value}>
      {children}
    </AppContext.Provider>
  );
}

// 문제: notifications가 변경될 때마다 
// theme만 사용하는 컴포넌트도 리렌더링됨
```

### Context 분리하기

이 문제를 해결하기 위한 첫 번째 방법은 관련 상태별로 Context를 분리하는 것이다.

```jsx
// Context 분리 예시
const UserContext = createContext();
const ThemeContext = createContext();
const NotificationContext = createContext();

function AppProvider({ children }) {
  return (
    <UserProvider>
      <ThemeProvider>
        <NotificationProvider>
          {children}
        </NotificationProvider>
      </ThemeProvider>
    </UserProvider>
  );
}

// 이제 notifications가 변경되어도 
// theme만 사용하는 컴포넌트는 리렌더링되지 않음
```

### React.memo와 함께 사용하기

`Context API`를 사용할 때 `React.memo`를 활용하여 불필요한 리렌더링을 방지할 수 있다.

```jsx
// React.memo 활용 예시
const ThemeContext = createContext();

// React.memo로 컴포넌트 감싸기
const ThemedButton = React.memo(function ThemedButton({ onClick, children }) {
  const { theme } = useContext(ThemeContext);
  
  return (
    <button 
      className={theme} 
      onClick={onClick}
    >
      {children}
    </button>
  );
});

// 이제 props가 변경되지 않으면 리렌더링되지 않음
```

### Context 값 메모이제이션

Context Provider의 value를 메모이제이션하여 불필요한 리렌더링을 방지할 수 있다.

```jsx
// Context 값 메모이제이션 예시
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  
  // useMemo로 value 메모이제이션
  const value = useMemo(() => {
    return { theme, setTheme };
  }, [theme]);
  
  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
}
```

### 언제 다른 상태 관리 솔루션을 고려해야 할까?

Context API가 적합하지 않은 경우도 있다:

1. **복잡한 상태 로직**: 상태 업데이트 로직이 복잡한 경우 Redux나 MobX가 더 적합할 수 있다.
2. **빈번한 업데이트**: 매우 자주 업데이트되는 상태의 경우 Context의 성능 이슈가 문제될 수 있다.
3. **미들웨어 필요**: 비동기 작업, 로깅 등을 위한 미들웨어가 필요한 경우 Redux가 더 적합하다.

> (👨🏻‍🏫 : "Context API는 만능 해결책이 아니에요. 때로는 Redux, Zustand, Recoil 같은 라이브러리가 더 적합할 수 있습니다. 중요한 건 프로젝트의 요구사항에 맞는 도구를 선택하는 거예요!")
> 

## 6. 결론

React가 Context API를 공식적으로 채택한 이유는 명확하다. Context API는 React의 철학과 완벽하게 일치하며, 추가 의존성 없이 간단하고 효과적인 상태 관리 솔루션을 제공한다. 특히 다음과 같은 이유로 Context API는 React의 공식 상태 관리 솔루션으로 자리잡았다:

1. **React 생태계와의 통합**: 외부 라이브러리 없이 React 자체에 내장되어 있어 자연스러운 통합이 가능하다.
2. **간결한 API**: 복잡한 보일러플레이트 코드 없이 직관적인 API를 제공한다.
3. **유연성**: 다양한 크기의 애플리케이션과 다양한 사용 사례에 적용할 수 있다.
4. **컴포넌트 기반 접근 방식**: React의 컴포넌트 모델과 자연스럽게 어울린다.

물론 Context API가 모든 상황에 완벽한 해결책은 아니다. 복잡한 상태 로직, 성능 최적화, 미들웨어 지원 등이 필요한 경우에는 Redux나 다른 상태 관리 라이브러리가 더 적합할 수 있다. 그러나 많은 애플리케이션에서 Context API는 충분히 강력하고 유연한 솔루션으로 채택될 수 있다.

결국 React 팀이 Context API를 선택한 것은 "적절한 도구를 적절한 상황에" 제공하려는 철학의 반영이다. 모든 상황에 대응하는 복잡한 솔루션보다는, 가장 일반적인 사용 사례를 간단하고 우아하게 해결하는 솔루션을 제공하고자 한 것이다. 그리고 이러한 선택은 React 개발자들에게 큰 혜택을 가져다 주었다. 

> (👨🏻‍🏫 : "개인적으로 최근에는 서버 상태관리 툴(Tanstack-Query) 와 유저 상태관리 툴(Redux, Recoil, Zustand, MobX, Context API) 등을 분리하는 방법론이 나오면서 유저 상태관리는 Context API로도 충분하다고 생각합니다! 물론 요구사항에 맞는 툴을 찾는 게 제일 중요하겠죠?")
> 

> 🙇🏻 글 내에 틀린 점, 오탈자, 비판, 공감 등 모두 적어주셔도 됩니다. 감사합니다..! 🙇🏻
>
