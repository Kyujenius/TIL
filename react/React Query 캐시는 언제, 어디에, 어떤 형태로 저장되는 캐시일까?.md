# React Query 캐시는 언제, 어디에, 어떤 형태로 저장되는 캐시일까?

프론트엔드 개발을 하다 보면 데이터 페칭과 상태 관리에 대한 고민은 끊임없이 이어진다. 특히 React 생태계에서는 이러한 고민을 해결하기 위한 다양한 라이브러리들이 존재하는데, 그 중에서도 TanStack Query(구 React Query)는 데이터 페칭과 캐싱을 효율적으로 관리할 수 있는 강력한 도구로 자리 잡았다. 그런데 이 라이브러리를 사용하면서 한 가지 궁금증이 생길 수 있다. "React Query는 대체 어디에 캐시를 저장하는 걸까?"

> (👨🏻‍🏫 : "여러분, 면접에서도 자주 물어보는 질문인데요! 많은 분들이 로컬 스토리지나 세션 스토리지라고 대답하시곤 하죠. 과연 정답은 무엇일까요?")
> 

이 글에서는 React Query의 캐싱 메커니즘에 대해 자세히 알아보고, 실제로 캐시가 어디에 저장되는지, 그리고 필요에 따라 어떻게 영구 저장소에 캐시를 유지할 수 있는지 살펴볼 것이다.

## 💡급하신 분들을 위해서 결론 먼저!

1. React Query는 기본적으로 **JavaScript의 런타임 메모리(RAM)**에 캐시를 저장한다.
2. 페이지 새로고침이나 브라우저 종료 시 캐시는 **모두 사라진다.**
3. 영구 저장이 필요하다면 **persistQueryClient** 플러그인을 사용하여 localStorage 등에 저장할 수 있다.
4. 캐시는 **일반 JavaScript 객체** 형태로 저장되며, 쿼리 키를 기반으로 구성된다.
5. **gcTime**과 **staleTime** 속성이 캐시의 수명과 신선도를 결정한다.

## 1. React Query의 기본 캐싱 메커니즘

## 캐시 저장 위치: 메모리

React Query는 모든 캐시 데이터를 **JavaScript의 런타임 메모리**에 저장한다. 이는 브라우저의 RAM에 세션 동안만 유지되는 방식이다. 이것은 로컬 스토리지나 세션 스토리지, IndexedDB와 같은 브라우저의 영구 저장소와는 다른 개념이다.

> (👨🏻‍🏫 : "많은 분들이 오해하시는 부분인데요, React Query는 기본적으로 메모리에만 데이터를 저장합니다. 페이지를 새로고침하면 모든 데이터가 날아가버린답니다! 왜 날아갈까요? ")
> 

### 새로고침 시 과정

1. **현재 페이지의 리소스 폐기**: 브라우저는 현재 로드된 페이지의 모든 JavaScript 코드, DOM 요소, **메모리에 저장된 모든 데이터를 완전히 폐기됨**.
2. **새 페이지 요청 시작**: 브라우저는 서버에 현재 URL에 대한 새로운 HTTP 요청을 보냅니다.
3. **새 페이지 로드 및 실행**: 서버로부터 응답을 받은 후, 브라우저는 HTML, CSS, JavaScript 등 모든 리소스를 처음부터 다시 로드하고 실행합니다.

이 과정에서 가장 중요한 점은 **모든 메모리가 초기화된다**는 것이다. 즉, JavaScript 런타임 환경이 완전히 재설정된다. 따라서, React Query 와 같이 JS 런타임 메모리에 저장하는 경우에는 새로고침시, 캐시를 해두었던 내용이 전부 사라진다.

> (👨🏻‍🏫 : "JS 객체로 성능 개선을 한 것 중에 대표적인 것을 꼽자면 Virtual DOM을 꼽을 수 있는데요? Virtual DOM 또한 DOM 의 얕은 복사본으로, 이 또한 JS 객체입니다. React 는 기존의 무거운 DOM에서 Virtual DOM을 적용해 비교(diffing)에 있어서, 훨씬 빠른 속도를 이끌어낸 것과 비슷하다고 볼 수 있겠죠? 그러나, 마냥 좋은 것만은 아닙니다. 메모리는 빠르지만 휘발성이 있어요. 전원이 꺼지면 데이터가 모두 사라지는 것처럼, 페이지를 새로고침하면 React Query의 캐시도 모두 사라진답니다. 허거덩~~ 😨")
> 

### 캐시의 구조

React Query의 캐시는 **QueryCache**라는 **JS 객체**에 의해 관리된다. 이 객체는 **모든 쿼리의 데이터, 메타 정보 및 상태를 저장**한다. 일반적으로 개발자는 QueryCache와 직접 상호작용하지 않고, queryKey를 통해서 QueryClient를 통해 특정 캐시에 접근한다.

```jsx
import { QueryCache } from '@tanstack/react-query'

const queryCache = new QueryCache({
  onError: (error) => {
    console.log(error)
  },
  onSuccess: (data) => {
    console.log(data)
  },
  onSettled: (data, error) => {
    console.log(data, error)
  },
})

const query = queryCache.find(['posts'])
```

출처: https://tanstack.com/query/v5/docs/reference/QueryCache

## 2. 메모리 기반 캐싱의 장단점

### 장점: 빠른 접근 속도

메모리 기반 캐싱의 가장 큰 장점은 **접근 속도가 매우 빠르다**는 점이다. 디스크 기반 저장소에 비해 메모리는 훨씬 빠른 읽기/쓰기 성능을 제공하므로, 사용자 경험 측면에서 큰 이점을 가진다.

### 단점: 휘발성

반면, 메모리 기반 캐싱의 가장 큰 단점은 **휘발성**이다. 페이지를 새로고침하거나 브라우저를 닫으면 모든 캐시 데이터가 사라진다. 이는 사용자가 페이지를 다시 방문할 때 모든 데이터를 다시 가져와야 함을 의미한다.

### gcTime과 staleTime

React Query는 **gcTime**(가비지 컬렉션 시간)과 **staleTime**(데이터 신선도 시간)이라는 두 가지 중요한 속성을 통해 캐시의 수명과 신선도를 관리한다.

- **staleTime**: 데이터가 '신선'하다고 간주되는 시간. 이 시간 동안은 쿼리가 다시 마운트되어도 **네트워크 요청을 하지 않는다**.
- **gcTime**: **비활성 쿼리가 메모리에서 제거되기까지의 시간**. 기본값은 5분이다.

```jsx
const { data } = useQuery({
  queryKey: ['todos'],
  queryFn: fetchTodos,
  staleTime: 1000 * 60 * 5, *// 5분*
  gcTime: 1000 * 60 * 60, *// 1시간*
})
```

> (👨🏻‍🏫 : "JS 객체로 성능 개선을 한 것 중에 대표적인 것을 꼽자면 Virtual DOM을 꼽을 수 있는데요? Virtual DOM 또한 DOM 의 얕은 복사본으로, JS 객체입니다. React 는 기존의 무거운 DOM에서 Virtual DOM을 적용해 비교(diffing)에 있어서, 훨씬 빠른 속도를 이끌어낸 것과 비슷하다고 볼 수 있겠죠? 그러나, 마냥 좋은 것만은 아닙니다. 메모리는 빠르지만 휘발성이 있어요. 전원이 꺼지면 데이터가 모두 사라지는 것처럼, 페이지를 새로고침하면 React Query의 캐시도 모두 사라진답니다. 허거덩~~ 😨 그럼 이 휘발성을 어떻게 제거할까요?")
> 

## 3. 영구 저장소로 캐시 유지하기

### persistQueryClient 플러그인

React Query는 기본적으로 메모리에만 캐시를 저장하지만, **persistQueryClient** 플러그인을 사용하면 캐시를 영구 저장소(localStorage, sessionStorage 등)에 유지할 수 있다( 요구사항에 따라서는 새로 만들 수도 있다 )

```jsx
import { PersistQueryClientProvider } from '@tanstack/react-query-persist-client'
import { createSyncStoragePersister } from '@tanstack/query-sync-storage-persister'

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      gcTime: 1000 * 60 * 60 * 24, *// 24시간*
    },
  },
})

const persister = createSyncStoragePersister({
  storage: window.localStorage,
})

ReactDOM.createRoot(rootElement).render(
  <PersistQueryClientProvider
    client={queryClient}
    persistOptions={{ persister }}
  >
    <App />
  </PersistQueryClientProvider>,
)
```

출처: https://tanstack.com/query/v5/docs/react/plugins/persistQueryClient

> (👨🏻‍🏫 : "이 플러그인을 사용하면 React Query의 캐시를 로컬 스토리지 같은 곳에 저장할 수 있어요. 페이지를 새로고침해도 데이터가 유지된답니다! 문서를 읽어보시면 [**createSyncStoragePersister](https://tanstack.com/query/v5/docs/framework/react/plugins/createSyncStoragePersister), [createAsyncStoragePersister](https://tanstack.com/query/v5/docs/framework/react/plugins/createAsyncStoragePersister), [create a custom persister](https://tanstack.com/query/v5/docs/framework/react/plugins/persistQueryClient/#persisters) 이렇게 나뉘어져 있어요! 한 번 비교해볼까요?** ")
> 

### React Query의 다양한 Persister 비교

React Query에서 제공하는 다양한 persister들은 쿼리 캐시를 저장하는 방식에 차이가 있다. 각 persister의 특징과 차이점을 자세히 살펴보겠다.

### createSyncStoragePersister

`createSyncStoragePersister`는 동기적으로 작동하는 스토리지를 위한 persister를 생성한다.

**주요 특징:**

- 브라우저의 `localStorage`나 `sessionStorage`와 같은 동기적 스토리지 API와 함께 사용된다.
- **웹 환경에서 주로 사용된다.**
- **데이터 쓰기 작업을 최적화하기 위해 1초에 최대 한 번만 저장 작업을 수행한다.**
- **구현 방법이 간단하다.**

**사용 예시:**

```jsx
import { persistQueryClient } from '@tanstack/react-query-persist-client'
import { createSyncStoragePersister } from '@tanstack/query-sync-storage-persister'

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      cacheTime: 1000 * 60 * 60 * 24, *// 24시간*
    },
  },
})

const localStoragePersister = createSyncStoragePersister({
  storage: window.localStorage,
})

persistQueryClient({
  queryClient,
  persister: localStoragePersister,
})
```

### createAsyncStoragePersister

`createAsyncStoragePersister`는 비동기적으로 작동하는 스토리지를 위한 persister를 생성한다.

**주요 특징:**

- React Native의 `AsyncStorage`와 같은 비동기 스토리지 API와 함께 사용된다.
- **모바일 앱 환경에서 주로 사용된다.**
- **비동기 작업을 지원하므로 모바일 환경에 적합하다.**
- **재시도 메커니즘도 비동기적으로 작동할 수 있다.**

**사용 예시:**

```jsx
import AsyncStorage from '@react-native-async-storage/async-storage'
import { QueryClient } from '@tanstack/react-query'
import { PersistQueryClientProvider } from '@tanstack/react-query-persist-client'
import { createAsyncStoragePersister } from '@tanstack/query-async-storage-persister'

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      cacheTime: 1000 * 60 * 60 * 24, *// 24시간*
    },
  },
})

const asyncStoragePersister = createAsyncStoragePersister({
  storage: AsyncStorage,
})

const Root = () => (
  <PersistQueryClientProvider
    client={queryClient}
    persistOptions={{ persister: asyncStoragePersister }}
  >
    <App />
  </PersistQueryClientProvider>
)
```

### 사용자 정의 Persister 생성하기 (아직 실험단계임)

자신만의 커스텀 persister를 만들 수도 있다. 이는 특별한 스토리지 요구사항이 있을 때 유용하다.

**주요 특징:**

- `Persister` 인터페이스를 구현하여 자신만의 `persister`를 만들 수 있다.
- `IndexedDB`와 같은 다른 스토리지 메커니즘을 사용할 수 있다.
- 특정 요구사항에 맞게 저장 및 복원 로직을 커스터마이즈할 수 있다.

**사용 예시 (IndexedDB 기반 persister):**

```jsx
import { get, set, del } from 'idb-keyval'
import { PersistedClient, Persister } from '@tanstack/react-query-persist-client'

export function createIDBPersister(idbValidKey = 'reactQuery') {
  return {
    persistClient: async (client: PersistedClient) => {
      await set(idbValidKey, client)
    },
    restoreClient: async () => {
      return await get<PersistedClient>(idbValidKey)
    },
    removeClient: async () => {
      await del(idbValidKey)
    },
  } as Persister
}
```

### 주요 차이점 요약

| Persister 유형 | 작동 방식 | 주요 사용 환경 | 스토리지 타입 | 특징 |
| --- | --- | --- | --- | --- |
| createSyncStoragePersister | 동기적 | 웹 브라우저 | localStorage, sessionStorage | 간단한 구현, 1초당 최대 1회 저장 |
| createAsyncStoragePersister | 비동기적 | 모바일 앱 | AsyncStorage | 비동기 작업 지원, 모바일 환경에 최적화 |
| 커스텀 Persister | 사용자 정의 | 특수 요구사항 | 사용자 정의 (예: IndexedDB) | 완전한 커스터마이징 가능 |

> (👨🏻‍🏫 : "왜 SyncStroage(동기) AsyncStorage(비동기) 방식에 따라, 웹과 앱에서 다르게 쓸까요? ㅎㅎ 이것 또한 나중에 블로그에서 다뤄보겠습니다! 결론부터 얘기하자면 모바일 환경에선 리소스가 제한적이라 비동기 작업이 UI 성능 유지에 중요하답니다…! ")
> 

### 주의사항

**persistQueryClient를 사용하면 모든 쿼리 데이터가 지정된 저장소에 유지된다. 따라서 민감한 정보를 포함하는 쿼리에는 주의해야 한다.**

## 4. 실제 사용 사례와 최적화 전략

### 언제 메모리 캐싱만으로 충분할까?

단일 세션 내에서만 데이터를 유지하면 되는 경우, 기본 메모리 캐싱만으로도 충분하다. 예를 들어, 대시보드나 관리자 페이지처럼 사용자가 한 번 로드한 후 지속적으로 사용하는 페이지에 적합하다.

### 언제 영구 저장이 필요할까?

다음과 같은 경우에는 영구 저장소를 고려해볼 수 있다:

- 사용자가 자주 페이지를 새로고침하는 경우
- 오프라인 지원이 필요한 경우
- 초기 로딩 시간을 최소화하고 싶은 경우

### 성능 최적화 팁

1. **적절한 staleTime 설정**: 데이터의 변경 빈도에 따라 staleTime을 조정하여 불필요한 네트워크 요청을 줄인다.
2. **선택적 영구 저장**: 모든 쿼리를 영구 저장하기보다는, 필요한 쿼리만 선택적으로 저장한다.
3. **캐시 크기 관리**: 너무 많은 데이터를 캐시하면 성능 저하가 발생할 수 있으므로, 적절한 gcTime을 설정하여 캐시 크기를 관리한다.

```jsx
*// 자주 변경되는 데이터*
const { data: realtimeData } = useQuery({
  queryKey: ['realtime-stats'],
  queryFn: fetchRealtimeStats,
  staleTime: 1000 * 10, *// 10초*
  gcTime: 1000 * 60, *// 1분*
})

*// 거의 변경되지 않는 데이터*
const { data: staticData } = useQuery({
  queryKey: ['static-config'],
  queryFn: fetchStaticConfig,
  staleTime: Infinity, *// 항상 신선*
  gcTime: Infinity, *// 절대 삭제하지 않음*
})
```

이제 React Query의 캐싱 메커니즘에 대해 자세히 알아보았다. 기본적으로 React Query는 메모리에 캐시를 저장하지만, 필요에 따라 persistQueryClient 플러그인을 사용하여 영구 저장소에 캐시를 유지할 수 있다. 각 프로젝트의 요구사항과 데이터 특성에 맞게 적절한 캐싱 전략을 선택하는 것이 중요하다.

> (👨🏻‍🏫 : "사용자 경험을 최우선으로 생각한다면, 자주 변경되지 않는 데이터는 영구 저장소에 캐시하는 것이 좋습니다. **하지만 민감한 정보는 절대 저장하면 안 된다는 점, 꼭 기억하세요! 큰 힘에는 큰 책임이 따른답니다.** **고려해야 할 또 다른 점은 다른 브라우저마다 localStorage 용량이 다르다는 것입니다.** 일반적으로 일상적인 데이터를 저장하기에는 좋은 옵션이지만, 일반적인 저장 한도가 약 5-10MB인 것을 고려하면 로컬 스토리지는 대용량 데이터셋을 저장하기에 최선의 솔루션이 아닐 수 있으며, 데이터가 너무 커질 경우를 대비한 적절한 대체 방안을 구현해야 합니다.")
> 

> 🙇🏻 글 내에 틀린 점, 오탈자, 비판, 공감 등 모두 적어주셔도 됩니다. 감사합니다..! 🙇🏻
>
