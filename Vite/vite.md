### React & Vite

- **Vite**란 빠르고 간결한 모던 웹 프로젝트 개발 경험에 맞춰 탄생한 빌드 도구
  - 사전 추가
  ```tsx
  yarn add

  react, nest, vite, nx,

  // ?

  yarn add @nrwl/react

  yarn add @nrwl/cli -g

  // 명령어

  yarn start my-new-app  (클라이언트)

  yarn start (= yarn nx serve) (서버)
  ```
  - yarn
  ```tsx

  yarn create nx-workspace  // nest 파일로 생성

  yarn nx g nx-plugin-vite:app your-vite-app --framework=react  // 해당 workspace로 이동해 리액트+vite 생성

  yarn add nx-plugin-vite@latest vite -D --save  // 최신 버전 vite로 설치 (v3.1.7)

  yarn add @vitejs/plugin-react-refresh --save  // 필요 라이브러리 설치

  yarn vite build apps/your-vite-app/   // 해당 경로의 파일 빌드

  [your-vite-app]으로 이동 후, yarn serve (vite preview)
  ```
  - npm
  ```tsx
  npm create-nx-workspace

  * package.json 파일 복붙 후, 'npm install --force'    // 버전 충돌시 force 명령어 추가해줘야함

  npx nx g nx-plugin-vite:app your-vite-app --framework=react

  npx vite build apps/your-vite-app/
  ```
- 생성된 vite 프로젝트를 보면 `index.html`파일이 `public`디렉터리가 아닌 프로젝트의 루트에 위치 → 추가적인 번들링 과정 없이 `index.html`파일이 앱의 진입점이 되게끔 하기 위해 의도적으로 위치시킴
- **프로젝트 루트 지정**
  - `vite`은 개발 서버를 시작할 때 현재 위치해 있는 디렉터리를 프로젝트 루트로 가정하고 동작. 만약 특정 디렉터리를 지정해 프로젝트 루트로써 동작하게끔 하고 싶다면, `vite serve some/sub/dir` 명령으로 Vite를 시작해야함
