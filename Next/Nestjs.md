# Next.js

### nextjs는 React로 만드는 서버사이드 렌더링 프레인 워크입니다. 서버사이드 렌더링을 함으로 얻는 이득은 다음과 같습니다.

- 클라이언트 렌더링의 경우 모든 js 파일을 로드하고 사용자는 웹을 보게됩니다. 이때까지 사용자는 많은 시간을 대기해야 합니다.
  seo 문제 - 클라이언트 사이드의 경우 자바스크립트가 로드 되지 않은 경우 아무런 정보를 보이지 않습니다. 구글의 검색엔진의 경우 자바스크립트가 로드되지 않은 페이지를 검색엔진으로 스캔함으로 결론적으로 검색에 아무 페이지도 걸리지 않게 됩니다.
  이 두가지를 해결하는 것이 서버렌더링입니다.

- 첫번째 문제는 서버에서 자바스크립트를 로딩함으로 클라이언트 측에서는 자바스크립트를 로딩하는 시간이 줄어들게 되고, 두번째 문제는 검색엔진이 자바스크립트를 읽는 것이 아닌 서버 측에서 자바스크립트, html, css를 만들어 컨텐츠를 직접 업로드 함으로 검색엔진에 게시글이 걸리게 됩니다.

- 또한 meta 태그를 자유롭게 추가함으로 seo를 용이하게 할수 있습니다.

# next.js가 제공하는 주요 기능

## hot reloading

- 개발 중 저장되는 코드는 자동으로 새로고침됩니다.

- automatic routing
  pages 폴더에 있는 파일은 해당 파일 이름으로 라우팅됩니다. (pages/page1.tsx -> localhost:3000/page1)

- public 폴더도 pages의 폴더와 동일하게 라우팅 할수있습니다. 그러나 모든 사람이 페이지에 접근할 수 있으므로 지양하도록합니다.

- single file components
  style jsx를 사용함으로 컴포넌트 내부에 해당 컴포넌트만 스코프를 가지는 css를 만들수 있습니다.

- `<style jsx global>`를 사용하면 글로벌로 스타일 정의 가능합니다.

```tsx
/ styled-jsx

function Heading(props) {
  const variable = "red";
  return (
    <div className="title">
      <h1>{props.heading}</h1>
      <style jsx>
        {`
          h1 {
            color: ${variable};
          }
        `}
      </style>
    </div>
  );
}

export default function Home() {
  return (
    <div>
      // red
      <Heading heading="heading" />
      // block
      <h1>ttt</h1>
    </div>
  );
}
```

## 글로벌 스타일 정의 가능합니다.

- \_app.tsx에만 정의 가능합니다. 다른 컴포넌트에 정의한 경우 다른 클래스와 겹쳐 오류를 발생할 수 있음으로 \_app에서만 허용합니다. 다른 컴포넌트에 정의시 아래와 같은 오류를 냅니다

```html
Global CSS cannot be imported from files other than your Custom
<App
  >. Please move all global CSS imports to pages/_app.tsx. Or convert the import
  to Component-Level CSS (CSS Modules).</App
>
```

```tsx
// _app.tsx
import "./globals.css";

function MyApp({ Component, pageProps }) {
  return <Component ponent {...pageProps} />;
}

export default MyApp;
```
