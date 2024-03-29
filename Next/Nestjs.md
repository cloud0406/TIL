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

## server landing

서버렌더링을 합니다. 클라이언트 렌더링과 다르게 서버렌더링을 한 페이지의 페이지 소스보기를 클릭하면 내부에 소스가 있습니다.

## code splitting

dynamic import를 이용하면 손쉽게 코드 스플리팅이 가능합니다.

코드 스플리팅은 내가 원하는 페이지에서 원하는 자바스크립트와 라이브러리를 렌더링 하는 것입니다. 모든 번들(chunk.js)이 하나로 묶이지 않고, 각각 나뉘어 좀 더 효율적으로 자바스크립트 로딩 시간을 개선할 수 있습니다.

## typescript

타입스크립트 활용을 위해 웹팩을 만지거나 바벨을 만질 필요 없습니다. 타입스크립트를 설치하고 (yarn add typescript @types/node @types/react) 명령어 (yarn run dev)만 하면 자동으로 tsconfig, next-end.d.ts가 생성되어 타입스크립트로 코딩이 가능해집니다.

## document.tsx

meta 태그를 정의하거나, 전체 페이지에 관려하는 컴포넌트입니다.

```tsx
// pages/_document.tsx
import Document, { Html, Head, Main, NextScript } from "next/document";
export default class CustomDocument extends Document {
  render() {
    return (
      <Html>
        <Head>
          // 모든페이지에 아래 메타테크가 head에 들어감 // 루트파일이기에 가능한
          적은 코드만 넣어야함 전역 파일을 엉망으로 만들면 안된다 // 웹 타이틀,
          ga 같은것 넣음
          <meta property="custom" content="123123" />
        </Head>
        <body>
          <Main />
        </body>
        <NextScript />
      </Html>
    );
  }
}
```

- 이곳에서 console은 서버에서만 보이고 클라이언트에서는 안보입니다.
- render 요소는 반영하지만 페이지 구성 요소만 반영되고 js는 반영 하지 않기 때문에 console은 보이지 않습니다. 즉, componentDidMount 같은 훅도 실행 되지 않습니다. 정말 static한 상황에만 사용합니다.

## app.tsx

```tsx
function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />;
}

export default MyApp;
```

- 이곳에서 렌더링 하는 값은 모든 페이지에 영향을 줍니다.
- 최초로 실행되는 것은 \_app.tsx
- app.tsx은 클라이언트에서 띄우길 바라는 전체 컴포넌트 → 공통 레이아웃임으로 최초 실행되며 내부에 컴포넌트들을 실행함
- 내부에 컴포넌트가 있다면 전부 실행하고 html의 body로 구성
- Component, pageProps를 받습니다.
- 여기서 props로 받은 Component는 요청한 페이지입니다. GET / 요청을 보냈다면, Component 에는 /pages/index.js 파일이 props로 내려오게 됩니다.
- pageProps는 페이지 getInitialProps를 통해 내려 받은 props들을 말합니다.
- 그 다음 \_document.tsx가 실행됨
- 페이지를 업데이트 하기 전에 원하는 방식으로 페이지를 업데이트 하는 통로
- app.tsx에서 console.log 실행시 client, server 둘다 콘솔 찍힙니다. (localhost:3000 웹과 터미널에서 둘다 콘솔 보임)

## import style component

```tsx
import styles from "./test.module.css";

function Heading(props) {
  // const variable = "red";
  return (
    <div className="title">
      <h1 className={styles.red}>{props.heading}</h1>
    </div>
  );
}

export default function Home() {
  return (
    <div>
      <Heading heading="heading" />
      <h1>스타일</h1>
    </div>
  );
}
```

```tsx
// test.module.css

h1.red {
  color: blue;
}

```

## sass 사용하기

따로 config 파일을 정의 하지 않고이 css 파일을 scss로 바꾸고 yarn add sass --dev 를 해주면 next에서 알아서 설정을 해줍니다.

## Link 사용하기

보통 페이지간 이동은 a 태그를 사용하나 next에서는 사용하지 않습니다.

a 태그를 사용하면 처음 페이지에 진입시 번들 파일을 받고, a 태그에 의해 라우팅 되면 다시 번들 파일을 받기 때문입니다. 또한 redux을 쓰시는 경우 store의 state 값이 증발되는 현상도 일어납니다. 그렇기 때문에 a 태그는 전혀 다른 사이트로 페이지를 이동시켜 다시 돌아오지 않는 경우만 사용하고, 그 이외에는 모두 Link 태그를 사용합니다.

```tsx
import Link from "next/link";

const Index = () => (
  <div>
    <Link href="/blog">
      <a>Blog</a>
    </Link>
    // 동적 link시 [] 사용
    <Link href="/blog/[address]">
      <a>Blog</a>
    </Link>
  </div>
);
```

        Copied!

## 동적 url

가변적으로 변하는 url에 대해 동적 url을 지원합니다. [] 문법으로 동적 페이지를 생성하는 동적 url을 만들 수 있습니다.

```tsx
// pages/[id].tsx

import { useRouter } from "next/router";

export default () => {
  const router = useRouter();

  return (
    <>
      <h1>post</h1>
      <p>postid: {router.query.id}</p>
    </>
  );
};
```

- 위처럼 작성하면 localhost:3000/123 으로 접속시 postid 가 123으로 나옵니다.

- pages/[값].tsx 왼쪽 페이지 구조의 값은 router.query.값과 동일합니다.

## prefetching

백그라운드에서 페이지를 미리 가져옵니다. 기본값은 true. `<Link />` 뷰포트에있는 모든 항목 (초기 또는 스크롤을 통해)이 미리 로드됩니다. 정적 생성을 사용하는 JSON페이지는 더 빠른 페이지 전환을 위해 데이터가 포함 된 파일을 미리 로드합니다.

이건 Link 컴포넌트를 사용해서 이뤄지는건데요. 링크 컴포넌트를 렌더링할때 `<Link prefetch href="...">` 형식으로 prefetch 값을 전달해주면 데이터를 먼저 불러온다음에 라우팅을 시작합니다.

프로덕션 레벨에서만 이루어집니다.

## next/router 사용법

react의 react-router-dom과 사용 방법은 거의 유사합니다.

link에 있는 preferch 기능도 사용 가능합니다.

```tsx
import { useEffect } from "react";
import { useRouter } from "next/router";
import posts from "../posts.json";

export default () => {
  const router = useRouter();

  const post = posts[router.query.id as string];
  if (!post) return <p>noting</p>;

  useEffect(() => {
    router.prefetch("/test");
  }, []);

  return (
    <>
      <h1>{post.title}</h1>
      <h1>{post.content}</h1>
      <button onClick={() => router.push("test")}>go to Test</button>
    </>
  );
};
```

## server side lifeCycle

위 포스팅을 통해 getInitialProps를 파악 하였다면, lifeCycle이 이해할 수 있습니다.

nextJs 서버가 GET 요청을 받는다.
GET 요청에 맞는 pages/Component를 찾는다.
\_app.tsx의 getInitialProps가 있다면 실행한다.
route에 맞는 페이지의 Component의 getInitialProps가 있다면 실행한다. pageProps들을 받아온다.
\_document.tsx의 getInitialProps가 있다면 실행한다. pageProps들을 받아온다.
모든 props들을 구성하고, \_app.tsx -> page Component 순서로 렌더링
모든 Content를 구성하고 \_document.tsx를 실행하여 html 형태로 출력한다.

## custom 태그로 head 태그 옮기기

우리는 페이지의 header로 다음과 같은 정보를 추가할 수 있습니다.

페이지 제목을 커스텀하고 싶을때
meta 태그를 변경하고 싶을때
next/head로 부터 Head 컴포넌트를 받아 모든 컴포넌트에서 사용할 수 있습니다.

```tsx
import Head from "next/head";

export default () => (
  <div>
    <Head>
      <title>새로 만들어진 타이틀 입니다</title>
    </Head>
    <div>...</div>
  </div>
);
```

next.js가 해당 컴포넌트가 mount 할때, Head내 태그들을 페이지의 HTML의 Head에 포함 시킵니다. 마찬가지로 unMount 할때, 해당 태그를 제거합니다.

## dynamic meta tag

위 예시처럼 정적으로 태그를 달때도 있지만 정적으로 들어가야할 태그가 바뀔 경우도 있습니다.
next.js meta tag 동적 content 할당하기 (opens new window)이 글에서 동적으로 meta 태그에 값을 할당하는 방법에 대해 알아볼 수 있습니다.

## dynamic component import

dynamic component import는 react에서 lazy하게 component를 import 해오는 방식과 유사합니다.
dynamic하게 처음 보여줘도 안되는 컴포넌트는 import 하지 않게 됨으로, 초기 화면 렌더링 속도를 상승시키기 위해 사용합니다.
주의점은 페이지에 진입할때 맨처음 보이는 컴포넌트라면 dynamic 메소드를 사용할 이유가 없습니다. (왜 그런지 모르겠다면 dynamic components를 사용하는 이유에 대해 다시 생각해보시기 바랍니다)
아래 예시로 next에서 사용하는 방법과 typescript에서 component에 props을 내려줄때 type 정의를 어떻게 하는지 알아봅니다.

```tsx
import React, { useState } from "react";
import dynamic from "next/dynamic";

const DynamicComponent = dynamic<{ nowTab: number }>(
  () => import("./DynamicComponent")
);

const Index = () => {
  const [nowTab, setNowTab] = useState(0);

  return (
    <>
      {nowTab === 0 && <div>0 tab</div>}
      {nowTab === 1 && <DynamicComponent nowTab={nowTab} />}
    </>
  );
};
```

## next.js meta tag 동적 content 할당하기

- nextjs에서는 Head를 이용하여 seo에 필요한 meta 태그에 접근할 수 있다.
- 이 meta 태그에 들어가는 content는 꼭 static한 상태여야만 html의 head에 들어갑니다 -> 동적인 상태에서는 html head에 들어가지 않음

## getInitalProps를 이용하여 값 받아오기

- 간단하게 getInitalProps를 이용하여 돔이 구성되기 전에 값을 가져오면 된다.
- 예시

```tsx
import React from "react";
import Head from "next/head";

export default function Test({ title }) {
  return (
    <>
      <Head>
        <meta property="og:title" content={title ? title : "default title"} />
      </Head>
      <div>dynamic meta test</div>
    </>
  );
}

Test.getInitialProps = () => {
  return { title: "seo title" };
};
```

### 주의사항

- 개발환경에서 위 작업이 가능한 이유는 로컬에서 next가 서버 역할을 해주기 때문에 가능
- 만약 이 작업이 운영(production)에 올라가려면 next build를 거쳐야하는데 운영에 next가 걸쳐있는 서버가 있지 않다면 build 실패가 뜰 것이다. 그렇기 때문에 웹용 next 서버를 먼저 띄워야함.
- 서버가 없다면 getInitialProps를 사용 못하게 되고 위 예시의 title이라는 값이 html이 만들어질 때 어디에도 존재하지 않기 때문에 content에는 defailt title이 들어감.

## next.js getInitialProps 사용법

- 서버사이드 렌더링을 하는 nextJs에서 컴포넌트는 각 페이지마다 사전에 불러와야할 데이터가 있다.(이하 data fetching) react, vue같은 Client Side Rendering (CSR)의 경우는 useEffect, created 함수를 이용하여 data fetching을 한다.
- 서버사이드에서 실행하는 next에서는 getInitialProps를 이용하여 data fetching 작업을 한다.
- next v9 이상에서는 getInitialProps 대신 getStaticProps, getStaticPaths, getServerSideProps을 사용하도록 가이드 한다.

## getStaticProps

- 빌드시 고정되는 값, 빌드 이후 값 변경 불가

```tsx
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  );
}

export async function getStaticProps() {
  const res = await fetch("https://.../posts");
  const posts = await res.json();

  // By returning { props: posts }, the Blog component
  // will receive `posts` as a prop at build time
  return {
    props: {
      posts,
    },
  };
}

export default Blog;
```

- docs 예제에서는 fetch를 통해 게시물을 가져오고 그 게시물의 title을 보여줍니다.

## getStaticPatch

- 빌드 타임 때, 정적으로 렌더링할 경로 설정
  이곳에 정의하지 않은 하위 경로는 접근해도 페이지가 안뜸
  동적라우팅 : 라우팅 되는 경우의 수 따져서 하위로 넣음

/pages/dyna/[dynamic].js: /dyna/동적인값

```tsx
// This function gets called at build time
export async function getStaticPaths() {
  return {
    //빌드 타임 때 아래 정의한  /dyna/1,  /dyna/2, ... /dyna/동적인값 경로만 pre렌더링.
    paths: [
      { params: { dynamic: 1 } },
      { params: { dynmic: 2 } }
      ......
      { params: { dynmic: 동적인값 } }
    ],
    // 만들어지지 않은 것도 추후 요청이 들어오면 만들어 줄 지 여부.
    fallback: true,
  }
}
```

## getServerSideProps

```tsx
function Page({ data }) {
  // Render data...
}

// This gets called on every request
export const getServerSideProps: GetServerSideProps = async (context) => {
  // Fetch data from external API
  const res = await fetch(`https://.../data`);
  const data = await res.json();

  // Pass data to the page via props
  return { props: { data } };
};

export default Page;
```

각 요청에 따라 서버로부터 데이터를 가져옵니다.

### 언제 쓰는가?

- getServerSideProps는 데이터 요청시 인출해야 페이지를 미리 렌더링해야하는 경우에만. TTFB (Time to First byte)는 getStaticProps서버가 모든 요청에 ​​대해 결과를 계산해야하고 추가 구성 없이는 결과를 CDN에 의해 ​​캐시 할 수 없기 때문에 더 느립니다.

- 데이터를 미리 렌더링 할 필요가없는 경우 클라이언트 측에서 데이터를 가져 오는 것을 고려해야합니다.

## 클라이언트 측에서 데이터 가져오기

- 페이지에 자주 업데이트되는 데이터가 포함되어 있고 데이터를 미리 렌더링 할 필요가없는 경우 클라이언트 측에서 데이터를 가져올 수 있다. 이에 대한 예는 사용자 별 데이터이며 작동 방식은 다음과 같다.

- 먼저 데이터가 없는 페이지를 즉시 표시한다. 페이지의 일부는 정적 생성을 사용하여 미리 렌더링 할 수 있으며 누락 된 데이터에 대한 로드 상태를 표시 할 수 있다.
- 이후 다음 클라이언트 측에서 데이터를 가져와 준비가 되면 표시한다.
- 예를 들어 이 접근 방식은 사용자 대시 보드 페이지에 적합하며 대시 보드는 비공개 사용자 별 페이지이기 때문에 SEO는 관련이 없으며 페이지를 미리 렌더링 할 필요가 없다. 데이터는 자주 업데이트되므로 요청 시간 데이터 가져오기가 필요하다.

## SWR

- next에서 만든 SWR을 사용하여 client side에서 데이터 패치를 한다.

```tsx
import useSWR from "swr";

function Profile() {
  const { data, error } = useSWR("/api/user", fetch);

  if (error) return <div>failed to load</div>;
  if (!data) return <div>loading...</div>;
  return <div>hello {data.name}!</div>;
}
```

- seo 필요한 페이지면 getServerSideProps를 쓰고 비공개 페이지일 경우는 클라이언트 측에서 가져온다 (getServerSideProps를 남발하면 서버가 모든 요청을 계산하고, 값 캐쉬가 힘들기에 비효율적)

## getInitialProps 이점

- 속도가 빨라진다. 서버는 data fetching만, 브라우저는 렌더링만 함으로 연산을 브라우저와 서버가 각각 나누어 분담하게되어 그만큼 속도가 빨라진다.
- 함수형 컴포넌트로 next를 코딩할 경우, 렌더링 하는 함수와 data fetching을 하는 함수가 분리됨으로 개발자의 입장에서 로직 파악이 쉽다.

### 사용법

#### 오류코드

- data fetching 할 mok 데이터 (src/posts.json)를 먼저 정의.

```tsx
{
  "test": {
    "title": "test post",
    "content": "test content"
  },
  "second": {
    "title": "second post",
    "content": "second content"
  }
}
```

- 데이터를 보여줄 컴포넌트.

```tsx
/ src/aegps / [id].tsx;
import { useEffect } from "react";
import { useRouter } from "next/router";
import posts from "../posts.json";

const Posts = () => {
  const router = useRouter();
  const post = posts[router.query.id];

  return (
    <>
      <h1>{post.title}</h1>
      <h1>{post.content}</h1>
    </>
  );
};

export default Posts;
```

- 위처럼 코드를 작성하고 yarn run dev 후에 localhost:3000/test로 접속합니다.

- [id].tsx가 렌더링 될 때, post 값이 없기 때문에 title이 없다는 에러가 납니다.

- CSR에서는 이럴때 if (!post) return <p></p>; 이러한 코드를 추가하여 data fetching이 되기를 기다립니다.

```tsx
// src/pages/[id].tsx
import { useEffect } from "react";
import { useRouter } from "next/router";
import posts from "../posts.json";

const Posts = () => {
  const router = useRouter();
  const post = posts[router.query.id];
  if (!post) return <p></p>;

  return (
    <>
      <h1>{post.title}</h1>
      <h1>{post.content}</h1>
    </>
  );
};

export default Posts;
```

ssr에서는 getInitalProps를 이용하여 데이터를 미리 받아오고, 렌더링 할 당시에는 이미 값이 있기 때문에 렌더링이 되는 방식으로 사용한다.

```tsx
import { useEffect } from "react";
import { useRouter } from "next/router";
import posts from "../posts.json";

const Posts = (props: { post: { title: string; content: string } }) => {
  const router = useRouter();

  return (
    <>
      <h1>{props.post.title}</h1>
      <h1>{props.post.content}</h1>
    </>
  );
};

Posts.getInitialProps = (context) => {
  // context.query.id = 'test'
  return {
    post: posts[context.query.id],
  };
};

export default Posts;
```

- 위처럼 컴포넌트의 외부에 getInitialProps 함수를 선언만합니다. 실행은 렌더링 될때 알아서 실행된다.

- getInitialProps 내부에는 context, component등 여러 객체가 있으며 그 중 query.id에 접근하여 우리의 url인 'test'를 받아오고, mok data에서 test 객체를 꺼내와 post에 담는다.

- post는 컴포넌트의 props에 담겨서 컴포넌트가 렌더링 될때 바로 사용 가능하다.

## 주의사항

- 하나의 페이지에서는 하나의 getInitialProps만 실행됨
- 페이지가 렌더링 될때 next 내부에서 거치는 순서는 \_app -> page component
- 즉, 예시 코드로 말씀드리면 \_app.tsx가 실행되고 [id].tsx가 실행됨.

- 만약 \_app에 getInitialProps를 정의했다면, 하위 컴포넌트에서는 getInitialProps는가 실행되지 않는다.

- 하위 컴포넌트에서도 getInitialProps 값을 반영하려면 아래와 같이 \_app.tsx에 코드를 추가.

```tsx
// src/_app.tsx
import "./globals.css";

function MyApp({ Component, pageProps }) {
  return <Component ponent {...pageProps} />;
}

MyApp.getInitialProps = async ({ Component, ctx }) => {
  let pageProps = {};
  // 하위 컴포넌트에 getInitialProps가 있다면 추가 (각 개별 컴포넌트에서 사용할 값 추가)
  if (Component.getInitialProps) {
    pageProps = await Component.getInitialProps(ctx);
  }

  // _app에서 props 추가 (모든 컴포넌트에서 공통적으로 사용할 값 추가)
  pageProps = { ...pageProps, posttt: { title: 11111, content: 3333 } };

  return { pageProps };
};

export default MyApp;
```

위 예시 코드처럼 모든 컴포넌트에서 추가할 값을 \_app.tsx에서 추가하고, 각 개별 컴포넌트에서 사용할 값은 개별 컴포넌트에서 추가하면 된다.

## getInitialProps는 서버에서 실행

- 브라우저 api (setTimeout, window.xxx, document.xxx)는 이곳에서 실행하면 안된다.

## Context Object

- pathname - 현재 pathname (/user?type=normal-> /user)
- query - 현재 query를 객체로 (http://localhost:3000/blog/test -> {id: 'test'}, /post?type=secret -> {type: 'secret'})
- asPath - 전체 path (http://localhost:3000/blog/test -> /blog/[id], /blog/test)
- req - HTTP request object (server only)
- res - HTTP response object (server only)
- err - Error object if any error is encountered during the rendering

## shallow route와 getInitialProps과 관계

- 아래 내용들은 getServerSideProps, getStaticProps, getInitialProps 모두 동일하게 적용.

- shallow routing은 getInitialProps를 이용하여 데이터를 가지오지 않고도 url을 변경 할 수 있다.

- 즉, 불필요한 서버 연산을 최소화 할 수 있고, 필요한 상태 값은 아래 예시코드 처럼 router 객체에 넣어서 전달할 수 있다.

```tsx
router.push(
  {
    pathname: "/cars?model=bmw",
    query: { ...values, page: 1 },
  },
  undefined,
  { shallow: true }
);
```

- 위의 예제로 보면 원래는 /cars 페이지에 있다가 어떤 이벤트를 통해 "/cars?model=bmw"로 바뀜.

- 페이지는 교체되지 않고, url만 바뀌는 경우 입니다. url이 변경되는 것은 componentDidUpdate, useEffect를 통해 감지 할 수 있다.

```tsx
componentDidUpdate(prevProps) {
  const { pathname, query } = this.props.router
  // verify props have changed to avoid an infinite loop
  if (query.counter !== prevProps.router.query.counter) {
    // fetch data based on the new query
  }
}

```

## 주의사항

- shallow routing은 동일 페이지의 url의 변경에서만 작동.
- 즉, 다른 페이지로 이동시 새 페이지가 로드하고, getInitialProps는 실행됩니다. 예시로 보자면 현재 url은 /cars 이다.

```tsx
router.push(
  {
    pathname: "/users",
    query: { ...values, page: 1 },
  },
  undefined,
  { shallow: true }
);
```

- 위처럼 /users로 url이 바뀌면 페이지가 바뀌었기 때문에 새로운 페이지가 로드되고 getInitialProps가 실행됨으로 data fetching을 하게됨. 즉, shallow routing이 의미가 없음.

- 같은 페이지에서 url이 바뀌는데 getInitialProps가 실행됨으로 굳이 같은 data fetching을 할 필요가 없을 때, shallow routing을 사용하면된다.

## next.js에서 material-ui 사용하기

### nextjs에서 mui(material-ui)를 적용하는 방법

- 이 이후에는 다음은 styled-components와 mui를 동시에 사용하는 방법 (opens new window)

## 프로젝트 세팅

next 프로젝트를 설치하고 이후 필요한 디펜던시를 설치

```tsx
npx create-next-app my-app

cd my-app
yarn add @emotion/react @emotion/styled @mui/icons-material @mui/material @mui/styles
```

## 세팅하기

- nextjs는 서버사이드랜더링을 하기 때문에 react에서 mui를 사용하는 것 처럼 손쉽게 반영 되지 않다. 그래서 몇가지 사전작업을 해야한다.

## \_document.tsx

- \_document는 서버사이드에 관여하는 로직 또는 static한 로직을 추가하는데 사용.
- ssr 지원을 위해 \_dococument.tsx에 mui에 대한 사전 작업을 한다
- 간단히 말씀드리면 서버에서 받아온 html, css와 클라이언트가 렌더링한 html, css가 다르면 next에서 warning을 띄우게 된다. 그래서 서버단에서 mui를 지원함으로 서버와 클라이언트간 간극을 맞추기 위해 아래와 같이 구현한다.

```tsx
import React from "react";
import Document, { Html, Head, Main, NextScript } from "next/document";
import { ServerStyleSheets } from "@mui/styles";

export default class MyDocument extends Document {
  render() {
    return (
      <Html>
        <body>
          <Head></Head>
          <Main />
          <NextScript />
        </body>
      </Html>
    );
  }
}

MyDocument.getInitialProps = async (ctx) => {
  const materialSheets = new ServerStyleSheets();
  const originalRenderPage = ctx.renderPage;

  ctx.renderPage = () =>
    originalRenderPage({
      enhanceApp: (App) => (props) =>
        materialSheets.collect(<App {...props} />),
    });

  const initialProps = await Document.getInitialProps(ctx);
  return {
    ...initialProps,
    styles: <>{initialProps.styles}</>,
  };
};
```
