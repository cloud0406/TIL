# State

* State는 리액트에서 화면을 그려내는 데 중요한 역할을 하는 요소로 상태가 바뀔 때마다 화면을 새롭게 그려내는 방식으로 동작한다.
* 리액트에서 State를 만들고 변경하기 위해서는 우선 'useState' 함수를 활용해야 한다.

  ```
  import { useState } from 'react';

  // ...

  const [num, setNum] = useState(1);

  // ...
  ```
 * 위와 같이 Destructuring 문법으로 작성하는데 이는 useState 함수가 초깃값을 argument로 받고 그에 따라 실행 결과로 요소 2개를 가진 배열의 형태로 리턴을 하기 때문이다.
 * 이때 첫 번째 요소가 바로 state이고, 두 번째 요소가 이 state를 바꾸는 setter 함수이다.
 * 위의 코드에서 처럼 첫 번째 변수는 원하는 state의 이름(num을, 두 번째 변수에는 state 이름 앞에 set을 붙인 다음 카멜 케이스로 이름을 지어주는 것(setNum)이 일반적이다.
 * state는 변수에 새로운 값을 할당하는 방식으로 변경하는 것이 아닌 setter 함수를 활용하는데 setter 함수는 호출할 때 전달하는 argument 값으로 state 값을 변경해준다.
    ```
    import { useState } from 'react';
    import Button from './Button';
    import Dice from './Dice';

    function App() {
      const [num, setNum] = useState(1);

      const handleRollClick = () => {
        setNum(3); // num state를 3으로 변경!
      };

      const handleClearClick = () => {
        setNum(1); // num state를 1로 변경!
      };

      return (
        <div>
          <Button onClick={handleRollClick}>던지기</Button>
          <Button onClick={handleClearClick}>처음부터</Button>
          <Dice color="red" num={num} />
        </div>
      );  
    }

    export default App;
    ```
  * 따라서 위의 코드와 같이 setter 함수를 활용하여 이벤트 핸들러를 등록하면, 이벤트가 발생할 때마다 상태가 변하며 화면이 새로 그려지게 된다.   
     

# 참조형 State
* 자바 스크립트의 자료형은 크게 기본형과 참조형으로 나눌 수 있는데 참조형 값들은 state를 활용할 때 주의해야할 점이 있다.
  ```
  const [gameHistory, setGameHistory] = useState([]);

    const handleRollClick = () => {
      const nextNum = random(6);
      gameHistory.push(nextNum);
      setGameHistory(gameHistory); // state가 제대로 변경되지 않는다!
    };
  ```
* 위의 코드처럼 배열값인 gameHistory에 push 메소드를 이용해서 배열의 값을 변경할 경우, 변경된 배열을 setter 함수로 state를 변경하려고 하면 코드가 제대로 동작하지 않는 걸 확인할 수 있다.
* gameHistory state는 배열 값 자체를 가지고 있는 것이 아닌 배열의 주소값을 참조하고 있는 것이므로 push 메소드로 배열안의 요소를 변경해도 결과적으로 참조하는 배열의 주소값이 변경된 것이 아니기 때문이다.
* 따라서 리액트는 gameHistory state가 참조하는 주소값이 여전히 같기 때문에 상태(state)기 바뀌었다고 판단하지 않게 된다. 그래서 **참조형 state를 활용할 때는 반드시 참조형 값을 만들어 state를 변경해야 한다.**
  ```
  // ... 

    const [gameHistory, setGameHistory] = useState([]);

    const handleRollClick = () => {
      const nextNum = random(6);
      setGameHistory([...gameHistory, nextNum]); // state가 제대로 변경된다!
    };

  // ...
  ```
* 위의 코드처럼 Spread 문법(...)을 활용해서 사용하는 방법이 간단하다. 