# Front-End

Transcendence Front-End 파트는 다음과 같은 사항들을 설치한다.

<aside>

💡 `TypeScript` `React` `React Router DOM` `Vite` `ESLint` `VSCode`

</aside>

---

### VSCode

VSCode에서 환경을 설정하기 위해 아래 Extension을 설치한다.  
**- ESLint**

설치가 끝났다면 Settings.json에 아래와 같은 내용을 추가해 준다.

```powershell
"editor.codeActionsOnSave": {
  "source.fixAll.eslint": true
},
"editor.formatOnSave": true,
"eslint.run": "onSave"
```

---

### Install Vite

Vite를 통해 TypeSciprt를 사용하는 React 프로젝트를 생성한다.  
아래 명령어를 통해 프로젝트를 생성하고 설정한다.

```powershell
$> npm create vite@latest front_end
# Select a framework : react
# Select a variant : react-ts
```

프로젝트 생성 후 해당 폴더로 이동해 npm을 설치하고 동작을 확인한다.

```powershell
$> cd front_end
$> npm install
$> npm run dev
```

---

### Install React-Router-DOM

Vite를 통해 React 프로젝트를 생성했으니 react-router-dom은 npm을 통해 설치만 하면 된다.  
아래 명령어를 통해 설치한다.

```powershell
$> npm install react-router-dom
```

---

### Install ESLint

아래 명령어를 통해 ESLint를 설치한다.

```powershell
$> npm install eslint --save-dev
```

설치가 완료되었다면 아래 명령어를 통해 ESLint를 초기화한다.

```powershell
$> npm init @eslint/config
# How would you like to use ESLint? : To check syntax, find problems, and enforce code style
# What type of modules does your project use? : JavaScript modules (import/export)
# Which framework does your project use? : React
# Does your project use TypeScript : Yes
# Where does your code run? : Browser
# How would you like to define a style for your project? : Use a popular style guide
# Which style guide do you want to follow? : Airbnb: https://github.com/airbnb/javascript
# What format do you want your config file to be in? : JSON
# Would you like to install them now with npm : Yes
```

---

### Set npm command for ESLint

ESLint는 커맨드를 통해 사용해야 한다.  
npm을 통해 실행할 수 있도록 아래와 같이 설정을 추가한다.

```powershell
# package.json

"scripts": {
    "lint": "eslint --ext .tsx src"
}
```

이제 `npm run lint` 명령을 통해 `src` 디렉토리 내 `.tsx` 파일에 대한 ESLint가 실행된다.  
아무 설정을 하지 않은 상태에서도 각종 에러가 발생하고, 이에 대한 수정을 맨 아래에서 수정할 계획이다.

---

### Set Config

위에서 언급했던 에러가 나오는 내용을 수정하기 위한 설정값이다.  
ESLint는 최상위 디렉토리의 `.eslintrc.json` 을 통해서, Prettier는 최상위 디렉토리의 `.prettierrc` 를 통해 설정이 가능하다.  
ESLint의 설정은 아래 블로그의 글을 참조하였다.  
[eslint 설정하기](https://sezzled.tistory.com/entry/eslint-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0)

```powershell
# .eslintrc.json

{
  "env": {
    "browser": true,
    "es2021": true
  },
  "extends": ["plugin:react/recommended", "airbnb"],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaFeatures": {
      "jsx": true
    },
    "ecmaVersion": "latest",
    "sourceType": "module"
  },
  "plugins": ["react", "@typescript-eslint"],
  "rules": {
    "quotes": ["error", "double"], //더블 쿼터 사용
    "@typescript-eslint/quotes": ["error", "double"], //더블 쿼터 사용
    "no-unused-vars": "off", //사용안한 변수 경고 중복
    "@typescript-eslint/no-unused-vars": "warn", //사용안한 변수는 경고
    "jsx-a11y/control-has-associated-label": "off", // 상호작용하는 엘리먼트에 label을 넣는다
    "react/no-array-index-key": "off", // key값으로 index를 사용할수 있다.
    "comma-dangle": "off", // 마지막에 , 을 넣어주지 않는다.
    "arrow-body-style": "off", //화살표 함수 안에 return을 사용 할 수 있다.
    "react/no-unescaped-entities": "off", //문자열 내에서 " ' > } 허용
    "react/prop-types": "off", //proptypes를 사용하지 않는다.
    "object-curly-newline": "off", // { 다음 줄 바꿈을 강제로 사용하지 않는다.
    "react/jsx-one-expression-per-line": "off", //한라인에 여러개의 JSX를 사용 할 수 있다.
    "implicit-arrow-linebreak": "off", // 화살표 함수 다음에 줄 바꿈을 사용할 수 있다.
    "no-shadow": "off", //파일 내에서 중복 이름을 사용 할 수 있다.
    "spaced-comment": "off", //주석을 뒤에 달 수 있다.
    "operator-linebreak": "off", //연산자 다음 줄 바꿈을 사용 할 수 있다.
    "react/react-in-jsx-scope": "off", // jsx를 사용하여도 React를 꼭 import 하지 않아도 된다.
    "react/jsx-props-no-spreading": "off", //props를 스프래드 할 수 있다.
    "jsx-a11y/anchor-is-valid": "off", // next js에서는 a에 href없이 사용
    "global-require": "off", //함수 내에서 require 사용가능
    "jsx-a11y/label-has-associated-control": "off", //label htmlFor을 사용하지 않아도 된다.
    "import/prefer-default-export": "off", //export default 를 사용하라.
    "no-param-reassign": "off",
    "react/jsx-curly-newline": "off", // jsx안에 }를 새로운 라인에 사용할 수 있다.
    "no-use-before-define": "off", // 선언하기 전에 사용할수 없다. 중복
    "@typescript-eslint/no-use-before-define": ["warn"], // 선언하기 전에 사용 한다면 경고
    "no-case-declarations": "off", // case문 안에서 변수 선언 사용하기
    "react/jsx-filename-extension": [
      1,
      { "extensions": [".js", ".jsx", ".tsx"] } //jsx사용가능한 확장자 설정
    ],
    "import/extensions": [
      "error",
      "ignorePackages",
      {
        "js": "never",
        "jsx": "never",
        "ts": "never",
        "tsx": "never"
      } //import 시 확장자명은 사용하지 않는다.
    ]
  },
  "settings": {
    "import/resolver": {
      "node": {
        "extensions": [".js", ".jsx", ".ts", ".tsx", ".d.ts"]
      }
    }
  }
}
```
