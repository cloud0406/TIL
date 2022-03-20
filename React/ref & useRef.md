## Ref 객체 생성<hr>

```js
import { useRef } from "react";

// ...

const ref = useRef();
```

- `useRef` 함수로 Ref 객체를 만들 수 있다.

## ref Prop 사용<hr>

```js
const ref = useRef();

// ...

<div ref={ref}> ... </div>;
```

- `ref` Prop에다 위에서 만든 Ref 객체를 내려주면 됨

## Ref 객체에서 DOM 노드 참조<hr>

```js
const node = ref.current;
if (node) {
  // node 를 사용하는 코드
}
```

- Ref 객체를 `current` 라는 프로퍼티를 사용하면 DOM 노드 참조할 수 있다.
- `current` 값이 없을 수도 있으니 위의 if문과 같이 반드시 값이 존재하는지 검사하고 사용해야 한다.

## ex) 이미지 크기 구하기 <hr>

```js
import { useRef } from "react";

function Image({ src }) {
  const imgRef = useRef();

  const handleSizeClick = () => {
    const imgNode = imgRef.current;
    if (!imgNode) return;

    const { width, height } = imgNode;
    console.log(`${width} x ${height}`);
  };

  return (
    <div>
      <img src={src} ref={imgRef} alt="크기를 구할 이미지" />
      <button onClick={handleSizeClick}>크기 구하기</button>
    </div>
  );
}
```

- 위의 코드는 `img` 노드 크기를 `ref` 를 활용해서 출력하는 예시
- `img` 노드에는 너비 값인 `width` 와 높이 값인 `height` 라는 속성이 있는데 Ref 객체의 `current` 로 DOM 노드를 참조하여 두 속성 값을 가져와서 사용한 것
