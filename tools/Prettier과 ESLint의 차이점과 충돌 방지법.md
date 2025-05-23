### 바쁘신 분들을 위해 먼저 결론!

<aside>
💡 결론

1. Prettier는 VSCode와 같은 IDE에서 설치 후(클릭 몇 번이면 됨) 쓸 수 있는 확장 프로그램이다. 그 중 Prettier는 코드의 포멧팅(formmatting) 을 정할 수 있게 도와준다.
2. Prettier**를 쓰고자, IDE나 터미널을 통해 설치를 하게 되면 `.prettierrc` 라는 파일이 생기는 데, 이는 단순한 설정파일**이고 **해당 설정파일을 각각의 IDE에서 적용**해야 하는 것이다.
3. `ESLint`는 정적 분석 도구이다. 자바스크립트 코드에서 문법적 오류와 잠재적 버그를 식별하는 도구이다. **`코드 포멧팅`,** **`코드 규칙 강제`를 통해** `ESLint`는 팀 내에서 설정된 코딩 규칙을 강제하여, 일관된 코드 스타일을 유지하게끔 도와준다. 이로써 코드의 품질을 보장하고, 개발자가 실수로 발생할 수 있는 일반적인 오류를 사전에 발견하도록 돕는다.
4. `ESLint` 는 이 과정에서 `Prettier`의 역할인 정렬 또한 도와주려 하는데, 뭣모르고 두가지 전부 다 사용하는 경우에는 이 둘이 충돌날 수 있다.
5. 즉, 이 경우에는 `ESLint` 설정 에서 `Prettier`에게 코드 포멧을 위임시킨다는 설정을 통해서 충돌을 방지해야한다.
6. 이 때, `Vite` 와 같은 통합 번들러를 사용하는 경우라면, `ESLint`와 `Prettier` 의 역할이 겹쳐서 교통 정릴르 해줬던 것처럼 동일하게 Vite 내에서의 설정을 통해, 충돌이 나지 않도록 plugin을 등록해줘야 한다.
</aside>

## Prettier과 ESLint 의 차이점

ESLint와 Prettier는 자바스크립트 및 기타 웹 개발 환경에서 코드 품질과 스타일을 유지하기 위해 널리 사용되는 도구다. 두 도구는 서로 다른 역할을 수행하며, 함께 사용될 때 시너지를 발휘하지만, 일부 그 역할이 겹쳐 주의해야 한다.

## ESLint의 역할

- **정적 코드 분석**: ESLint는 자바스크립트 코드에서 문법적 오류와 잠재적 버그를 식별하는 정적 분석 도구다. 이는 코드의 품질을 보장하고, 개발자가 실수로 발생할 수 있는 일반적인 오류를 사전에 발견하도록 돕는다.

- **코드 규칙 강제**: ESLint는 팀 내에서 설정된 코딩 규칙을 강제하여, 일관된 코드 스타일을 유지하게 한다. 예를 들어, 사용되지 않는 변수를 경고하거나, 특정 스타일의 따옴표 사용을 강제하는 등의 규칙을 설정할 수 있다.

- **유지보수성 향상**: 코드의 일관성과 품질을 높임으로써, 나중에 코드를 유지보수하기 쉬운 형태로 만들어 준다. 이는 새로운 팀원이 프로젝트에 쉽게 적응할 수 있도록 도우며, 코드 파악에도 빠른 속도를 낼 수 있게 돕는다.

## Prettier의 역할

- **코드 포맷팅**: Prettier는 코드의 스타일을 자동으로 포맷팅하는 도구다. 줄 바꿈, 공백, 들여쓰기 등과 같은 스타일 요소를 일관되게 조정하여, 코드를 깔끔하게 유지하는 데 중점을 둔다.

- **개발자 집중도 향상**: Prettier는 개발자가 코드를 작성할 때 스타일에 대한 고민을 덜어주며, 저장할 때 자동으로 포맷팅하여 일관된 스타일로 변환한다. 이를 통해 개발자는 코드 로직에 더 집중할 수 있다.

- **협업 효율성 증가**: 팀원 간의 코드 스타일 차이(들여쓰기, 쌍따움표 등)를 줄여주어, 협업 시 발생할 수 있는 혼란을 최소화한다. 모든 팀원이 동일한 포맷팅 규칙을 따르기 때문에 코드 리뷰 과정이 더 원활해진다.

## ESLint와 Prettier의 통합 사용

ESLint와 Prettier는 각기 다른 목적을 가지고 있지만, 함께 사용될 때 더 큰 효과를 발휘한다. ESLint가 코드의 품질을 관리하고, Prettier가 스타일을 일관되게 유지함으로써 개발자는 더 깔끔하고 오류 없는 코드를 작성할 수 있다.

- **충돌 방지**: ESLint의 포맷팅 기능은 Prettier와 충돌할 수 있으므로, `eslint-config-prettier` 패키지를 사용하여 ESLint에서 Prettier와 충돌하는 규칙을 비활성화해야 한다.

- **효율적인 설정**: `eslint-plugin-prettier`를 추가하면 Prettier 규칙이 ESLint 규칙으로 통합되어, `eslint --fix` 명령어로 포맷팅과 린팅을 동시에 수행할 수 있다.

결론적으로, ESLint는 코드 품질과 관련된 문제를 해결하고, Prettier는 코드 스타일을 일관되게 유지하는 데 특화되어 있다. 이 두 도구를 함께 사용함으로써 개발자는 보다 효율적이고 체계적인 코딩 환경을 구축할 수 있다.

## Prettier 설정

1. **프리티어 설치 및 기본 설정**

```bash
npm install --save-dev --save-exact prettier
```

그리고 프로젝트 루트에 `.prettierrc` 파일을 만들어 코드 포멧팅의 규칙을 정의한다.

```jsx
// .prettierrc
{
  "semi": true,
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2,
  "trailingComma": "es5"
}
```

1. **IDE 설정**

- VS Code 사용자: Prettier 확장 프로그램 설치
- WebStorm 사용자: 내장된 Prettier 설정 활성화

## **ESLint와 함께 쓰는 경우 충돌 방지법!**

**그러나 ESLint와 함께 쓰는 경우 2단계를 더 설정해야 한다.**

1. eslint-config-prettier 패키지 다운로드

```bash
npm install --save-dev eslint-config-prettier eslint-plugin-prettier
```

2. **이후, .eslintrc에 추가한다.**

```jsx
{
  "extends": [
    "eslint:recommended",
    "prettier"
  ],
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "error"
  }
}

```

## **Git Commit 시 코드 포멧팅의 자동화가 필요한 경우**

```bash
npm install --save-dev husky lint-staged
```

이후 package.json에 정의

```jsx
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": "prettier --write"
  }
}

```

**핵심은 프리티어 설치와 IDE 설정만 하면 기본적인 코드 포맷팅 통일은 가능**하다. 그 외에도 ESLint 를 사용하면, 추가적인 설정을 통해 충돌을 방지할 수 있고, 이것만으로도 부족하다고 생각하여, 이중 삼중으로 방지하고 싶다면, Git Hooks를 사용해 팀의 필요에 따라 선택적으로 추가하면 된다.

## Vite 사용시 변경점

Vite 로 구성된 프로젝트에서 프리티어와 ESLint를 설정하는 방법

### 기본 설치

**1. 프리티어 설치**

```bash
npm install --save-dev --save-exact prettier

```

**2. ESLint 관련 패키지 설치**

```bash
npm install --save-dev vite-plugin-eslint eslint-plugin-prettier eslint-config-prettier

```

### 설정 파일 생성

**프리티어 설정 (.prettierrc)**

```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 80
}
```

**Vite 설정 내 ESLint 등록 (vite.config.js)**

```jsx
import { defineConfig } from 'vite';
import eslintPlugin from 'vite-plugin-eslint';

export default defineConfig({
  plugins: [eslintPlugin()]
});

/**
eslintPlugin({
  include: ['src/**/*.js', 'src/**/*.jsx', 'src/**/*.ts', 'src/**/*.tsx']
})
*/
// 다음과 같이 검사 범위를 src 내로 좁혀서 빌드 성능 향상까지 노려볼 수 있다. (default에도 검사 범위를 제한해준다.)
```

### IDE 설정

**VS Code 사용자**는 `.vscode/settings.json` 파일을 생성하여 다음 설정 추가:

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode"
}
```

**코드 포메팅 시, 무시할 파일 설정 (.prettierignore - gitignore과 유사)**

```
node_modules/
dist/
```

이렇게 설정하면 Vite 프로젝트에서 자동으로 코드 포맷팅이 적용되며, 팀원들과 동일한 코드 스타일을 유지할 수 있다.

## ESLint 설정

ESLintPlugin() 설정 하나 한다고 되면 안된다! eslint.config 파일을 통해 eslint 에서도 설정할 수 있게 한다.

<aside>
💡

```jsx

{
  "extends": [
    "eslint:recommended",
    "prettier"
  ],
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "error"
  },
  "env": {
    "browser": true,
    "es2021": true,
    "node": true
  },
  "parserOptions": {
    "ecmaVersion": "latest",
    "sourceType": "module"
  }
}

```

</aside>

## 도구 간의 관계

```
Vite (개발 환경)
   ↓
ESLint (코드 품질 검사)
   ↓
Prettier (코드 스타일 포맷팅)
```

**작동 순서**

1. Vite가 개발 서버를 실행하고 코드 변경을 감지
2. ESLint가 코드 품질을 검사 (버그 가능성, 안티 패턴 등 약속한 코드인지 검사)
3. Prettier가 코드 스타일을 정리 (들여쓰기, 줄바꿈 등)

이렇게 각 도구가 계층적으로 연결되어 있어서, 상위 도구의 설정이 하위 도구에 영향을 미치게 된다고 생각하면 된다.
