# styled-components

## styled-components란

- 기존 돔을 만드는 방식인 css, scss 파일을 밖에 두고, 태그나 id, class이름으로 가져와 쓰지 않고, 동일한 컴포넌트에서 컴포넌트 이름을 쓰듯 스타일을 지정하는 것을 styled-components 라고 한다.
- css 파일을 밖에 두지 않고, 컴포넌트 내부에 넣기 때문에, css가 전역으로 중첩되지 않도록 만들어주는 장점이 있다.

### 예시

```tsx
import React, { useState } from "react";
import styled from "styled-components";

function Example() {
  const [email, setEmail] = useState("");

  return (
    <ExampleWrap active={email.length}>
      <Button>Hello</Button>
      <NewButton color="blue">Im new Button</NewButton>
    </ExampleWrap>
  );
}

const ExampleWrap = styled.div`
  background: ${({ active }) => {
    if (active) {
      return "white";
    }
    return "#eee";
  }};
  color: black;
`;

const Button = styled.button`
  width: 200px;
  padding: 30px;
`;

// Button 컴포넌트 상속
const NewButton = styled.Button`
  // NewButton 컴포넌트에 color가는 props가 있으면 그 값 사용, 없으면 'red' 사용
  color: ${(props) => props.color || "red"};
`;

export default Example;
```

## styled components 만들기

- const 컴포넌트명 = styled.태그명스타일 넣기
- 만들고자하는 컴포넌트의 render 함수 밖에서 만듭니다.

## 스타일에 props 적용하기

- styled-component를 사용하는 장점 중 하나는 변수에 의해 스타일을 바꿀 수 있다는 점이다.
- 위 코드 예시를 보면 email이라는 state값에 따라 ExampleWrap에 prop으로 내려준 active라는 값이 true or false로 바뀌게 된다.
- styled-component는 내부적으로 props을 받을 수 있고, 그 props에 따라 스타일을 변경할 수 있다.

## 스타일 상속

- const 컴포넌트명 = styled.스타일컴포넌트명스타일 넣기...문법으로 만들어지며 기존에 있는 스타일컴포넌트를 상속받아 재사용한다.
