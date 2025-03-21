## React Native에서 svg 파일을 쓰려면 왜이리 번거로울까?
모바일 앱 개발을 하다 보면 아이콘이나 로고 같은 벡터 이미지를 사용할 일이 많다. 웹에서는 SVG 파일을 간단히 import해서 사용할 수 있지만, 리액트 네이티브에서는 왜 이렇게 복잡한 설정이 필요할까? 오늘은 이 번거로운 과정의 이유와 해결 방법에 대해 알아보자.

## 💡급하신 분들을 위해서 결론 먼저!

1. 리액트 네이티브는 기본적으로 SVG 파일 형식을 지원하지 않는다
2. 네이티브 환경에서 SVG를 렌더링하려면 별도의 라이브러리가 필요하다
3. 타입스크립트 사용 시 SVG 파일에 대한 타입 정의가 필요하다
4. Metro 번들러 설정을 통해 SVG 파일을 컴포넌트로 변환해야 한다
5. 이 모든 과정은 네이티브 플랫폼과 웹 플랫폼의 근본적인 차이 때문이다

## 1. 리액트 네이티브와 SVG의 불편한 관계

### 웹과 네이티브의 근본적 차이

리액트 네이티브는 웹 기술을 사용해 모바일 앱을 개발하지만, 실제로는 네이티브 컴포넌트로 변환되어 실행된다. 웹 브라우저는 SVG를 기본적으로 지원하지만, iOS와 Android의 네이티브 환경은 그렇지 않다.

> "(👨🏻‍🏫 : 웹에서는 그냥 `<img src="icon.svg" />` 하면 끝인데, 네이티브에서는 그런 거 없답니다. 네이티브 플랫폼에서는 SVG를 직접 해석할 수 있는 기능이 내장되어 있지 않거든요!)"
> 

### SVG 렌더링의 복잡성

**SVG**는 XML 기반의 벡터 그래픽 형식이다. 웹에서는 브라우저가 이 XML을 해석하여 그래픽으로 렌더링하지만, 리액트 네이티브에서는 이 과정을 직접 구현해야 한다. 이를 위해 **react-native-svg** 라이브러리가 필요하다.

```jsx
*// 웹에서는 이렇게 간단하게*
import iconSvg from './icon.svg';
<img src={iconSvg} />

*// 리액트 네이티브에서는 이런 과정이 필요(더 간단한 방법은 아래에 나옴)*
import Svg, { Path } from 'react-native-svg';
<Svg width="100" height="100" viewBox="0 0 100 100">
  <Path d="M10,10 L90,90" stroke="black" />
</Svg>
```

## 2. 번거로운 설정의 이유

### Metro 번들러의 한계

리액트 네이티브는 **Metro** 번들러를 사용하여 JavaScript 코드와 에셋을 패키징한다. 기본적으로 Metro는 SVG 파일을 컴포넌트로 변환하는 기능을 제공하지 않는다. 따라서 **react-native-svg-transformer**를 사용하여 이 기능을 추가해야 한다.

> "(👨🏻‍🏫 : Metro 번들러는 기본적으로 이미지 파일은 그냥 URL로 가져오고, JS 파일은 실행 가능한 코드로 가져오는데요. SVG는 이 둘의 중간 형태라서 특별한 처리가 필요합니다!)"
> 

### metro.config.js 설정의 필요성

Metro 번들러에게 SVG 파일을 어떻게 처리해야 하는지 알려주기 위해 **metro.config.js** 파일을 수정해야 한다. **React Native**는 `Metro` 라는 번들러를 사용하기에, 이 번들러의 설정을 바꿔줘야만 합니다. 

```jsx
*// metro.config.js - React Native CLI 최신 버전*
const { getDefaultConfig } = require("metro-config");

module.exports = (async () => {
  const {
    resolver: { sourceExts, assetExts }
  } = await getDefaultConfig();
  
  return {
    transformer: {
      babelTransformerPath: require.resolve("react-native-svg-transformer")
    },
    resolver: {
      assetExts: assetExts.filter(ext => ext !== "svg"),
      sourceExts: [...sourceExts, "svg"]
    }
  };
})();
```

## 3. 타입스크립트와의 불편한 동거

### 타입 정의의 필요성

더군다나 타입스크립트를 사용할 때는 SVG 파일을 임포트할 때 타입 오류가 발생한다. 타입스크립트는 기본적으로 SVG 파일이 어떤 타입인지 알지 못하기 때문에, 이를 위한 타입 정의가 필요하다.

> "(👨🏻‍🏫 : 타입스크립트는 '이게 뭐지? SVG 파일이라고? 그게 뭔데?' 하고 물어봅니다. 그래서 우리가 '이건 리액트 컴포넌트야~' 라고 알려줘야 해요!)"
> 

### declarations.d.ts 파일 생성

SVG 파일을 리액트 컴포넌트로 인식하도록 타입 정의를 추가해야 한다.

```jsx
*// declarations.d.ts (파일 명은 상관 없습니다. svg.d.ts 로 관리해도 됩니다.)*
declare module "*.svg" {
  import React from 'react';
  import { SvgProps } from "react-native-svg";
  const content: React.FC<SvgProps>;
  export default content;
}
```

> "(👨🏻‍🏫 : 즉, svg 파일이 인식되면 이 파일을 갖다가 react-native-svg의 SvgProps로 바꿔서 React 컴포넌트처럼 쓰겠다~ 는 의미입니다.)"
> 

## 4. 성능과 최적화 문제

### SVG 렌더링 성능

리액트 네이티브에서 SVG를 렌더링하는 것은 생각보다 비용이 많이 든다. 특히 복잡한 SVG나 많은 수의 SVG를 렌더링할 때 성능 이슈가 발생할 수 있다.

> "(👨🏻‍🏫 : 500개의 SVG를 한 번에 렌더링하면 9-10초가 걸린다는 테스트 결과도 있어요! 이건 사용자 경험에 매우 좋지 않죠.)"
> 

### 대안과 최적화 방법

**React Native**는 `Expo`로 시작하냐, `React Native CLI` 로 시작하냐에 따라 설정이 달라진다. 이는 **React Native**를 `Expo`라는 걸 거쳐서 실행할 건지, 자체적인 방법을 통해 실행할 건지를 의미한다. `Expo`를 사용할 경우웨는 **Expo Image**와 같은 라이브러리를 사용하여 SVG 렌더링 성능을 개선하는 방법이 등장하고 있다. 이 방법은 SVG를 Base64 URI로 변환하여 네이티브 이미지 컴포넌트로 렌더링한다.

```jsx
*// Expo Image를 사용한 SVG 렌더링*
import { Image } from 'expo-image';
import { convertSvgToBase64 } from './utils';

const base64Svg = convertSvgToBase64(mySvgXml);
<Image source={{ uri: `data:image/svg+xml;base64,${base64Svg}` }} />
```

## 5. 그래도 SVG를 사용해야 하는 이유

### 해상도 독립성

**SVG**는 벡터 기반이므로 어떤 화면 크기나 해상도에서도 선명하게 표시된다. 이는 다양한 기기에서 실행되는 모바일 앱에 매우 중요한 장점이다.

> "(👨🏻‍🏫 : PNG로 아이콘 만들면 2x, 3x 버전 다 따로 만들어야 하는데, SVG는 하나만 있으면 끝이랍니다! 정말 편리하죠?)"
> 

### 파일 크기와 커스터마이징

SVG 파일은 일반적으로 비트맵 이미지보다 파일 크기가 작고, 색상이나 크기를 동적으로 변경할 수 있다는 장점이 있다.

```jsx
*// SVG 컴포넌트의 색상과 크기를 props로 쉽게 변경 가능*
import IconSvg from './icon.svg';

<IconSvg width={24} height={24} color="#FF0000" />
```

## 6. React Native CLI와 Expo CLI 설정 방법 비교

### React Native CLI 설정 방법

React Native CLI를 사용하는 경우 다음과 같은 단계로 SVG를 설정할 수 있다:

1. 필요한 패키지 설치

```dart
npm install react-native-svg
npm install --save-dev react-native-svg-transformer
cd ios && pod install
```

1. metro.config.js 파일 수정

```dart
const { getDefaultConfig } = require("metro-config");

module.exports = (async () => {
  const {
    resolver: { sourceExts, assetExts }
  } = await getDefaultConfig();
  
  return {
    transformer: {
      babelTransformerPath: require.resolve("react-native-svg-transformer")
    },
    resolver: {
      assetExts: assetExts.filter(ext => ext !== "svg"),
      sourceExts: [...sourceExts, "svg"]
    }
  };
})();
```

1. TypeScript 사용 시 declarations.d.ts 파일 생성

```dart
declare module "*.svg" {
  import React from 'react';
  import { SvgProps } from "react-native-svg";
  const content: React.FC<SvgProps>;
  export default content;
}
```

### Expo CLI 설정 방법

Expo를 사용하는 경우 설정이 더 간단하다:

1. 필요한 패키지 설치

`bashexpo install react-native-svg`

1. SVG 파일 사용 방법은 React Native CLI와 동일하다

## 7. 최종 정리: React Native에서 SVG 사용하기

### 도식화: React Native에서 SVG 사용 과정

![](https://velog.velcdn.com/images/kyujenius/post/7409c8a2-044f-47ba-acf6-6278af65e49d/image.png)



### 사용 방법 요약

1. **설치 및 설정**
    - React Native CLI: react-native-svg, react-native-svg-transformer 설치 및 metro.config.js 수정
    - Expo CLI: expo install react-native-svg만 실행
2. **TypeScript 사용 시**
    - declarations.d.ts 파일에 SVG 모듈 타입 정의 추가
3. **SVG 파일 사용**
    - SVG 파일을 React 컴포넌트처럼 import
    - width, height, color 등의 props로 스타일링 가능
4. **성능 최적화**
    - 많은 SVG를 사용할 경우 성능 이슈 고려
    - 필요시 Base64 인코딩 방식 등 대안 검토

이처럼 리액트 네이티브에서 SVG를 사용하는 것은 번거롭지만, 그 장점을 생각하면 충분히 가치 있는 작업이다. 설정이 복잡한 이유는 네이티브 플랫폼과 웹 플랫폼의 근본적인 차이에서 비롯되며, 이러한 설정을 통해 우리는 두 플랫폼의 장점을 모두 활용할 수 있다.

> 🙇🏻 글 내에 틀린 점, 오탈자, 비판, 공감 등 모두 적어주셔도 됩니다. 감사합니다..! 🙇🏻
>
