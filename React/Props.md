# Props

## Props란? <hr>
* JSX 문법에서 컴포넌트를 작성할 때 컴포넌트에도 속성을 지정할 수 있는데, 이렇게 **리액트에서 컴포넌트에 지정한 속성들을 Props(Properties의 약자) 라고 하며** 컴포넌트에 속성을 지정해주면 각 속성이 하나의 객체로 모여 컴포넌트를 정의한 함수의 첫 번째 파라미터로 전달된다.   
ex) App.js
  ```js
  import Dice from './Dice';

  function App() {
    return (
      <div>
        <Dice color="blue" />
      </div>
    );
  }

  export default App;
  ```

  ex) Dice.js
  ```js
  import diceBlue01 from './assets/dice-blue-1.svg';

  function Dice(props) {
    console.log(props)
    return <img src={diceBlue01} alt="주사위" />;
  }

  export default Dice;
  ```

* 위의 코드들 처럼 App 함수에서 Dice 컴포넌트에 color 속성을 blue로 지정해준 후 Dice 함수 내부에서 props라는 파라미터를 하나 생성하여 출력해보면 콘솔에서 다음과 같은 결과를 보여준다.

  ```js
  { color: "blue" }
  ```
* 이렇게 컴포넌트를 활용할 때 속성값을 다양하게 전달하고 props값을 활용하면, 같은 컴포넌트라도 전달된 속성값에 따라 서로 다른 모습을 그릴 수 있게 된다.

  ex) App.js
  ```js
  import Dice from './Dice';

  function App() {
    return (
      <div>
        <Dice color="red" num={2} />
      </div>
    );
  }

  export default App;
  ```

  ex) Dice.js
  ```js
  import diceBlue01 from './assets/dice-blue-1.svg';
  import diceBlue02 from './assets/dice-blue-2.svg';
  // ...
  import diceRed01 from './assets/dice-red-1.svg';
  import diceRed02 from './assets/dice-red-2.svg';
  // ...

  const DICE_IMAGES = {
    blue: [diceBlue01, diceBlue02],
    red: [diceRed01, diceRed02],
  };

  function Dice(props) {
    const src = DICE_IMAGES[props.color][props.num - 1];
    const alt = `${props.color} ${props.num}`;
    return <img src={src} alt={alt} />;
  }

  export default Dice;
  ```
* 이렇게 같은 Dice 컴포넌트라도 속성값을 다르게 하여 빨간색 주사위와 파란색 주사위로 표현이 가능   
     
* 참고로 props는 객체 형태이므로 Destructuring 문법을 활용하여 조금 더 간결하게 코드를 작성할 수도 있다. 아래 예시를 보자   
  ex) Dice.js
  ```js
  import diceBlue01 from './assets/dice-blue-1.svg';
  import diceBlue02 from './assets/dice-blue-2.svg';
  // ...
  import diceRed01 from './assets/dice-red-1.svg';
  import diceRed02 from './assets/dice-red-2.svg';
  // ...

  const DICE_IMAGES = {
    blue: [diceBlue01, diceBlue02],
    red: [diceRed01, diceRed02],
  };

  function Dice({ color = 'blue', num = 1 }) {
    const src = DICE_IMAGES[color][num - 1];
    const alt = `${color} ${num}`;
    return <img src={src} alt={alt} />;
  }

  export default Dice;
  ```

   
## Children이란? <hr>
* children이란 props의 조금 특별한 프로퍼티로 JSX 문법으로 컴포넌트를 작성할 시 컴포넌트를 단일 태그가 아닌 여는 태그와 닫는 태그의 형태로 작성하게 되면, 그 안에 작성된 코드가 바로 이 children 값에 담기게 된다.   
ex) Button.js   
  ```js
  function Button({ children }) {
    return <button>{children}</button>;
  }

  export default Button;
  ```   

  ex) App.js
  ```js
  import Button from './Button';
  import Dice from './Dice';

  function App() {
    return (
      <div>
        <div>
          <Button>던지기</Button>
          <Button>처음부터</Button>
        </div>
        <Dice color="red" num={2} />
      </div>
    );
  }

  export default App;
  ```

* JSX 문법으로 컴포넌트를 작성할 때 어떤 정보를 전달할 때는 일반적으로 props의 속성값을 주로 활용하며, 화면에 보여질 모습을 조금 더 직관적인 코드로 작성할 때는 children 값을 활용한다.   

* 또한 children을 활용하면 단순히 텍스트만 작성하는 것이 아닌 컴포넌트 안에 컴포넌트를 작성할 수 있고, 컴포넌트 안에 복잡한 태그들을 더 작성할 수도 있게 된다.