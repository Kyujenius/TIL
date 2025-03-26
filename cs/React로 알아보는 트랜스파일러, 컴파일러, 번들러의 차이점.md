## React로 알아보는 트랜스파일러, 컴파일러, 번들러의 차이점

프론트엔드 개발자라면 누구나 한 번쯤 경험해봤을 것이다. 프로젝트를 시작하려는데 webpack.config.js, babel.config.js, package.json 등 온갖 설정 파일들이 눈앞에 펼쳐지는 상황을. "이게 다 뭐지?".. 트랜스파일러, 컴파일러, 번들러... 이 용어들이 뭘 자꾸 하나로 합쳐주거나, 변환을 해준다는데 매번 헷갈리기 마련이다.

> (👨🏻‍🏫 : 근데 저는 처음에 이런 용어들을 구분하지 못해서 한참 헤맸답니다. 사실 알아야 하는 순간이 오기 전까지는, 굳이 알 필요가 없기도 합니다…! 여러분은 제 글을 읽고 그런 시기에 여러 시행착오를 겪지 않았으면 좋겠네요!)

## 💡급하신 분들을 위해서 결론 먼저!

1. **트랜스파일러**는 한 언어에서 다른 언어로 변환하는 도구 (예: ES6 → ES5, TypeScript → JavaScript)
2. **컴파일러**는 코드를 브라우저가 이해할 수 있는 형태로 변환하는 도구 (front-end에 한해서)
3. **번들러**는 여러 파일을 하나로 묶어 최적화하는 도구 (예: Webpack, Rollup, Parcel)
4. 이 도구들은 별개의 기능을 수행하지만 종종 함께 사용됨
5. 최신 도구들은 더 빠른 성능과 개발자 경험을 제공함 (예: Vite, esbuild, Turbopack)

## 1. 트랜스파일러(Transpiler)란 무엇인가?

### 트랜스파일러의 정의와 역할

**트랜스파일러**는 한 프로그래밍 언어로 작성된 코드를 비슷한 수준의 다른 프로그래밍 언어로 변환하는 도구다. 가장 대표적인 예로는 **Babel**이 있다. Babel은 최신 JavaScript(ES6+) 코드를 이전 버전(ES5)으로 변환하여 오래된 브라우저에서도 실행될 수 있게 해준다.

> (👨🏻‍🏫 : 트랜스파일러와 컴파일러의 차이점이 헷갈리시죠? 트랜스파일러는 '비슷한 수준'의 언어 간 변환이고, 컴파일러는 보통 '다른 수준'의 언어로 변환한답니다! 그래도 이해가 안 된다고요? 트랜스파일러는 경상도 사투리 → 표준어, 컴파일러는 한국어 → 영어로 보시면 될 거 같아요!)

```tsx
*// ES6 코드*
const greeting = name => `Hello, ${name}!`;

*// Babel로 트랜스파일 후 (ES5 코드)*
var greeting = function greeting(name) {
  return "Hello, ".concat(name, "!");
};
```

트랜스파일러는 개발자가 최신 문법을 사용하면서도 호환성 문제를 걱정하지 않게 해준다. TypeScript를 JavaScript로 변환하는 것도 트랜스파일링의 한 예다.

## 2. 컴파일러(Compiler)의 역할

### 컴파일러와 트랜스파일러의 차이점

**컴파일러**는 한 언어의 코드를 다른 언어(주로 더 낮은 수준의 언어)로 변환하는 프로그램이다. 전통적인 의미에서 컴파일러는 C나 Java 같은 언어를 기계어나 바이트코드로 변환한다. 하지만 JavaScript 생태계에서는 이 용어가 조금 다르게 사용된다.

> (👨🏻‍🏫 : 사실 JavaScript 세계에서는 컴파일러와 트랜스파일러라는 용어가 종종 혼용되기도 한답니다. 하지만 엄밀히 말하면 차이가 있어요!)

JavaScript에서 **컴파일러**는 주로 코드를 브라우저가 이해할 수 있는 형태로 변환하는 도구를 가리킨다. 예를 들어, TypeScript 컴파일러(tsc)는 TypeScript 코드를 JavaScript로 변환하고, SWC는 최신 JavaScript를 이전 버전으로 변환하면서 최적화까지 수행한다.

```tsx
*// TypeScript 코드*
function greet(name: string): string {
  return `Hello, ${name}!`;
}

*// 컴파일 후 JavaScript 코드*
function greet(name) {
  return "Hello, " + name + "!";
}
```

## 3. 번들러(Bundler)의 중요성

### 번들러가 필요한 이유

**번들러**는 여러 JavaScript 파일과 그 의존성을 하나 또는 여러 개의 최적화된 파일로 묶는 도구다. 모듈 시스템(import/export)을 사용하면 코드를 여러 파일로 나눌 수 있지만, 이 파일들을 브라우저에서 효율적으로 로드하려면 번들링 과정이 필요하다.

> (👨🏻‍🏫 : 번들러 없이 수십, 수백 개의 파일을 각각 로드한다고 상상해보세요! 네트워크 요청이 너무 많아져서 성능이 크게 저하될 거예요. 😱 차라리 하나의 파일로 합치고, 해당 파일 내에서의 기능을 여러개로 나누는 게 더 효율적일 거예요 이를 증명하듯, npm run build 시에 나온 파일을 보면 되게 작답니다! )

### 주요 번들러 비교

1. **Webpack**: 가장 널리 사용되는 번들러로, 다양한 플러그인과 로더를 통해 거의 모든 종류의 자산을 처리할 수 있다.
2. **Rollup**: 트리 쉐이킹에 강점이 있어 라이브러리 개발에 적합하다.
3. **Parcel**: 설정이 거의 필요 없는 제로 설정 번들러로, 빠른 개발에 적합하다.
4. **esbuild**: Go로 작성되어 매우 빠른 속도를 자랑하는 번들러다.
5. **Vite**: Rollup 기반으로 개발 시에는 네이티브 ES 모듈을 활용해 빠른 개발 경험을 제공한다. (최근 React는 이를 추천합니다.)

아래는 webpack의 설정 파일입니다.

```jsx
*// webpack.config.js 예시*
module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
        },
      },
    ],
  },
};
```

출처: [Webpack 공식 문서](https://webpack.js.org/concepts/)

## 4. React 프로젝트에서의 활용

### Create React App의 내부 구조

Create React App(CRA)은 React 애플리케이션을 쉽게 시작할 수 있게 해주는 도구다.(현재는 유지보수 상태로, Vite를 사용하기를 권장하고 있다.) CRA는 내부적으로 Webpack, Babel, ESLint 등을 사용하여 개발 환경을 구성한다.

```bash
npx create-react-app my-app
cd my-app
npm start
```

CRA에서는 이러한 도구들의 설정이 추상화되어 있어 개발자가 직접 설정할 필요가 없다. 하지만 `npm run eject` 명령을 실행하면 모든 설정 파일을 볼 수 있다.

> (👨🏻‍🏫 : 결국엔 이 여러 작업들을 한 줄의 명령어로 세팅을 해주다보니, 처음에 개발을 시작할 때는 이러한 다른 역할들을 하는 툴들이 내장되어있는지도 모를 수 있어요! )

### Next.js와 같은 프레임워크의 접근 방식

Next.js는 자체적인 빌드 시스템을 가지고 있으며, 최근에는 Turbopack이라는 새로운 번들러를 도입했다. Turbopack은 Webpack보다 훨씬 빠른 성능을 제공하며, 증분 빌드를 통해 대규모 애플리케이션에서도 빠른 개발 경험을 제공한다.

```bash
npx create-next-app@latest --example with-turbopack
```

## 5. 최신 트렌드와 미래 전망

### 빠른 개발 경험을 위한 도구들

최근 JavaScript 빌드 도구의 트렌드는 **속도**와 **개발자 경험**에 초점을 맞추고 있다. Vite, esbuild, Turbopack 등의 도구는 기존 도구보다 훨씬 빠른 빌드 속도를 제공한다.

> (👨🏻‍🏫 : 예전에는 조금 큰 규모의 프로젝를 한다면, webpack 빌드가 끝날 때까지 커피 한 잔 마시고 올 수 있었는데, 요즘 도구들은 눈 깜짝할 사이에 빌드가 끝나버려요! ☕)

### 모듈 페더레이션과 마이크로 프론트엔드

Webpack 5에서 도입된 **모듈 페더레이션**은 여러 독립적인 빌드 간에 코드를 공유할 수 있게 해준다. 이는 마이크로 프론트엔드 아키텍처를 구현하는 데 큰 도움이 된다.

```jsx
*// webpack.config.js*
module.exports = {
  *// ...*
  plugins: [
    new ModuleFederationPlugin({
      name: 'app1',
      filename: 'remoteEntry.js',
      remotes: {
        app2: 'app2@http://localhost:3002/remoteEntry.js',
      },
      exposes: {
        './Button': './src/Button',
      },
      shared: { react: { singleton: true }, 'react-dom': { singleton: true } },
    }),
  ],
};
```

출처: [Webpack Module Federation](https://webpack.js.org/concepts/module-federation/)

### 웹어셈블리와 네이티브 성능

**웹어셈블리(WebAssembly)**의 등장으로 JavaScript 빌드 도구들도 변화하고 있다. esbuild와 SWC는 각각 Go와 Rust로 작성되어 JavaScript보다 훨씬 빠른 성능을 제공한다. 이러한 도구들은 대규모 프로젝트에서 빌드 시간을 크게 단축시킬 수 있다.

> (👨🏻‍🏫 : 네이티브 언어로 작성된 도구들이 JavaScript로 작성된 도구보다 10배 이상 빠른 경우도 있답니다! 정말 놀랍죠? 최근 Typescript가 JavaScript 에서 Go 로 네이티브 포트를 발표했거든요 이 또한 10배 빨라졌답니다. 제 글을 보시면 알 수 있어요!)

JavaScript 생태계는 계속해서 발전하고 있으며, 트랜스파일러, 컴파일러, 번들러도 함께 진화하고 있다. 이러한 도구들을 이해하고 적절히 활용하면 더 효율적인 개발 경험과 더 나은 성능의 애플리케이션을 만들 수 있다.

최신 도구들은 개발자 경험을 크게 향상시키고 있지만, 기본 개념은 여전히 중요하다. 트랜스파일러는 코드를 변환하고, 컴파일러는 코드를 실행 가능한 형태로 만들며, 번들러는 코드를 최적화하여 묶는다. 이 세 가지 도구의 조합이 현대 웹 개발의 기반을 이루고 있다.

> 🙇🏻 글 내에 틀린 점, 오탈자, 비판, 공감 등 모두 적어주셔도 됩니다. 감사합니다..! 🙇🏻
