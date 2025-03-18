
## Emotion을 쓰라고? Styled-Components랑 같은데 더 빠르다고?
프론트엔드 개발자라면 CSS-in-JS 라이브러리 선택에서 한 번쯤 고민해본 적이 있을 것이다. 여러 선택지 중 Styled Components 가 종종 꼽히는데, 오늘은 왜 Styled Components 대신 Emotion을 선택해야 하는지에 대해 깊이 있게 살펴보려 한다. 두 라이브러리 모두 훌륭하지만, Emotion이 가진 특별한 장점들이 있다.

> (👨🏻‍🏫 : 저는 둘 다 써봤는데, Emotion으로 갈아탄 후, 굳이 Styled Components를 써야하나 싶더라고요? 거의 동일한데, 더 빨라요. 보통 이러면 버그가 잦은데 버그도 없더라고요? ~~개인적으로 Emotion 이 좀 더 세보입니다.~~)

CSS-in-JS 라이브러리는 React 생태계에서 스타일링의 혁명을 가져왔다. 컴포넌트 기반 개발에 최적화된 이 방식은 많은 개발자들의 사랑을 받고 있다. 그중에서도 Emotion이 주목받는 이유를 함께 알아보자!

## 💡급하신 분들을 위해서 결론 먼저!

1. Emotion은 **Styled Components 와 동일한 개발 경험** 을 주되, **더 작은 번들 크기**와 **더 빠른 성능**을 제공한다.
2. Emotion은 **개발자 친화적인 API**와 **유연한 스타일링 옵션**을 제공한다.
3. Emotion은 **css prop**을 통해 더 깔끔한 코드 작성이 가능하다.
4. Emotion은 **React Concurrent Mode**에 완벽하게 대응한다.
5. Emotion은 **서버 사이드 렌더링(SSR)**에서 더 효율적인 성능을 보인다.

## 1. 성능과 번들 크기

### 더 작은 번들 크기

Emotion은 Styled Components보다 **더 작은 번들 크기**를 자랑한다. 웹 애플리케이션에서 초기 로딩 시간은 사용자 경험에 직접적인 영향을 미치는데, Emotion의 작은 번들 크기는 이러한 측면에서 큰 장점이 된다.

> (👨🏻‍🏫 : 번들 크기가 8.9kb로 Styled Components의 14.6kb보다 훨씬 작아요! 모든 KB가 소중한 모바일 환경에서는 정말 중요한 차이랍니다. 📱)

### 뛰어난 런타임 성능

Emotion은 **런타임 성능**에서도 우수한 결과를 보여준다. 벤치마크 테스트에 따르면, Emotion은 일부 시나리오에서 Styled Components보다 최대 25배 빠른 성능을 보인다. 이는 Emotion의 효율적인 스타일 처리와 캐싱 메커니즘 덕분이다.

## 2. 개발자 경험

### 유연한 스타일링 옵션

Emotion은 **문자열 스타일**과 **객체 스타일** 모두를 지원하는 유연한 API를 제공한다. 이러한 유연성은 개발자가 자신의 선호도에 맞게 스타일을 작성할 수 있게 해준다.

**Emotion 코드 예시:**

```jsx
*// 문자열 스타일*
const Button = styled.button`
  color: ${props => props.primary ? "white" : "hotpink"};
  background-color: ${props => props.primary ? "hotpink" : "white"};
`;

*// 객체 스타일*
const Button = styled.button({
  color: props => props.primary ? "white" : "hotpink",
  backgroundColor: props => props.primary ? "hotpink" : "white"
});
```

**Styled Components 코드 예시:**

```jsx
*// 문자열 스타일*
const Button = styled.button`
  color: ${props => props.primary ? "white" : "hotpink"};
  background-color: ${props => props.primary ? "hotpink" : "white"};
`;
```

두 라이브러리 모두 문자열 스타일 작성 방식은 거의 동일하다. 하지만 Emotion은 객체 스타일 방식도 지원하여 더 유연한 스타일링이 가능하다. 조금 더 유연하답니다.

### 간편한 컴포넌트 네이밍

Emotion은 **css prop**을 통해 컴포넌트에 직접 스타일을 적용할 수 있어, 불필요한 컴포넌트 네이밍 작업을 줄여준다. Styled Components에서는 각 스타일 컴포넌트마다 의미 있는 이름을 부여해야 하지만, Emotion에서는 이러한 부담이 줄어든다.

**Emotion css prop 예시:**

```jsx
*/** @jsxImportSource @emotion/react */*
import { css } from '@emotion/react'

const Button = (props) => (
  <button
    css={css`
      color: ${props.primary ? "white" : "hotpink"};
      background-color: ${props.primary ? "hotpink" : "white"};
    `}
  >
    {props.children}
  </button>
);
```

**Styled Components 예시:**

```jsx
import styled from "styled-components";

const StyledButton = styled.button`
  color: ${(props) => (props.primary ? "white" : "hotpink")};
  background-color: ${(props) => (props.primary ? "hotpink" : "white")};
`;

const Button = (props) => (
  <StyledButton primary={props.primary}>{props.children}</StyledButton>
);
```

> (👨🏻‍🏫 : 매번 StyledButton, StyledHeader 같은 이름 짓기 귀찮으셨죠? Emotion에서는 그런 고민이 훨씬 줄어든답니다! 😌 ~~그래도 뭔가 지저분해서 결국엔 이름을 짓는다는 점~~)

## 3. CSS Prop의 강력함

### 더 깔끔한 코드 구성

Emotion의 **css prop**은 코드 구성을 더 깔끔하게 만들어준다. 별도의 스타일 컴포넌트를 만들지 않고도 직접 HTML 요소에 스타일을 적용할 수 있어 코드의 가독성이 향상된다.

### 쉬운 조합과 재사용

css prop을 사용하면 스타일을 쉽게 **조합**하고 **재사용**할 수 있다. 이는 복잡한 스타일링 로직을 다룰 때 특히 유용하다.

**Emotion 스타일 조합 예시:**

```jsx
*/** @jsxImportSource @emotion/react */*
import { css } from '@emotion/react'

const baseStyle = css`
  padding: 10px;
  border-radius: 4px;
`;

const primaryStyle = css`
  background-color: hotpink;
  color: white;
`;

const Button = (props) => (
  <button css={[baseStyle, props.primary && primaryStyle]}>
    {props.children}
  </button>
);
```

**Styled Components 스타일 조합 예시:**

```jsx
import styled, { css } from "styled-components";

const baseStyle = css`
  padding: 10px;
  border-radius: 4px;
`;

const primaryStyle = css`
  background-color: hotpink;
  color: white;
`;

const Button = styled.button`
  ${baseStyle}
  ${(props) => props.primary && primaryStyle}
`;
```

두 라이브러리 모두 스타일 조합이 가능하지만, Emotion의 css prop 방식은 컴포넌트 내부에서 더 직관적으로 스타일을 적용할 수 있다.

## 4. 미래 지향적 기술 지원

### React Concurrent Mode 지원

Emotion은 **React Concurrent Mode**를 완벽하게 지원한다. 이는 React의 미래 방향성과 일치하는 중요한 장점이다. Concurrent Mode는 React 애플리케이션의 반응성을 크게 향상시키는데, Emotion은 이러한 새로운 패러다임에 맞춰 최적화되어 있다.

### 지속적인 성능 개선

Emotion 팀은 **지속적인 성능 개선**에 집중하고 있다. 새로운 웹 기술과 React의 발전에 맞춰 Emotion도 계속해서 발전하고 있어, 미래 지향적인 프로젝트에 적합하다.

> (👨🏻‍🏫 : 기술은 계속 발전하니까 미래를 대비하는 것도 중요하죠! Emotion은 이런 측면에서 한 발 앞서 있답니다. 🚀 )

## 5. 서버 사이드 렌더링 최적화

### 효율적인 SSR 지원

Emotion은 **서버 사이드 렌더링(SSR)** 에서 뛰어난 성능을 보인다. `extractCritical` 유틸리티를 통해 서버 사이드 렌더링 중에 중요한 스타일을 추출하고 인라인화할 수 있어, 페이지 로드 시간을 크게 개선할 수 있다.

### 간편한 설정

Emotion의 SSR 설정은 비교적 **간단**하며, Next.js와 같은 프레임워크와도 쉽게 통합된다. 이는 개발자가 복잡한 설정 없이도 SSR의 이점을 누릴 수 있게 해준다.

**Emotion SSR 설정 예시:**

```jsx
import { extractCritical } from '@emotion/server'

const html = renderToString(<App />)
const { css, ids } = extractCritical(html)

*// 서버에서 렌더링된 HTML에 스타일 추가*
const finalHtml = `
  <!DOCTYPE html>
  <html>
    <head>
      <style data-emotion-css="${ids.join(' ')}">${css}</style>
    </head>
    <body>
      <div id="root">${html}</div>
    </body>
  </html>
`
```

**Styled Components SSR 설정 예시:**

```jsx
import { ServerStyleSheet } from "styled-components";
import { renderToString } from "react-dom/server";

const sheet = new ServerStyleSheet();
try {
  const html = renderToString(sheet.collectStyles(<App />));
  const styleTags = sheet.getStyleTags();

  const finalHtml = `
    <!DOCTYPE html>
    <html>
      <head>
        ${styleTags}
      </head>
      <body>
        <div id="root">${html}</div>
      </body>
    </html>
  `;
} finally {
  sheet.seal();
}
```

두 라이브러리 모두 SSR을 지원하지만, Emotion의 접근 방식이 더 간결하고 효율적이라는 평가를 받는다.

> (👨🏻‍🏫 : 근데 보통 Next.js에서는 tailwind-css가 거의 고정으로 되어가는 느낌이 없지 않아 있죠…? ㅎㅎ React 에서 새로 나온 서버 컴포넌트를 쓸 때, 쓰면 좋지 않을까 싶은 생각이 드네요 )

## 결론

두 라이브러리 모두 훌륭한 선택이지만, 프로젝트의 특성과 개발자의 선호도에 따라 선택이 달라질 수 있다. **그러나 더 유연한 방식을 택할 수 있고, 성능과 번들 크기또한 개선시켜주는데, Emotion을 택하지 않을 이유가 없다.** 개인적으로 개발하면서 크게 Emotion 때문에 문제가 생기거나 한 적은 없었기에, 향후에도 CSS-in-JS 를 택할 때는, Emotion 을 택할 것이다.

> 🙇🏻 글 내에 틀린 점, 오탈자, 비판, 공감 등 모두 적어주셔도 됩니다. 감사합니다..! 🙇🏻
