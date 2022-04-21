# BE 프로젝트 기반 설정 및 Lint 설정

# Nest.js

Node.js에서는 ES Modules을 지원하고 FE에서 번들러를 사용하는 이유들이 BE에선 불필요하므로 Vite를 사용할 필요가 없습니다.

Nest.js는 Node.js 기반의 백앤드 프레임워크이며 내부적으로 Express.js나 Fastify 프레임워크를 사용합니다.

Nest.js는 자체 CLI를 이용해 프로젝트 빌드 및 실행 등을 지원해 줍니다.

## Nest.js 기반 타입스크립트 프로젝트 설정

Nest.js는 타입스크립트 개발이 기본이므로 별도로 설정할 필요는 없으며 nest cli가 전역으로 설치되어 있다면 다음 명령어로 프로젝트를 생성할 수 있습니다.

```bash
$> nest new [프로젝트명]
```

저는 제 로컬 환경에 nest cli를 전역으로 설치할 것이 아니므로 nest cli만 별도 폴더에 설치하고 해당 폴더 내에서 프로젝트를 생성하는 방법을 선택하였습니다.

```bash
$> npm init -y # node 기반 프로젝트 생성 (-y 안붙이면 수동으로 설정)
$> npm i @nestjs/cli # NestJS CLI 설치
```

## prettier 관련 코드 삭제

`package.json` , `package-lock.json` , `.eslintrc.js` 파일 내 prettier 관련 코드와 `.prettirerrc` 파일을 삭제합니다.

## ESLint 설정 변경

Nest.js가 기본적으로 제공하는 보일러플레이트 코드에는 ESLint가 적용되어 있습니다. 이 프로젝트에서 기본적으로 airbnb의 룰을 따르기로 하였으므로 airbnb 규칙을 추가합니다.

ESLint의 airbnb 패키지를 다음 명령어로 설치합니다.

```tsx
$> npm install eslint-config-airbnb-base eslint-config-airbnb-typescript --save-dev
```

- `eslint-config-airbnb-base` : `eslint-config-airbnb` 패키지엔 ECMAScript 외에 리액트 관련 룰을 포함하고 있습니다. 그래서 리액트 관련 룰이 제외된 `eslint-config-airbnb-base` 패키지를 설치합니다.
- `eslint-config-airbnb-typescript` : 만약 airbnb 룰만 적용한다면 타입스크립트 관련 오류가 발생할 수 있습니다. `eslint-config-airbnb-typescript` 패키지는 airbnb 룰에 타입스크립트를 쉽게 적용할 수 있도록 해줍니다.

다음 eslintrc 파일에 해당 내용을 추가합니다.

```tsx
extends: [
    'airbnb-base',
    'airbnb-typescript/base',
  ],
```

### airbnb 룰과 Nest.js 보일러플레이트 코드 충돌 해결

- `import/prefer-default-export` : 모듈 내에 export하는 대상이 하나만 존재할 때엔 `default` 키워드를 붙여야 합니다.
- `import/no-useless-path-segments` : "./../" 대신 "../” 를 사용합니다.
- `arrow-body-style` : 화살표 함수 사용시 불필요하게 중괄호와 return을 동시에 사용하지 않습니다.
- `class-methods-use-this`  : 클래스 내 메소드가 `this` 키워드를 사용하지 않으면 static 메소드로 사용합니다.
  - 하지만 static 메소드로 선언하면 클래스의 인스턴스로 static 메소드에 접근하지 못하는 문제가 있으므로 해당 규칙은 프로젝트에서 예외처리 하도록 협의하였습니다.

## .eslintrc.json 파일 명세

```json
{
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "project": "tsconfig.json",
    "tsconfigRootDir" : "./", 
    "sourceType": "module"
  },
  "plugins": ["@typescript-eslint/eslint-plugin"],
  "extends": [
    "airbnb-base",
    "airbnb-typescript/base",
    "plugin:@typescript-eslint/recommended"
  ],
  "root": true,
  "env": {
    "node": true,
    "jest": true
  },
  "ignorePatterns": [".eslintrc.json"],
  "rules": {
    "@typescript-eslint/interface-name-prefix": "off",
    "@typescript-eslint/explicit-function-return-type": "off",
    "@typescript-eslint/explicit-module-boundary-types": "off",
    "@typescript-eslint/no-explicit-any": "off",
    "class-methods-use-this": "off"
  }
}
```

