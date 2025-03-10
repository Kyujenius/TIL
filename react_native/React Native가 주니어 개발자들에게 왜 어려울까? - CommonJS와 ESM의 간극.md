## React Native의 한계: CommonJS 모듈 시스템과의 씨름

"이게 왜 안 되지?" 라는 말을 하루에도 수십 번 중얼거리며 터미널 에러 메시지를 노려보고 있다면, 당신은 아마도 React Native와 최신 자바스크립트 생태계 사이의 간극과 싸우고 있을 가능성이 높다. (~~그게 나다.. ㅎ\_ㅎ~~) 특히 그 중심에는 CommonJS라는, 마치 고대 유물처럼 React Native가 고수하고 있는 모듈 시스템이 있다.

> (👨🏻‍🏫 : 처음에는 CommonJS 는 JS 의 대명사 같은 느낌인 줄 알았고, ESM은 조금 최신 JS 겠구나~ 싶었습니다. 하하, 얼마나 순진했던가요... 안다고 크게 나아질까 싶으면서도... 그래도 왜 맞는지는 알고 맞는 게 좋지 않을까 싶어서 잘 정리해봤습니다.)

오늘은 React Native가 여전히 CommonJS에 의존하면서 발생하는 다양한 문제점과 그 해결 방법에 대해 깊이 있게 살펴보려 한다. 이 글을 통해 당신이 겪고 있는 많은 "이상한" 오류들의 근본 원인을 이해하고, 더 효율적인 해결책을 찾는 데 도움이 되길 바란다.

## 💡급하신 분들을 위해서 결론 먼저!

1. `React Native`는 여전히 `CommonJS` 모듈 시스템을 사용하며, 이는 최신 ESM 기반 라이브러리와 호환성 문제를 일으킨다.
2. `Metro` 번들러의 제한된 기능은 최신 자바스크립트 생태계와의 통합을 어렵게 만든다.
3. 이 문제를 해결하기 위한 방법으로는 `babel-plugin-transform-modules`, `metro-react-native-babel-preset` 커스터마이징, 또는 `ESM` 지원 라이브러리 사용이 있다.
4. `React Native`의 아키텍처 변화와 함께 `Hermes` 엔진의 발전으로 점진적인 개선이 이루어지고 있다.
5. 개발자는 라이브러리 선택 시 `CommonJS` 호환성을 확인하고, 필요한 경우 변환 도구를 활용해야 한다.

## CommonJS와 ESM: 두 세계의 충돌

자바스크립트 모듈 시스템의 역사는 복잡하다. 브라우저에서 `모듈 시스템`이 없던 시절, `Node.js`는 `CommonJS`라는 모듈 시스템을 도입했다. 이 시스템은 `require()`와 `module.exports`를 사용하여 모듈을 가져오고 내보내는 방식이다.

```jsx
*// CommonJS 방식*
const React = require('react');
module.exports = MyComponent;

```

반면, 최신 자바스크립트는 `ECMAScript Modules(ESM)`이라는 표준 모듈 시스템을 채택했으며, 이는 `import`와 `export` 구문을 사용한다.

```jsx
*// ESM 방식*
import React from 'react';
export default MyComponent;

```

## CommonJS가 쓰이는 경우 vs ESM이 쓰이는 경우

`CommonJS`와 `ESM`은 각각 다른 환경과 요구사항에 맞게 사용된다.

### CommonJS가 쓰이는 경우

`CommonJS`는 주로 다음과 같은 상황에서 사용된다:

- **서버 사이드 Node.js 개발**: Node.js의 초기부터 사용된 모듈 시스템으로, 서버 개발에 최적화되어 있다.
- **빌드 프로세스나 번들링이 필요 없는 앱**: 간단한 구조의 애플리케이션에 적합하다.
- **프론트엔드 라이브러리와 프레임워크를 광범위하게 사용하지 않는 앱**: 복잡한 의존성 관리가 필요 없는 경우에 적합하다.
- **명령줄 도구와 유틸리티**: 간단한 스크립트 작성에 적합하다.
- **API 서버와 백엔드 서비스**: 서버 환경에서의 안정성이 중요한 경우에 사용된다.

> (👨🏻‍🏫 : 저는 주로 오래된 Node.js 프로젝트나 백엔드 서비스를 개발할 때 CommonJS를 사용한답니다. 익숙하고 안정적이니까요!)

### ESM이 쓰이는 경우

ESM은 다음과 같은 상황에서 주로 사용된다:

- **최신 브라우저 환경의 클라이언트 사이드 개발**: 브라우저에서 네이티브 지원을 받는다.
- **프론트엔드 라이브러리와 프레임워크를 광범위하게 사용하는 앱**: React, Vue.js, Angular 등의 최신 프레임워크와 함께 사용하기 적합하다.
- **동적 임포트가 필요한 앱**: 코드 스플리팅과 지연 로딩을 활용할 수 있다.
- **단일 페이지 애플리케이션(SPA)**: 모듈화된 구조와 비동기 로딩이 중요한 경우에 적합하다.
- **프로그레시브 웹 앱(PWA)**: 최신 웹 기술을 활용하는 앱에 적합하다.
- **새로운 프로젝트**: 미래 호환성을 고려할 때 ESM이 권장된다.

### CommonJS와 ESM의 차이점: 버전에 따른 선형적인 것인가, 완전히 다른 것인가?

`CommonJS`와 `ESM`은 **`완전히 다른 모듈 시스템`**이다. 단순히 버전이 업그레이드된 관계가 아니라, 설계 철학과 작동 방식이 근본적으로 다르다.

### 근본적인 차이점

1. **로딩 방식**: `CommonJS`는 `동기적(synchronous)`으로 모듈을 로드하는 반면, `ESM`은 `비동기적(asynchronous)`으로 로드한다. 이는 실행 환경과 성능에 큰 영향을 미친다.
2. **구문 차이**: `CommonJS`는 `require()`와 `module.exports`를 사용하고, ESM은 `import`와 `export` 구문을 사용한다.
3. **정적 분석 가능성**: `ESM`은 정적 구조를 가지고 있어 빌드 타임에 모듈 간의 의존 관계를 파악할 수 있다. 반면 `CommonJS`는 런타임에서만 모듈 관계를 파악할 수 있다.

```jsx
// CommonJS - 동적 로딩 가능
const moduleName = "math";
const math = require(`./utils/${moduleName}`);

// ESM - 정적 경로만 가능
import math from "./utils/math.js"; // 동적 경로 불가능
```

1. **트리 쉐이킹**: `ESM`은 정적 분석이 가능하기 때문에 트리 쉐이킹을 통해(사용하지 않는 코드 제거)이 가능하지만, CommonJS는 그렇지 않다. (동적으로 작용하기에)
2. **브라우저 지원**: `ESM`은 최신 브라우저에서 네이티브로 지원되지만, CommonJS는 번들러를 통해 변환해야 한다.

> (👨🏻‍🏫 : 두 시스템은 마치 자동차와 비행기의 차이와 같답니다. 둘 다 이동 수단이지만, 작동 원리와 사용 환경이 완전히 다르죠!)

## 호환성과 전환

두 시스템은 완전히 다르지만, Node.js에서는 두 시스템 간의 상호 운용성을 위한 방법을 제공한다:

- ESM에서 CommonJS 모듈 사용: 일반적인 `import` 구문으로 가능하다.
- CommonJS에서 ESM 모듈 사용: 동적 `import()` 함수를 통해 비동기적으로 가능하다.

```jsx
// ESM에서 CommonJS 모듈 사용
import add from "./add.cjs";

// CommonJS에서 ESM 모듈 사용
(async function () {
  const add = (await import("./index.mjs")).default;
  add(1, 2);
})();
```

Node.js 23부터는 특정 조건 하에서 `require()`를 통해 ESM 모듈을 로드할 수 있게 되었지만, 이는 실험적 기능이다.

결론적으로, CommonJS와 ESM은 단순한 버전 차이가 아닌 완전히 다른 모듈 시스템이며, 각각의 장단점과 사용 사례가 있다. 현재 자바스크립트 생태계는 ESM으로 점진적으로 이동하고 있지만, CommonJS는 여전히 널리 사용되고 있으며 앞으로도 상당 기간 지원될 것이다.

## React Native의 선택

React Native는 Metro라는 자체 번들러를 사용하며, 이 Metro는 **여전히 CommonJS를 기본 모듈 시스템으로 사용**한다. 이는 React Native가 2015년에 출시되었을 때 ESM이 아직 널리 채택되지 않았던 시기적 배경이 있다.

> (👨🏻‍🏫 : 근데, 저희는 RN에서도 import, export 를 쉽게 쓰는데 어떻게 된걸까요? ESM 과 CommonJS를 같이 쓰는 걸까요?)

### 개발은 ESM으로, 번들링은 CommonJS로?

Metro의 내부 변환 과정
우리가 작성한 `ESM 문법(import/export)`을 내부적으로 `CommonJS`로 변환해서 처리한다. 이 과정은 다음과 같이 진행된다:

1. 우리는 현대적인 ESM 문법으로 코드를 작성:

```jsx
import React from "react";
export default MyComponent;
```

2. Metro는 Babel을 사용해서 이 `ESM 문법`을 CommonJS로 변환한다:

```jsx
const React = require("react");
module.exports = MyComponent;
```

3. 변환된 `CommonJS` 코드가 번들링되어 최종적으로 앱에서 실행된다. 이 과정에서 `@react-native/babel-preset`이 중요한 역할을 한다. 이 프리셋은 `@babel/plugin-transform-modules-commonjs`를 포함하고 있어서 `ESM 문법`을 자동으로 `CommonJS`로 변환해준다.

### 왜 이런 방식을 사용하는 걸까?

Metro가 이런 방식을 사용하는 이유는 다음과 같아요:

`Node.js` 환경과의 호환성: `Metro`는 `Node.js` 환경에서 실행되며, `Node.js`는 `CommonJS`를 기본 모듈 시스템으로 사용했어요.

네이티브 통합: `React Native`는 `JavaScript` 코드와 `네이티브 코드(iOS, Android) 간의 통합`이 필요하며, 이 과정에서 `CommonJS`가 더 안정적인 선택이었어요. (왜 그럴까요?)

### Metro 번들러의 제한사항

React Native의 번들링 시스템인 Metro는 웹 생태계에서 널리 사용되는 Webpack, Vite, Rollup 등과 비교했을 때 몇 가지 중요한 제한사항을 가지고 있다.

### 제한된 ESM 지원

Metro는 기본적으로 ESM 구문을 지원하지만, 이를 CommonJS로 변환하여 처리한다. 이 과정에서 ESM의 일부 고급 기능들이 제대로 지원되지 않는 경우가 있다.

```jsx
*// 이런 동적 import는 Metro에서 문제가 될 수 있다*
const module = await import(`./locales/${language}.js`);

```

### 조건부 내보내기 미지원

최신 Node.js와 웹 생태계에서는 package.json에서 "exports" 필드를 통해 조건부 내보내기를 지원한다. 이를 통해 동일한 패키지가 환경에 따라 다른 버전의 코드를 제공할 수 있다.

```json
*// package.json*
{
  "exports": {
    "import": "./esm/index.js",
    "require": "./cjs/index.js",
    "react-native": "./native/index.js"
  }
}

```

하지만 Metro는 이러한 조건부 내보내기를 완전히 지원하지 않아, 최신 라이브러리를 사용할 때 문제가 발생한다.

출처: [Metro 공식 문서](https://facebook.github.io/metro/)

## 실제 개발 과정에서 마주치는 문제들

CommonJS 기반 시스템을 사용하면서 발생하는 실제 문제들을 살펴보자.

### 최신 라이브러리 호환성 문제

점차 많은 최신 자바스크립트 라이브러리들이 ESM만 지원하거나 ESM을 기본으로 제공하면서, React Native 프로젝트에 통합하기 어려워졌다.

```bash
Error: Unable to resolve module 'modern-esm-only-library' from 'App.js'

```

이런 오류 메시지는 React Native 개발자에게 너무나 친숙하다. 🥲

### 테스트 도구 통합의 어려움

Vitest와 같은 최신 테스트 도구들은 ESM을 기본으로 사용하며, React Native 프로젝트에 통합하려면 추가적인 설정이 필요하다.

```jsx
*// vitest.config.js*
export default {
  test: {
    deps: {
      inline: ["react-native"]
    }
  }
}

```

하지만 이런 방식으로도 완벽한 호환성을 보장하기 어렵다.

### 타입스크립트 설정 복잡성

타입스크립트를 사용할 때도 모듈 시스템 간의 차이로 인한 복잡성이 증가한다.

```json
*// tsconfig.json*
{
  "compilerOptions": {
    "module": "ESNext",
    "moduleResolution": "node",
    "esModuleInterop": true
  }
}

```

이러한 설정은 타입스크립트 컴파일러가 ESM 구문을 이해하도록 하지만, 실행 시에는 여전히 Metro가 CommonJS로 변환하는 과정이 필요하다.

## 해결 방법과 대안

React Native에서 CommonJS와 관련된 문제를 해결하기 위한 몇 가지 방법을 살펴보자.

### Babel 플러그인 활용

`babel-plugin-transform-modules` 같은 플러그인을 사용하여 ESM 전용 라이브러리를 CommonJS로 변환할 수 있다.

> (👨🏻‍🏫 : 특정 라이브러리를 사용해야할 때, 항상 설치에서 끝나는 게 아니라, 추가적인 작업을 해야하는 이유가 이런 이유였던 것이다..!)

```jsx
*// babel.config.js*
module.exports = {
  plugins: [
    ['babel-plugin-transform-modules', {
      'modern-esm-library': {
        transform: 'modern-esm-library/dist/cjs/index.js',
      },
    }],
  ],
};

```

출처: [babel-plugin-transform-modules](https://www.npmjs.com/package/babel-plugin-transform-modules)

### Metro 설정 커스터마이징

Metro 설정을 커스터마이징하여 특정 패키지를 처리하는 방법을 변경할 수 있다.

```jsx
*// metro.config.js*
module.exports = {
  resolver: {
    extraNodeModules: {
      'esm-only-package': require.resolve('./path-to-commonjs-version')
    }
  }
};

```

출처: [Metro 설정 문서](https://facebook.github.io/metro/docs/configuration)

### CommonJS 호환 라이브러리 선택

가능하다면, CommonJS를 지원하는 대체 라이브러리를 선택하는 것도 좋은 방법이다. npm 패키지를 설치하기 전에 해당 패키지가 CommonJS를 지원하는지 확인하자.

```jsx
*# package.json을 확인하여 "type": "module"이 없는지 확인*
npm view some-package

```

## React Native의 미래와 ESM

React Native 팀도 이러한 문제를 인식하고 있으며, 장기적으로는 ESM 지원을 개선하기 위한 노력을 기울이고 있다.

### 새로운 아키텍처

React Native의 새로운 아키텍처인 "New Architecture"는 내부 구조를 현대화하는 과정의 일부이며, 이는 장기적으로 모듈 시스템 개선으로 이어질 수 있다.

### Hermes 엔진의 발전

React Native의 자바스크립트 엔진인 Hermes도 지속적으로 발전하고 있으며, ESM 지원을 개선하기 위한 작업이 진행 중이다.

> (👨🏻‍🏫 : Hermes가 처음 나왔을 때는 기능이 제한적이었지만, 이제는 점점 더 강력해지고 있답니다. 인내심을 가지고 기다려 봅시다!)

### 커뮤니티 주도 해결책

React Native 커뮤니티에서도 이 문제를 해결하기 위한 다양한 도구와 방법을 개발하고 있다. 예를 들어, `react-native-esbuild`와 같은 프로젝트는 Metro 대신 esbuild를 사용하여 더 나은 ESM 지원을 제공하려는 시도이다.

> (👨🏻‍🏫 : 현재는 토스 개발자 이신 거 같은데 해당 라이브러리를 만드는 과정을 들여다보시면, 진짜 대단한 노력이 들어간다는 사실을 알 수 있습니다….! 되려, 이 분은 토스에서 다음과 같은 방식의 성공 사례를 보고서, 과감하게 시도해보았다고 하네요. )

출처: [react-native-esbuild](https://github.com/leegeunhyeok/react-native-esbuild)

## 결론 및 실용적인 조언

React Native의 CommonJS 의존성은 현대 자바스크립트 생태계와의 통합을 어렵게 만드는 요소 중 하나이다. 그러나 이러한 제한 속에서도 효과적으로 개발하기 위한 방법들이 존재한다.

1. **RN으로 개발 중 라이브러리 선택 시 CommonJS 호환성 확인**: 새로운 라이브러리를 도입할 때 CommonJS 지원 여부를 미리 확인하자.
2. **변환 도구 활용**: ESM 전용 라이브러리를 사용해야 할 경우, Babel 플러그인이나 기타 변환 도구를 활용하자.
3. **Metro 설정 최적화**: 프로젝트의 요구사항에 맞게 Metro 설정을 최적화하여 호환성 문제를 최소화하자.
4. **커뮤니티 동향 주시**: React Native 커뮤니티와 공식 팀의 발표를 주시하여 ESM 지원 개선에 관한 소식을 놓치지 말자.

React Native의 CommonJS 의존성은 단기간에 해결되기 어려운 문제이지만, 개발자로서 이러한 제한을 이해하고 적절히 대응한다면 여전히 생산적인 개발이 가능하다. 모바일 앱 개발의 여정에서 이러한 도전은 우리를 더 나은 개발자로 성장시키는 기회가 될 수 있다. 💪

> 🙇🏻 글 내에 틀린 점, 오탈자, 비판, 공감 등 모두 적어주셔도 됩니다. 감사합니다..! 🙇🏻
