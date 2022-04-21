# Front-End

---

Transcendence Front-End 개발 환경은 아래 환경들을 적용된다.

<aside>
💡 `TypeScript` `React` `React Router DOM` `Vite` `ESLint` `Prettier` `VSCode`

</aside>

### VSCode

---

VSCode에서 환경을 설정하기 위해 아래 Extension을 설치한다.

- ESLint
- Prettier

설치가 끝났다면 아래와 같은 설정을 해 준다.

- Default Formatter : Prettier
- Format On Save : Check

### Install Vite

---

Vite를 통해 TypeSciprt를 사용하는 React 프로젝트를 생성한다.

아래 명령어를 통해 프로젝트를 생성하고 설정한다.

```powershell
npm create vite@latest front_end
# Select a framework : react
# Select a variant : react-ts
```

프로젝트 생성 후 해당 폴더로 이동해 npm을 설치하고 동작을 확인한다.

```powershell
cd front_end
npm install
npm run dev
```

### Install React-Router-DOM

---

Vite를 통해 React 프로젝트를 생성했으니 react-router-dom은 npm을 통해 설치만 하면 된다.

아래 명령어를 통해 설치한다.

```powershell
npm install react-router-dom
```

### Install ESLint

---

아래 명령어를 통해 ESLint를 설치한다.

```powershell
npm install eslint --save-dev
```

설치가 완료되었다면 아래 명령어를 통해 ESLint를 초기화한다.

```powershell
npm init @eslint/config
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

### Set npm command for ESLint

---

ESLint는 커맨드를 통해 사용해야 한다

npm을 통해 실행할 수 있도록 아래와 같이 설정을 추가한다.

```powershell
# package.json

"scripts": {
    "lint": "eslint --ext .tsx src"
}
```

이제 `npm run lint` 명령을 통해 `src` 디렉토리 내 `.tsx` 파일에 대한 ESLint가 실행된다.

아무 설정을 하지 않은 상태에서도 각종 에러가 발생하고, 이에 대한 수정을 맨 아래에서 수정할 계획이다.

### Install Prettier

---

아래 명령어를 통해 Prettier를 설치한다.

```powershell
npm install prettier --save-dev
```

이 때, 프리티어와 ESLint를 함께 사용하기 위한 플러그인도 설치한다.

```powershell
# prettier와 ESLint 출동 규칙 비활성화
npm install eslint-config-prettier --save-dev

# prettier 규칙을 ESLint로 추가
# ESLint사용 시 prettier도 확인된다.
npm install eslint-plugin-prettier --save-dev
```

### Set Config

---

위에서 언급했던 에러가 나오는 내용을 수정하기 위한 설정값이다.

ESLint는 최상위 디렉토리의 `.eslintrc.json` 을 통해서, Prettier는 최상위 디렉토리의 `.prettierrc` 를 통해 설정이 가능하다.

ESLint의 설정은 아래 블로그의 글을 참조하였다.
