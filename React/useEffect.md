# useEffect

## 한 번만 실행<hr>

- 컴포넌트가 처음 렌더링 된 후 리액트는 콜백 함수를 기억해뒀다가 실행하며 그 이후로는 콜백 함수를 실행하지 않는다. ( 즉, 첫 렌더링 직후에만 실행 )

  ```js
  useEffect(() => {
    // 실행할 코드
  }, []);
  ```

## 값이 바뀔 때마다 실행<hr>

- 위와 마찬가지로 컴포넌트가 처음 렌더링 된 후 리액트가 콜백 함수를 기억해뒀다가 실행하며 그 이후로는 렌더링 할 때 디펜던시 리스트에 있는 값들을 확인하여 하나라도 바뀌면 렌더링 이후 콜백 함수를 실행한다.

  ```js
  useEffect(() => {
    // 실행할 코드
  }, [dep1, dep2, dep3, ...]);
  ```

## 코드 예시 <hr>

- 아래 코드는 useEffect 함수의 동작을 간단히 실험해볼 수 있는 코드이다.
- 디펜던시 리스트에 [], [first], [first, second] 를 넣어보며 콘솔 출력이 어떻게 달라지는지 확인해보자

  ```js
  import { useEffect, useState } from "react";

  function App() {
    const [first, setFirst] = useState(1);
    const [second, setSecond] = useState(1);

    const handleFirstClick = () => setFirst(first + 1);

    const handleSecondClick = () => setSecond(second + 1);

    useEffect(() => {
      console.log("렌더링 이후", first, second);
    }, []);

    console.log("렌더링", first, second);

    return (
      <div>
        <h1>
          {first}, {second}
        </h1>
        <button onClick={handleFirstClick}>First</button>
        <button onClick={handleSecondClick}>Second</button>
      </div>
    );
  }

  export default App;
  ```

* 위의 코드를 실행한 후 first, second 버튼을 각각 누르면 "렌더링" 문구와 함께 first와 second가 1씩 증가하며 콘솔에 출력된다.

* 위의 코드에서 디펜던시 리스트를 [first]로 바꾼 후 실행하면 위와 마찬가지로 각 버튼들을 누를때 마다 "렌더링" 문구와 함께 first와 second가 1씩 증가하며 콘솔에 출력되며 first 버튼을 누를 시에는 "렌더링" 문구가 뜬 후 useEffect 코드 안의 코드인 "렌더링 이후" 코드도 실행된다. (디펜던시 리스트인 first 값이 바뀌었기 때문에 렌더링 이후에 useEffect안의 코드가 실행된 것)

* 마찬가지로 위의 코드에서 디펜던시 리스트를 [first, second]로 바꾼 후 실행하면 second 버튼을 눌러도 "렌더링" 문구가 뜬 후 useEffect 코드 안의 코드인 "렌더링 이후" 코드도 실행된다.
