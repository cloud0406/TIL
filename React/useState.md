# useState

## 초기값 지정<hr>

- `useState` 함수에 값을 전달하면 초깃값으로 지정할 수 있다.

  ```js
  const [state, setState] = useState(initialState);
  ```

- 초기값을 계산해서 넣는 경우 콜백을 사용하면 되는데
  ```js
  function ReviewForm() {
    const savedValues = getSavedValues(); // ReviewForm을 렌더링할 때마다 실행됨
    const [values, setValues] = useState(savedValues);
    // ...
  }
  ```
- 위의 코드에서 `getSavedValues` 라는 함수로 컴퓨터에 저장된 초깃값을 가져온다고 해보자. 그렇다면 `savedValues` 라는 값을 처음 렌더링 한 번만 계산하면 되는데 위의 코드는 매 렌더링 때마다 불필요하게 `getSavedValues` 라는 함수를 실행하여 저장된 값을 가져오게 된다.
  ```js
  function ReviewForm() {
    const [values, setValues] = useState(() => {
      const savedValues = getSavedValues(); // 처음 렌더링할 때만 실행됨
      return savedValues;
    });
    // ...
  }
  ```
- 이럴 때는 위의 코드처럼 콜백 형태로 초기값을 지정해주면 처음 렌더링할 때 한번만 콜백을 실행해서 초기값을 만들고, 그 이후로는 콜백을 실행하지 않기 때문에 `getSavedValues` 함수를 불필요하게 실행하지 않는다.
- 주의할 점은 콜백 함수가 리턴할 때까지 리액트는 렌더링하지 않고 기다리게 되는데, **콜백 함수의 실행이 오래 걸릴 수록 초기 렌더링이 늦어지게 되므로 주의해야 한다.**

## Setter 함수 사용 <hr>

- 아래 코드와 같이 Setter 함수에 값을 전달하면 해당하는 값으로 변경된다.

  ```js
  const [state, setState] = useState(0);

  const handleAddClick = () => {
    setState(state + 1);
  };
  ```

- **이때 배열이나 객체 같은 참조형은 반드시 새로운 값을 만들어서 전달해야 한다.**

### 잘못된 참조형 State 사용

```js
const [state, setState] = useState({ count: 0 });

const handleAddClick = () => {
  state.count += 1; // 참조형 변수의 프로퍼티를 수정
  setState(state); // 참조형이기 때문에 변수의 값(레퍼런스)는 변하지 않음
};
```

### 올바른 참조형 State 사용

```js
const [state, setState] = useState({ count: 0 });

const handleAddClick = () => {
  setState({ ...state, count: state.count + 1 }); // 새로운 객체 생성
};
```

- 만약 이전 State 값을 참조하며 State를 변경하는 경우에는 비동기 함수에서 State를 변경하게 되면 최신 값이 아닌 State 값을 참조한다는 문제가 있는데, 이럴 때는 아래 코드와 같이 콜백을 사용하여 처리하면 된다.

  ```js
  const [count, setCount] = useState(0);

  const handleAddClick = async () => {
    await addCount();
    setCount((prevCount) => prevCount + 1);
  };
  ```
