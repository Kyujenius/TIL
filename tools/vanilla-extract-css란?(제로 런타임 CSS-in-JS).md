![](https://velog.velcdn.com/images/kyujenius/post/67d69d97-84d8-4dd4-b8ab-5b84e5639e62/image.png)

웹 개발자라면 누구나 CSS를 사용해 본 경험이 있을 것이다. 그리고 대부분은 CSS를 작성하면서 여러 가지 불편함을 느꼈을 것이다. 클래스 이름 충돌, 스타일 유지보수의 어려움, JS와의 연동 불가,그리고 타입 안전성 부재 등 다양한 문제점들이 존재한다.

> (👨🏻‍🏫 : 이를 해결하기 위해, module.css를 적용하거나, BEM 방식 등을 적용해 작성하면 클래스 이름 충돌은 없었답니다. 하지만 그 긴 클래스 이름들... 정말 지겹지 않나요?)
> 

이러한 문제를 해결하기 위해 다양한 `CSS-in-JS` 라이브러리들이 등장했다. `Styled Components`, `Emotion` 등이 대표적인 예시이다. 그러나 이러한 라이브러리들은 런타임 오버헤드라는 또 다른 문제를 가지고 있었다.

그리고 이제 우리에게는 **`Vanilla Extract CSS`**라는 새로운 대안이 있다. 타입스크립트로 작성하는 제로 런타임 스타일시트, 이것이 바로 Vanilla Extract CSS의 핵심이다.

## 💡급하신 분들을 위해서 결론 먼저!

1. Vanilla Extract는 타입스크립트로 CSS를 작성할 수 있게 해주는 (CSS-in-JS) 라이브러리이다.
2. 기존의 CSS 완 달리, 빌드 타임에 정적 CSS 파일을 생성하여 런타임 오버헤드가 없다.
3. Typescript를 사용해 타입 안전성, 로컬 스코프, 테마 지원 등 다양한 기능을 제공한다.
4. Styled Components와 같은 런타임 CSS-in-JS 라이브러리보다 성능이 우수하다.
5. 다른 제로 런타임 라이브러리로는 Linaria, Astroturf 등이 있다.

## 1. CSS-in-JS의 탄생 배경

CSS-in-JS는 2014년 Facebook 개발자인 Christopher Chedeau(일명 Vjeux)의 발표에서 처음 소개됐다. 이 개념은 Facebook이 겪던 CSS 관리의 어려움을 해결한 사례를 바탕으로 발전하게 됐다.

### CSS의 역사적 배경

웹의 초기 시절을 살펴보면 CSS-in-JS의 등장 배경을 이해할 수 있다:

1. 1990년대 초, HTML이 탄생했을 때는 CSS가 없었다. 문서를 표현하기 위해 HTML만 사용했다.
2. 디자인에 대한 요구가 커지면서 inline-style 방식으로 스타일을 적용하기 시작했다.
3. 1996년, HTML에서 스타일만 분리한 CSS가 고안됐다.
4. 웹이 복잡해지고 동적 기능 요구가 증가하면서 HTML과 CSS만으로는 모든 스타일을 제어하기 어려워졌다.

### CSS의 문제점
![](https://velog.velcdn.com/images/kyujenius/post/4415b1ed-1612-4f14-8544-73cafd291c37/image.png)

Vjeux는 CSS를 작성할 때 다음과 같은 어려움을 지적했다:

- **Global namespace**: 모든 스타일이 전역 공간에 선언되어 중복되지 않는 클래스 이름을 적용해야 하는 문제가 있었다. (BEM 기법이나, module.css 로 이를 해결하려고 했었죠.. )
- **Dependencies**: CSS 간의 의존 관계를 관리하기 어려운 문제가 있었다.
- **Dead Code Elimination**: 기능 추가, 변경, 삭제 과정에서 불필요한 CSS를 제거하기 어려웠다.
- **Minification**: 클래스 이름의 최소화 문제가 있었다.
- **Sharing Constants**: JavaScript와 CSS 사이에 상태 값을 공유할 수 없었다.
- **Non-deterministic Resolution**: CSS 로드 순서에 따라 스타일 우선 순위가 달라지는 문제가 있었다.
- **Isolation**: CSS와 JS가 분리된 탓에 상속에 따른 격리가 어려웠다.

### CSS-in-JS의 발전

CSS-in-JS는 이러한 문제들을 해결하기 위해 JavaScript 코드에서 CSS를 작성하는 방식으로 등장했다. 이후 개념이 발전하면서 Styled Components, Emotion 등 다양한 라이브러리가 등장했다.

CSS-in-JS의 주된 선택 요소는 "스타일을 얼마나 동적으로 작성할 수 있는가"였다. 즉, JavaScript 변수를 사용할 수 있는지, 사용할 수 있다면 그 범위는 어디까지인지가 중요한 기준이 됐다.

이러한 방식은 React, Vue, Angular와 같은 모던 자바스크립트 라이브러리의 인기와 함께 컴포넌트 기반 개발 방법론이 주류가 되면서 더욱 발전하게 됐다.

---

## 2. Vanilla Extract란?

Vanilla Extract는 `타입스크립트` 또는 `자바스크립트`로 `CSS를 작성`할 수 있게 해주는 라이브러리이다. 이 라이브러리의 가장 큰 특징은 **`빌드 타임에 정적 CSS 파일을 생성`**한다는 점이다. 이는 `런타임`에 스타일을 평가하고 주입하는 다른 CSS-in-JS 라이브러리들과 차별화되는 부분이다.

> (👨🏻‍🏫 : 빌드 타임에 CSS 파일을 생성한다고요? 그럼 그냥 CSS 파일을 직접 작성하는 것과 뭐가 다른가요? 🤔 이제부터 그 차이점을 알아봅시다!)
> 

### 주요 특징

1. **타입 안전성**: 타입스크립트를 사용하여 스타일을 작성하므로, 오타나 잘못된 속성 사용을 컴파일 타임에 잡아낼 수 있다.
2. **로컬 스코프**: 클래스 이름이 자동으로 해시되어 충돌을 방지한다.
3. **제로 런타임**: 빌드 타임에 정적 CSS 파일을 생성하므로 런타임 오버헤드가 없다.
4. **테마 지원**: CSS 변수를 활용한 강력한 테마 시스템을 제공한다.

### 기본 사용법

Vanilla Extract를 사용하기 위해서는 먼저 패키지를 설치해야 한다:

```jsx
npm install @vanilla-extract/css
```

스타일은 `.css.ts` 또는 `.css.js` 파일에 정의한다:

```jsx
*// styles.css.ts*
import { style } from '@vanilla-extract/css';

export const buttonStyle = style({
  backgroundColor: 'blue',
  color: 'white',
  padding: '10px 15px',
  borderRadius: '5px'
});
```

그리고 이 스타일을 컴포넌트에서 다음과 같이 사용할 수 있다:

```jsx
*// Button.tsx*
import { buttonStyle } from './styles.css';

const Button = ({ children }) => (
  <button className={buttonStyle}>{children}</button>
);
```

> (👨🏻‍🏫 : 와! 정말 간단하죠? CSS-in-JS의 장점과 일반 CSS의 성능을 모두 가져갈 수 있네요!)
> 

## 3. Vanilla Extract가 나오게 된 계기

웹 개발 생태계에서 CSS 작성 방식은 계속해서 진화해왔다. 초기에는 단순한 CSS 파일을 작성했지만, 프로젝트가 복잡해지면서 Sass, Less와 같은 전처리기가 등장했다. 그리고 컴포넌트 기반 개발이 대세가 되면서 CSS-in-JS 라이브러리들이 인기를 끌기 시작했다.

### CSS-in-JS의 문제점

`Styled Components`나 `Emotion`과 같은 `CSS-in-JS` 라이브러리들은 컴포넌트와 스타일을 함께 관리할 수 있는 편리함을 제공했다. 하지만 이러한 라이브러리들은 **`런타임에 스타일을 평가`하고 `DOM에 주입`**하는 방식으로 작동한다. 이는 다음과 같은 문제를 야기한다:

1. **성능 오버헤드**: 런타임에 스타일을 평가하고 DOM에 주입하는 과정은 성능에 부담을 준다.
2. **번들 크기 증가**: 스타일 관련 코드가 JavaScript 번들에 포함되어 번들 크기가 커진다.
3. **서버 사이드 렌더링 복잡성**: SSR 환경에서 스타일을 올바르게 처리하기 위한 추가 설정이 필요하다.

### 최근 웹 개발 트렌드

Vanilla Extract는 다음과 같은 최근 웹 개발 트렌드를 활용하여 등장했다 :

1. **JavaScript 개발자들의 TypeScript 전환**: 타입 안전성에 대한 요구가 증가했다.
2. **CSS 커스텀 프로퍼티(변수)의 브라우저 지원**: 동적 스타일링을 위한 네이티브 솔루션이 등장했다.
3. **유틸리티 우선 스타일링**: Tailwind CSS와 같은 유틸리티 기반 접근 방식의 인기가 높아졌다.

이러한 배경에서 Vanilla Extract는 타입 안전성, 제로 런타임, 그리고 강력한 테마 시스템을 제공하는 새로운 대안으로 등장하게 되었다.

## 3. 오버헤드가 있고 없음의 차이점

`CSS-in-JS 라이브러리`는 크게 두 가지 유형으로 나눌 수 있다: **`런타임 라이브러리`**와 **`제로 런타임 라이브러리`**. 이 둘의 가장 큰 차이점은 스타일이 언제, 어떻게 처리되는지에 있다.

### 런타임 CSS-in-JS (예: Styled Components)
![](https://velog.velcdn.com/images/kyujenius/post/b935e0f8-de18-4cae-9961-6504662f5063/image.png)

런타임 CSS-in-JS 라이브러리는 다음과 같은 특징을 가진다:

1. **동적 스타일 생성**: 컴포넌트가 렌더링될 때 스타일이 동적으로 생성되고 DOM에 주입된다.
2. **런타임 오버헤드**: JavaScript가 스타일을 처리하고 DOM에 주입하는 과정에서 성능 오버헤드가 발생한다.
3. **번들 크기 증가**: 스타일 처리 로직이 JavaScript 번들에 포함되어 번들 크기가 커진다.

```jsx
*// Styled Components 예시*
const Button = styled.button`
  background-color: ${props => props.primary ? 'blue' : 'white'};
  color: ${props => props.primary ? 'white' : 'blue'};
  padding: 10px 15px;
  border-radius: 5px;
`;
```

> (👨🏻‍🏫 : 이렇게 props에 따라 스타일이 동적으로 변하는 건 정말 편리하죠! 하지만 이 편리함에는 대가가 따른답니다... 바로 성능 오버헤드죠!)
> 

### 제로 런타임 CSS-in-JS (예: Vanilla Extract)
![](https://velog.velcdn.com/images/kyujenius/post/8869d996-eca8-4271-ba79-36cd999e67eb/image.png)

제로 런타임 CSS-in-JS 라이브러리는 다음과 같은 특징을 가진다:

1. **빌드 타임 스타일 생성**: 스타일이 빌드 타임에 정적 CSS 파일로 추출된다.
2. **런타임 오버헤드 없음**: 브라우저는 일반 CSS 파일을 로드하므로 JavaScript 관련 오버헤드가 없다.
3. **더 작은 번들 크기**: 스타일 관련 코드가 JavaScript 번들에 포함되지 않는다.

```jsx
*// Vanilla Extract 예시*
import { style, styleVariants } from '@vanilla-extract/css';

const baseButton = style({
  padding: '10px 15px',
  borderRadius: '5px'
});

export const button = styleVariants({
  primary: [baseButton, { backgroundColor: 'blue', color: 'white' }],
  secondary: [baseButton, { backgroundColor: 'white', color: 'blue' }]
});
```

### 성능 차이

런타임 오버헤드의 유무는 특히 대규모 애플리케이션에서 눈에 띄는 성능 차이를 가져온다:

1. **초기 로딩 시간**: 제로 런타임 라이브러리는 스타일을 정적 CSS 파일로 제공하므로 초기 로딩 시간이 더 빠르다.
2. **런타임 성능**: 제로 런타임 라이브러리는 스타일 처리를 위한 JavaScript 실행이 필요 없어 런타임 성능이 더 좋다.
3. **메모리 사용량**: 제로 런타임 라이브러리는 스타일 처리를 위한 추가 메모리를 사용하지 않는다.

> (👨🏻‍🏫 : 특히 모바일 기기나 저사양 디바이스에서는 이런 성능 차이가 더 크게 느껴질 수 있답니다!)
> 

## 4. 비슷한 다른 것들

Vanilla Extract 외에도 제로 런타임 CSS-in-JS 라이브러리는 여러 가지가 있다. 각각의 라이브러리는 고유한 특징과 장단점을 가지고 있다.

### Linaria

Linaria는 제로 런타임 CSS-in-JS 라이브러리 중 하나로, Vanilla Extract와 유사한 접근 방식을 취한다.

**주요 특징**:

- 빌드 타임에 CSS 파일 생성
- CSS 문법을 그대로 사용 (템플릿 리터럴 방식)
- 동적 스타일링을 위한 CSS 변수 활용

```jsx
*// Linaria 예시*
import { css } from 'linaria';

const button = css`
  background-color: blue;
  color: white;
  padding: 10px 15px;
  border-radius: 5px;
`;
```

### Astroturf

Astroturf는 또 다른 제로 런타임 CSS-in-JS 라이브러리로, CSS 모듈과 유사한 접근 방식을 취한다.

**주요 특징**:

- 빌드 타임에 CSS 파일 생성
- CSS 모듈과 유사한 API
- React 컴포넌트와의 통합 지원

```jsx
*// Astroturf 예시*
import React from "react";
import { css } from "astroturf";

const btn = css`
  color: black;
  border: 1px solid black;
  background-color: white;
`;

export default function Button({ children }) {
  return <button className={btn}>{children}</button>;
}
```

### Vanilla Extract vs 다른 라이브러리 비교

| 라이브러리 | 타입 안전성 | 스타일 정의 방식 | 테마 지원 | 빌드 도구 통합 |
| --- | --- | --- | --- | --- |
| Vanilla Extract | 우수 (TypeScript 네이티브) | 객체 리터럴 | 강력한 테마 시스템 | Webpack, Vite, Next.js 등 |
| Linaria | 보통 (TypeScript 지원) | 템플릿 리터럴 | CSS 변수 기반 | Webpack, Rollup 등 |
| Astroturf | 보통 (TypeScript 지원) | 템플릿 리터럴 | 제한적 | Webpack 중심 |
| Styled Components (런타임) | 보통 (TypeScript 지원) | 템플릿 리터럴 | ThemeProvider | 대부분의 빌드 도구 |

> (👨🏻‍🏫 : 저는 개인적으로 TypeScript를 좋아해서 Vanilla Extract를 선호한답니다. 객체 리터럴 방식이 코드 자동 완성도 잘 되고, 타입 체크도 확실히 되거든요!)
> 

## 5. 결론

Vanilla Extract CSS는 현대 웹 개발에서 스타일링 문제를 해결하기 위한 강력한 도구이다. 타입스크립트의 타입 안전성, 빌드 타임 CSS 생성을 통한 제로 런타임, 그리고 강력한 테마 시스템을 제공함으로써 기존 CSS-in-JS 라이브러리들의 단점을 극복했다.

### Vanilla Extract의 장점

- **타입 안전성**: 타입스크립트를 통한 스타일 정의로 오류를 사전에 방지할 수 있다.
- **성능**: 빌드 타임에 정적 CSS 파일을 생성하여 런타임 오버헤드가 없다.
- **개발자 경험**: 자동 완성, 타입 체크 등 타입스크립트의 장점을 활용할 수 있다.
- **테마 지원**: CSS 변수를 활용한 강력한 테마 시스템을 제공한다.

### Vanilla Extract의 단점

- **동적 스타일링 제한**: 런타임에 스타일을 동적으로 생성할 수 없다는 제약이 있다.
- **학습 곡선**: 새로운 API와 패턴을 학습해야 한다.
- **빌드 설정**: 빌드 도구에 추가 설정이 필요하다.

### 언제 Vanilla Extract를 사용해야 할까?

Vanilla Extract는 다음과 같은 상황에서 특히 유용하다:

1. **대규모 프로젝트**: 타입 안전성과 성능이 중요한 대규모 프로젝트에서 유용하다.
2. **디자인 시스템 구축**: 일관된 스타일과 테마를 관리해야 하는 디자인 시스템에 적합하다.
3. **성능이 중요한 애플리케이션**: 런타임 오버헤드를 최소화해야 하는 성능 중심 애플리케이션에 적합하다.

> (👨🏻‍🏫 : 물론 모든 프로젝트에 Vanilla Extract가 최선의 선택은 아닙니다. 작은 프로젝트나 빠르게 프로토타입을 만들어야 하는 경우에는 다른 솔루션이 더 적합할 수 있답니다. 항상 프로젝트의 요구사항과 팀의 선호도를 고려해서 결정하세요!)
> 

웹 개발 생태계는 계속해서 진화하고 있으며, Vanilla Extract는 그 진화의 한 부분이다. 타입 안전성과 성능을 모두 챙기고 싶은 개발자들에게 Vanilla Extract는 매력적인 선택지가 될 것이다.

**결론적으로**, Vanilla Extract는 CSS-in-JS의 개발자 경험과 일반 CSS의 성능적 이점을 모두 제공하는 균형 잡힌 솔루션이다. 타입스크립트를 사용하는 프로젝트에서 스타일링 솔루션을 고민하고 있다면, Vanilla Extract를 고려해볼 만하다.

> 🙇🏻 글 내에 틀린 점, 오탈자, 비판, 공감 등 모두 적어주셔도 됩니다. 감사합니다..! 🙇🏻

