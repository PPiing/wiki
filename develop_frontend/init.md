# FE 프로젝트 기반 설정 및 Lint 설정

# What / Why Vite?

- 참조 문서 : https://vitejs-kr.github.io/guide/why.html

자바스크립트를 모듈 단위로 작성하는 것(ES Modules)이 과거에는 불가능하였습니다. 그래서 브라우저에서 구동시킬 때엔 번들러 (모듈들을 하나로 합치는 작업 혹은 툴)를 사용해서 하나로 합치는 방법을 사용했습니다.

최근 브라우저에서는 모듈 단위로 작성한 소스 코드를 실행시킬 수 있지만 여러 이유(여러 파일 → 하나의 파일 / 압축, 난독화 등 / 폴리필)로 실제 배포 시엔 번들링을 하고 배포합니다.

기존에 번들러로 사용하던 여러 툴들 (대표적으로 Webpack)은 여러 모듈로 이루어진 프로젝트들을 하나로 합칠 때의 가장 큰 단점은 “느리다" 였습니다.

기존 번들러 툴 대비 Vite는 100배정도 빠른 번들링 속도를 제공하므로 Vite를 사용하는 것이 합리적일 수 있습니다.

## Vite 기반 리액트 + 타입스크립트 프로젝트 생성

vite는 여러 프레임워크나 라이브러리 기반의 템플릿 코드들을 제공합니다. 예를 들면 리액트 기반 타입스크립트 프로젝트르 vite 기반으로 만들고자 할 때에는 `--template` 옵션을 붙이고 `react-ts` 를 붙이면 됩니다. 별도의 기반이 필요 없는, 비어있는 프로젝트를 만들고자 할 때에는 `--template` 옵션을 떼면 됩니다.

```bash
$> npm create vite@latest [프로젝트명] --template react-ts
```

다음 명령어를 기입하면 `프로젝트명` 폴더에 타입스크립트 기반의 리액트 프로젝트 템플릿을 만들어 줍니다.

## Vite의 기능

- npm을 이용해서 추가한 모듈을 스크립트에서 import 문으로 가져올 수 있습니다.
  - ES Module을 지원하는 브라우저가 타겟이라면 import 문을 수정해 url을 이용해 가져올 수 있도록 합니다.
- 타입스크립트 컴파일을 지원합니다. 단 Vite가 타입 체크를 하지 않으므로 별도로 tsc 등을 이용해 타입 체킹은 별도로 하여야 합니다.
- 리액트 스타일의 jsx 파일 및 tsx 파일을 지원합니다.
- 기타 기존 번들러에서 지원하는 기능들을 지원합니다.

## 플러그인 소개

Vite는 개발을 돕는 여러 플러그인을 제공합니다. Vite 프로젝트에서 플러그인을 추가하려면 프로젝트 내 `package.json` 파일 내 devDependencies (개발할 때에만 필요한 라이브러리를 의미합니다.) 항목에 플러그인을 추가해야 합니다.

Vite는 대부분의 유용한 기능들을 기본으로 제공해 주므로 별도로 플러그인을 설치할 일이 흔하지 않습니다.

## 프로젝트 디렉터리 구조

Vite 기반의 프론트앤드 프로젝트는 앱의 기본 진입점인 `index.html` 파일이 프로젝트의 루트에 위치해 있습니다.

프로젝트의 소스 코드들은 `index.html` 파일을 제외하고 모두 `src/` 폴더 내에 위치해 있습니다.

빌드된 결과물들은 모두 `dist` 폴더 내에 생성됩니다.

## 명령어

`package.json` 파일의 scripts 항목을 보면 (이 항목에 있는 것들은 해당 프로젝트에서 실행시킬 수 있는 스크립트 명령어와 실행할 명령을 정의합니다.) 다음 명령들을 실행할 수 있습니다.

- dev → `vite`
  - 개발 단계에서 현재 프로젝트를 브라우저 상에서 구동시킬 수 있도록 해줍니다.
- build → `tsc && vite build`
  - 타입스크립트 기반 프로젝트일 경우 타입 검사를 위해 `tsc` 를 먼저 실행하며, 프로젝트를 빌드합니다. 빌드된 결과물은 `dist` 폴더 내에 생성됩니다.
- preview → `vite preview`
  - 빌드 된 앱을 로컬에서 구동해보기 위한 명령어입니다. `--port [포트번호]` 옵션으로 동작시킬 포트를 지정할 수도 있습니다.

# ESLint 설정

## Lint란?

lint의 뜻은 보푸라기라는 뜻입니다. 코드 상에서 직접적으로 오류를 발생시키지는 않지만 잠재적으로 오류를 발생시킬 수 있는 요소들을 점검하는 툴을 Lint라고 부릅니다.

## ESLint

ESLint 공식 홈페이지엔 다음과 같이 소개되어 있습니다.

> ESLint는 코드를 더 일관되게 만들고 버그를 피하기 위해 ECMAScript/JavaScript 코드에 있는 패턴을 식별하고 보고하는 도구입니다.

ESLint는 자바스크립트 문법인 ECMAScript에 대한 Lint 툴입니다. 기존에 여러 린트 툴이 있지만 최근에는 ESLint를 주로 사용하는 추세입니다.

## 설치 및 설정

- 참조 : https://eslint.org/docs/user-guide/getting-started

Vite로 생성한 프로젝트에는 ESLint를 포함하고 있지 않으므로 해당 프로젝트에 ESLint를 설치합니다.

```bash
# --save-dev 옵션은 package.json의 devDependencies에 해당 디펜던시를 추가합니다.
$> npm install eslint --save-dev
```

이후 ESLint 설정을 위해 다음 명령을 실행합니다. 명령을 실행하면 프롬프트를 통해 설정을 할 수 있습니다. 기존 프로젝트 설정에 따라 해당 내용을 설정합니다.

```bash
$> npm init @eslint/config
✔ How would you like to use ESLint? · style
✔ What type of modules does your project use? · esm
✔ Which framework does your project use? · react
✔ Does your project use TypeScript? · Yes
✔ Where does your code run? · browser
✔ How would you like to define a style for your project? · guide
✔ Which style guide do you want to follow? · airbnb
✔ What format do you want your config file to be in? · YAML
✔ Would you like to install them now with npm? · Yes

Successfully created .eslintrc.yml file in path
```

ESLint의 사용 범위, 프레임워크, TS/JS 여부, 기존 존재하는 스타일 적용, 설정 파일 등을 고를 수 있습니다.

위의 일련의 작업들을 거치면 `package.json` 파일 내 devDependencies에 필요한 디펜던시들이 추가되며 `.eslintrc.yml` 파일 내에 ESLint 설정이 명시됩니다.

## 구동

참조 : https://eslint.org/docs/user-guide/command-line-interface

ESLint를 설치할 때 `package.json` 파일의 scripts 항목에 별도로 명령을 추가하지 않기 때문에 별다른 설정을 하지 않을 경우 다음 명령으로 파일을 검사해야 합니다.

```bash
$> npx eslint [파일]
```

우리는 `src` 폴더 내의 `*.ts, *.tsx` 파일만 검사하길 원하므로 다음 명령을 적용해야 합니다.

```bash
$> npx eslint --ext ".tsx" --ext ".ts" "src/"
```

ESLint로 검사할 때마다 명령어를 기입하기 번거로우므로 `package.json` 파일의 scripts 항목에 별도로 명령을 추가합니다.

```bash
"lint": "eslint --ext \\".tsx\\" --ext \\".ts\\" \\"src/\\""
```

VSCode에 ESLint 확장 기능을 설치하면 VSCode가 설정파일을 읽어 해당 프로젝트의 린트를 점검합니다.

## 추가 설정

airbnb 룰 사용 (TBD)

# Prettier (적용 X)

## prettier란

Lint는 코드의 잠재적인 오류 요소에 대해 초점이 맞춰져 있다면 Prettier는 소스코드의 포맷에 초점이 맞춰져 있습니다. 소스코드에서 스페이스를 사용하냐, 탭을 사용하냐의 여부는 구동에 영향을 미치지 않습니다.

Linter와 Prettier의 차이는 Prettier 공식 홈페이지에도 명시되어 있습니다. (https://prettier.io/docs/en/comparison.html)

이런 소스코드의 포맷을 통일할 때 Prettier를 사용합니다.

## 설치 및 설정

참조 : https://prettier.io/docs/en/install.html

Vite로 생성한 프로젝트에는 Prettier를 포함하고 있지 않으므로 해당 프로젝트에 Prettier를 설치합니다.

```bash
# --save-dev 옵션은 package.json의 devDependencies에 해당 디펜던시를 추가합니다.
$> npm install prettier --save-dev
```

## Prettier 적용 논의 필요

탭과 스페이스 사용 여부, 크기 등은 기존 ESLint (airbnb 룰)에서도 통제되는 대상이며 Prettier는 ESLint처럼 권장하는 설정이 별도로 존재하지 않는 것 같습니다.

그리고 기존 다른 JS/TS 기반 프로젝트 (네이버의 빌보드.js와 리액트)를 살펴본 결과 Prettier를 아예 적용하지 않거나 제한적으로 적용하고 있는데 Prettier를 적용하는 데 프로젝트의 복잡도만 높이는 것으로 생각됩니다.

그래서 Prettier는 적용하지 않는 것으로 협의 (4월 14일)하였습니다.
