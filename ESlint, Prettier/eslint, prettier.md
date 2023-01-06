# ESLint & Prettier

## ESlint란?

ESlint는 ES와 lint의 합성어이다.

ES는 Ecma Script를 의미하며, Ecma라는 기구에서 만든 Script, 즉 표준 JavaScript를 의미한다.

Lint는 에러가 있는 코드에 표시를 달아놓은 것을 말한다.

즉 ESlint란 JS 프로젝트내에서 문법을 검사하며 에러를 표시해주는 기능을 말한다. (여기서 에러란 문법상의 에러를 포함하여 직접 사용자가 지정해놓은 문법에 맞지 않는 경우도 포함된다.)

ESlint는 여러 작업자들과 협업을 할때 공통된 코딩 스타일을 맞춰갈 수 있어 가독성이나 해석적 측면에서 효율이 좋다.

## Prettier란?

코드 정리 규칙을 세부적으로 설정해놓으면 정해놓은 규칙에 맞게 코드를 정렬해서 가독성을 높이고 코드 스타일을 통일할 수 있게 도와주는 플러그인을 말한다.

## ESlint와 Prettier의 차이

### lint

JS 언어 자체가 느슨한 경향이 있기 때문에 다음과 같은 상황에서 lint의 중요성을 느낄 수 있다.

```js
a = 2;
console.log(a); // 2
let a = 3;
cnsole.log(a); // 3
```

a를 변수에 선언하지 않았음에도 에러가 없이 그대로 콘솔로그에 찍히고 있다. 이러한 경우 당장에는 문제가 없지만 코드량이 많아져 문제가 발생할 경우 해당 내역을 찾기가 쉽지 않다. 이를 방지하기 위해 lint를 적용한다.

```js
// lint 적용

a = 2; // 'a' is not defined.
console.log(a); // 'a' is not defined.
let b = 3; // 'b' is assigned a value but never used.
```

위의 lint를 적용한 코드를 살펴보면 우선 a를 let, const 등으로 선언하지 않고 수정할 경우, 이를 콘솔로 찍었을 경우, b와 같이 변수가 선언되었지만 사용되지 않았을 경우 모두 사용자에게 위의 주석 메세지와 같은 경고를 준다.

### prettier

prettier는 팀원간의 코딩 스타일을 맞추기 위해서 사용된다.

다음의 두 코드를 살펴보자.

```js
// 1
const test = () => {
  alert("테스트를 진행 중입니다.");
};

// 2
const test2 = () => {
  alert("두번째 테스트를 진행중입니다.");
};
```

1의 경우와 2의 경우는 각각 탭 사이즈가 4와 2로 다르고 코드의 마지막에 세미콜론의 여부도 다르다. 이와 같이 간단히 작성할 경우는 이해가 빠르지만 프로젝트의 규모가 커질수록 개발자의 코드 스타일에 따라 서로에게 익숙치 않은 코드가 될 수 있다.

이를 해결하기위해 prettier를 적용하면 정해진 코드 작성 스타일을 잡아 두고 검사시에 가이드에 맞지 않을 경우 전체 코드를 새로 작성해주는 포맷터 기능을 제공한다.

```js
// prettier 적용 전
const test = () => {
  alert("테스트를 진행 중입니다.");
};

// prettier 적용 후 (탭 사이즈 2, 코드 종료시 세미콜론 작성)
const test2 = () => {
  alert("테스트를 진행 중입니다.");
};
```

ESlint : 함수의 선언 방식 혹은 반복문의 사용등과 같이 코드 작성법을 일관성 있는 방식으로 구현하도록 도와줌

prettier : eslint처럼 코드 구현 방식이 아닌 줄바꿈, 공백, 들여쓰기 등 에디터에서 텍스트 자체를 일관되게 작성하도록 도와줌

즉, ESlint와 Prettier의 차이는 기능적 측면으로 봤을때 ESlint안에 Prettier가 속한다고 볼 수 있다.(완전히 속하는 것은 아님) 다만 둘을 같이 쓰는 경우 Prettier가 더 포맷터 기능에 더욱 특화되어 있기 때문에 ESlint의 포맷터 기능을 off하고 Prettier가 포맷터 기능을 대체하도록 한다. (eslint는 코드 퀄리티를 보장하도록 도와주고, prettier는 코드 스타일을 깔끔하게 통일하도록 도와준다고 생각하자.)

## 설정

### ESlint 설정

1. 우선 린트를 설치

   ```
   npm install -D eslint

   yarn add -D eslint
   ```

2. vscode에서 eslint extension을 설치

   - eslint extension의 설명을 살펴보면 해당 워크스페이스(프로젝트)에서 eslint가 설치되어 있는지 확인한 후 없으면 글로벌 eslint를 참조한다고 한다. (필요에 따라 .eslintrc 파일이 필요할 수 있다.)

   - 프로젝트 코드 단에서 사용하는 것이 아닌 vscode 에디터에 적용해 사용하는 것이다 보니, eslint를 사용하려면 eslint extension만 설치하거나 eslint library만 설치하는 것이 아닌 둘다 설치 및 세팅이 되어 있어야 한다.

3. 설치 후, .eslintrc 파일을 통해 lint rule 세팅

   - 공식문서 (https://eslint.org/docs/latest/user-guide/configuring/configuration-files) 의 예제

   ```json
   {
     "root": true,
     "plugins": ["@typescript-eslint"],
     "extends": ["eslint:recommended", "plugin:@typescript-eslint/recommended"],
     "parser": "@typescript-eslint/parser",
     "rules": {
       "@typescript-eslint/strict-boolean-expressions": [
         2,
         {
           "allowString": false,
           "allowNumber": false
         }
       ]
     }
   }
   ```

   - **"root"** default 값은 true인데 이 값이 true가 아닐 경우 eslintrc 파일을 찾을 때 해당 프로젝트 디렉토리뿐만 아니라 내 PC의 root 파일 시스템 root 디렉토리까지 eslint를 찾게 된다.

   - **"plugins"** 는 `'eslint-plugin-react: 리액트 전용 플러그인'`, `'eslint-plugin-prettier: 린트 위에 사용할 프리티어 플러그인'` 등 여러 종류가 있으며 프로젝트에 필요한 각 플러그인들은 npm, yarn 등을 통해 설치하면 된다. (만약 위에 `@typescript-eslint/eslint-plugin`을 쓸 거면, eslintrc 파일의 plugins 배열에 해당 모듈에서 제공하는 `@typescript-eslint`를 작성하면 되고, 다른 모듈도 같이 쓸 거면 배열에 같이 추가하면 된다.)

   - **"parser"** 는 각 코드 파일을 검사할 파서를 설정하는 부분. 기본 설정은 `espree`이고 위와 같이 `'@typescript-eslint/eslint-plugin'` 처럼 특정 플러그인을 사용할 경우 해당 플러그인에서 제공하는 parser를 장착하면 된다.

   - **"extends"** 는 eslint rule 설정이 저장되어 있는 외부 file을 extends 하는 부분. 위의 `.eslintrc`처럼 extends에 `eslint:recommended`, `plugin:@typescript-eslint/recommended`를 작성하면, 사용하려는 해당 플러그인에서 기본적으로 제공하는 rule set이 적용된다. (변경하고 싶은 부분은 `rules`에서 커스터마이징 하면 된다.)

   - **"rules"** 는 직접 lint rule을 적용하는 부분. extends로 자동으로 설정된 rules 중, 특정 rule을 끄거나, erorr를 warning으로 나오도록 변경하는 등 설정을 변경할 수 있다.

   - 각 플러그인의 eslintrc 설정 방법은 각 eslint 플러그인 문서를 보면 상세하게 나와 있다. 예를 들어 `'@typescript-eslint`을 설정한다고 가정하면 아래와 같이 설정하면 된다.

     ```
     npm install --save-dev eslint typescript @typescript-eslint/parser @typescript-eslint/eslint-plugin
     ```

     ```json
     // .eslintrc

     module.exports = {
     root: true,
     parser: '@typescript-eslint/parser',
     plugins: [
         '@typescript-eslint',
     ],
     extends: [
         'eslint:recommended',
         'plugin:@typescript-eslint/recommended',
     ],
     };
     ```

   - 좀 더 자세한 설정은 다음 공식 문서를 참고하자 (https://typescript-eslint.io/)

### 정리

1. npm, yarn으로 설치한 eslint와 VSCode extension의 eslint는 단순히 린트 환경을 세팅해주는 모듈
2. 실제로 코드를 검사하여 에러를 확인하기 위해서는, 사용하고 싶은 lint plugins를 별도로 설치하 후 eslintrc 파일에 작성해서 적용해야 한다.

### Prettier 설정

세팅 방법은 크게 2가지

1. 별도의 prettier 관련 플러그인을 npm, yarn으로 설치하는 것이 아닌 vscode extension을 통해 설치
2. prettier 플러그인을 직접 설치 후 eslintrc에 세팅

1번의 방법은 위의 eslint 적용과 같이 본인의 vscode 에디터에 prettier rule을 세팅하는 방식. 즉 내 환경의 vscode에서만 해당 prettier 방식이 적용됨.

- vscode extension: 'prettier - code formatter' 설치 (설치하는 순간 바로 적용됨)
- `command + shift + p`를 누른 후 검색 창에 Open user Settings로 들어가 prettier를 검색하면 관련 설징 리스트들이 나타나며 본인에 맞게 설정하면 된다.
- 자세한 설정 방법은 공식 문서에 잘 설명되어 있다. (https://code.visualstudio.com/docs/getstarted/settings)

2번의 경우는 프로젝트 자체에 prettier rule 적용, 해당 프로젝트를 다른 환경에서 돌려도 동일하게 prettier rule이 적용됨.

- npm, yarn으로 prettier 설치
  ```
  npm install -D prettier
  yarn add _D prettier
  ```
- 공식문서를 보면 eslint를 사용할 경우 eslint-config-prettier (eslint와 중복되는 규칙을 prettier쪽에서 알아서 꺼주는 역할)를 설치해 세팅하라고 가르쳐준다.
- 상세한 prettier rule 세팅은 root 디렉토리의 `.prettierrc` 파일이 있는지 찾고 해당 파일의 'rule'을 통해 명시하면 된다. (`.eslintrc` 자체에도 선언 가능하나 가독성을 위해 `.prettierrc`로 분리하는 것을 권장)
- 마찬가지로 공식 문서에 설명이 잘 명시되어 있다. (https://prettier.io/docs/en/install.html)

1번의 경우에는 vscode 에디터에 설정하는 방식이기 때문에 extension 설치한 플러그인에만 적용가능하며 npm, yarn 으로 설치해 세팅한 prettier 플러그인에는 적용되지 않는다. 따라서 2번과 같이 npm, yarn으로 prettier를 설치했다면 `.prettierrc` 파일을 꼭 이용해야 한다.

또한 두 방식에 적용 우선 순위가 다른데 vscode extension으로 설치했다면 vscode settings의 prettier에서도 설정할 수 있으며, `.prettierrc` 파일로도 설정 가능하지만 `.prettierrc` 파일이 있다면 vscode settings의 설정은 무시되며 `.prettierc` 파일이 있다면 vscode settings의 설정은 무시되고 `.preetierc` 파일의 rule이 적용된다.

### 포맷팅 설정

위처럼 설정 하면 코드 내에서 lint와 prettier rule에 위반되는 코드를 에러로 판단해 빨간 줄을 보여준다. 하지만 코드 저장시 이를 알아서 수정해주는 기능 ('fotmat on save')은 다음과 같은 설정이 필요하다.

VScode 에디터가 코드 저장시 자동으로 린트를 잡아주는 기능을 동작하게 하기 위해 vscode setting을 설정해야 한다. `command + shift + p`를 통해 검색창에 `Open Settings.json(JSON)`을 선택하면 settings 설정이 JSON 형식으로 나온다. 두 가지 적용 방법이 있는데

1. editor.codeActionsOnSave

   ```
   "editor.codeActionsOnSave": {
       "source.fixAll.eslint": true
   },
   ```

   저장할때, eslint 관련 요소들을 전체적으로 수정하도록 동작하는 코드이다. (eslint rule에 의해 에러로 위반되는 부분들을 lint rule에 맞게 수정)

2. editor.formatOnSave

   ```
   {
       "editor.formatOnSave": true,
       "editor.defaultFormatter": "esbenp.prettier-vscode"
   }
   ```

   저장할때, vscdoe extension으로 설정된 prettier 환경에 맞게 코드를 수정하는 옵션. 1번의 방법과는 다르게 eslint 에러를 보고 수정하는 방식이 아닌 에디터에 있는 텍스트들을 prettier rule에 맞게 정리

만약, onSave 기능이 제대로 동작하지 않을 경우는 vscode settings.json 파일의 설정이 아닌 eslint, prettier 설정을 다시 한번 확인할 것

## 정리

1. ESLint
   eslint 기능 자체는 VSCode 에디터에 적용하는 것이므로, VSCode extension Plugin이 꼭 설치되어야 한다.

2. Prettier
   prettier 세팅 방식은 크게 2가지
   _ eslint처럼 VSCode 에디터 자체에 적용하도록 Prettier용 VSCode Extension을 설치하는 방식.
   _ prettier 플러그인을 설치 후 eslint 환경에 접목시키는 방식.

3. Format on Save
   "format on Save(저장 시 알아서 수정해주는 기능)"은 2가지
   _ 코드 내 lint error를 발견하고 수정해주는 옵션.
   _ 코드 텍스트들을 VSCode Extension으로 설치한 prettier 포맷에 맞게 수정해주는 옵션.
