>![](https://velog.velcdn.com/images/kyujenius/post/a5c3479d-8052-4b19-b3b2-a803db6fbe16/image.png)

출처: https://www.instagram.com/coding.xplorers/reel/DGalDrXisg1/

CRA는 React 애플리케이션을 시작하는 과정을 크게 간소화했다. 단일 명령어로 개발 환경을 구축할 수 있었기 때문에 초보자도 쉽게 React 개발을 시작할 수 있었다.

## CRA의 편리함 : 간단한 시작

CRA는 단 하나의 명령어로 React 프로젝트를 생성할 수 있었다:

```bash
npx create-react-app my-app
```

이 명령어 하나로 다음과 같은 작업이 자동으로 이루어졌다:

- React, ReactDOM, React-Scripts 설치
- Webpack, Babel, ESLint 등 필요한 모든 개발 도구 설정
- 개발 서버, 빌드 스크립트, 테스트 환경 구성
- Hot Module Replacement가 가능한 개발 서버 제공
- 기본적인 프로젝트 구조

## 설정 없는 개발 환경

CRA는 복잡한 Webpack 설정을 숨겨 개발자가 설정 파일을 만지지 않고도 바로 코드 작성에 집중할 수 있게 했다. 이는 특히 다음과 같은 이점을 제공했다 ( 저도 사실은 **Webpack이 Default로 사용되고 있음**을 나중에 알았습니다. 그만큼 알게 모르게 이미 많은 것들을 지원했던 셈이죠 )

- JSX, ES6/7, TypeScript 지원
- CSS 모듈, Sass 지원
- 이미지, 폰트 등 정적 자산 처리
- 서비스 워커 지원
- 코드 분할 기능

## 표준화된 구조

CRA는 모든 React 프로젝트에 일관된 구조를 제공했다. 이로 인해 팀 간 협업이 쉬워지고, 새로운 개발자도 프로젝트에 빠르게 적응할 수 있었다.

## React 공식 문서의 Vite 권장

React 공식 문서 속(v19 이후)는 이제 CRA 대신 Vite를 사용하도록 권장하고 있다. React 공식 문서의 "Start a New React Project" 섹션에서는 다음과 같이 설명한다:

https://react.dev/learn/build-a-react-app-from-scratch

"우리는 새로운 React 프로젝트를 시작할 때 Vite와 같은 도구를 사용할 것을 권장합니다. Vite는 빠른 개발 서버를 제공하고 프로덕션을 위한 번들을 생성하는 현대적인 빌드 도구입니다."

공식 문서에서는 Vite를 사용하여 새 프로젝트를 시작하는 방법을 다음과 같이 안내한다:

```tsx
npm create vite@latest my-react-app -- --template react
```

또는 TypeScript를 사용하는 경우:

```tsx
npm create vite@latest my-react-app -- --template react-ts
```

React 팀은 Vite를 권장하는 주요 이유로 다음을 언급한다:

1. **속도**: Vite는 ES 모듈을 활용하여 CRA보다 훨씬 빠른 개발 서버를 제공한다.
2. **현대적인 개발 경험**: 즉각적인 HMR(Hot Module Replacement)과 더 나은 오류 메시지를 제공한다.
3. **가벼움**: 불필요한 의존성이 적어 설치 시간이 짧고 프로젝트 크기가 작다.
4. **유연성**: 필요에 따라 쉽게 구성을 조정할 수 있다.

공식 문서에서는 "Create React App은 더 이상 권장되지 않으며 새로운 기능 개발이 중단되었습니다"라고 명시하며, 대신 Vite나 Next.js와 같은 현대적인 도구를 사용할 것을 권장한다.

## CRA의 전격 중단 대체 왜?

React 개발팀이 2025년 2월 14일, 공식적으로 Create React App(CRA)의 지원 중단을 발표했다.  한때 React 애플리케이션을 시작하는 가장 인기 있는 방법이었던 CRA는 이제 새로운 프로젝트에 더 이상 권장되지 않는다.

## CRA 지원 중단의 배경

CRA는 2016년에 출시되어 React 애플리케이션을 처음부터 구축하는 어려움을 해결하기 위해 만들어졌다. 그러나 2021년 이후로 거의 유지보수가 이루어지지 않았으며, React 커뮤니티는 수년 동안 CRA가 권장되지 않는다는 것을 알고 있었다.

최근 **React 19** 출시 이후 발생한 호환성 문제가 공식 지원 중단의 직접적인 계기가 되었다. NPM 도구에서 의존성 문제로 인해 오류가 발생했고, 이는 "완벽한 비호환성의 폭풍"이라고 불렸다.

### 라우팅(Routing) 문제

CRA는 특정 라우팅 솔루션을 포함하지 않는다.  초기에는 `useState`를 사용하여 라우트를 전환할 수 있지만, 이 방식은 URL을 변경하지 않아 모든 링크가 동일한 페이지로 이동하게 되는 문제가 있다. 결국 대부분의 앱은 `React Router`나 `Tanstack Router`와 같은 라우팅 라이브러리를 추가로 설치해야 했다.

### 데이터 불러오기(Data Fetching) 한계

CRA는 데이터 불러오기(`Data Fetching`) 솔루션을 포함하지 않는다. 일반적으로 개발자들은 `useEffect` 내에서 `fetch`를 사용하여 데이터를 로드했지만, 이 방식은 컴포넌트가 렌더링된 후에 데이터를 가져오기 때문에 네트워크 폭포수(network waterfall) 현상을 유발한다. 이는 코드가 다운로드되는 동안 병렬로 데이터를 가져오는 대신, 앱이 렌더링될 때 데이터를 가져오기 때문에 발생한다.

### 코드 분할 기능 부재

CRA는 코드 분할 솔루션을 포함하지 않는다. 이로 인해 앱이 단일 번들로 제공되어 사용자가 전체 코드를 다운로드해야 하는 문제가 있다. 이상적인 성능을 위해서는 코드를 별도의 번들로 "분할"하여 사용자가 필요한 부분만 다운로드할 수 있도록 해야 한다. 그 과정에서 항상 `Webpack` 숙련도가 선행되어야 했고, 이에 따라서 많은 회사에서도 `Webpack`을 잘 다뤄본 자들을 뽑으려고 한다.

### **최신 React 패턴 미지원**

 React 18, 19의 새로운 기능(예: 서버 컴포넌트, Suspense for Data Fetching)을 활용하기 어려움

### **성능 최적화 한계**

기본 `Webpack` 설정이 무겁고, 최신 빌드 도구(`Vite`, `Turbopack` 등)에 비해 개발 서버 속도가 느림

## 대안: 무엇을 사용해야 하나?

React 팀은 이제 새로운 앱을 위해 다음과 같은 대안을 권장한다:

## 1. 프레임워크 사용

- **Next.js**: 서버 사이드 렌더링과 정적 사이트 생성을 지원하는 완전한 프레임워크
- **Remix**: 웹의 네이티브 기능을 활용한 최적의 성능을 제공하는 프레임워크
- **Gatsby**: 정적 사이트 생성에 특화된 프레임워크

## 2. 빌드 도구 사용

- **Vite**: 번들리스 개발 서버를 제공하는 빠른 빌드 도구
- **Parcel**: 설정이 필요 없는 간편한 번들러
- **Rsbuild**: 성능 최적화에 중점을 둔 빌드 도구

## 현재 CRA 사용시 경고!

CRA를 설치하려고 하면 이제 다음과 같은 경고 메시지가 표시된다:

```tsx
create-react-app is deprecated.
You can find a list of up-to-date React frameworks on react.dev
```

CRA는 여전히 유지보수 모드로 작동하며 React 19를 지원하도록 업데이트되었지만, 새로운 기능이나 개선 사항은 더 이상 추가되지 않는다. React 팀은 기존 앱을 프레임워크나 빌드 도구로 마이그레이션하기 위한 상세한 가이드를 제공하고 있다.

## Vite 를 쓰자!
> ![](https://velog.velcdn.com/images/kyujenius/post/44f9a9f2-42e1-47b3-b327-730f94d711fa/image.png)


Vite로 프로젝트 초기 세팅하기: https://ko.vite.dev/guide/

### Typescript + React + Vite 로 초기 세팅

```bash
# npm v7 이상에서는 `--` 를 반드시 붙여주세요:
npm create vite@latest my-own-react-app --template react-ts
```



### Why Vite?
Vite를 왜 써야하는지: https://ko.vite.dev/guide/why

브라우저에서 ESM(ES Modules)을 지원하기 전까지, JavaScript 모듈화를 네이티브 레벨에서 진행할 수 없었습니다. 그래서 소스 모듈을 브라우저에서 실행할 수 있는 파일로 크롤링, 처리 및 연결하는 "번들링(Bundling)"이라는 해결 방법을 사용해야 했습니다.

Webpack, Rollup 그리고 Parcel과 같은 도구는 이런 번들링 작업을 진행해줌으로써 프런트엔드 개발자의 생산성을 크게 향상시켰습니다.

하지만 애플리케이션이 점점 더 발전함에 따라 처리해야 하는 JavaScript 모듈의 개수도 극적으로 증가하고 있습니다. 심지어 수천 개의 모듈이 존재하는 것도 대규모 프로젝트에서는 그리 드문 일이 아닙니다. 이러한 상황에서 JavaScript 기반의 도구는 성능 병목 현상이 발생되었고, 종종 개발 서버를 가동하는 데 비합리적으로 오랜 시간을 기다려야 한다거나 HMR을 사용하더라도 변경된 파일이 적용될 때 까지 수 초 이상 소요되곤 했습니다. 이와 같은 느린 피드백 루프는 개발자의 생산성과 행복에 적지 않은 영향을 줄 수 있습니다.

Vite는 이러한 것에 초점을 맞춰, 브라우저에서 지원하는 ES Modules(ESM) 및 네이티브 언어로 작성된 JavaScript 도구 등을 활용해 문제를 해결하고자 합니다.**( 어떻게 해결하고자 하는 건지는 추후 포스팅에서 자세히 다루겠습니다.)**



## 결론

CRA의 지원 중단은 **React** 생태계의 중요한 변화를 의미한다. 이는 더 효율적이고 성능이 뛰어나며 확장 가능한 React 개발을 향한 긍정적인 변화로 볼 수 있다. 개발자들은 이제 **Next.js**, **Vite** 등 상황에 맞는 도구를 탐색하고 채택하여 React 개발의 미래를 함께 받아들일 시간이다. 웹 생태계에서의 지대한 영향을 미치고 있는 **React 팀에서 더 큰 혁신을 위해 포기하는 것이라고 보면 된다. **

**글 내에 틀린 점, 오탈자, 비판, 모두 적어주셔도 흔쾌히 받아들이겠습니다. 감사합니다..!**
