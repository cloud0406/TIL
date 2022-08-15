# TypeScript

## Why TypeScript?

- 안정성 (타입 안정성)

  javascript는 자유도가 높음

  ```js
  // 허용되지 않는 값을 허용해버림

  [1, 2, 3, 4] + false; // '1,2,3,4false'

  function divide(a, b) {
    return a / b;
  }

  divide(2, 3); // 0.66666666

  // 입력값이 2개이고 숫자여야하지만 다음의 경우도 에러를 발생하지 않고 그냥 실행시켜 버림
  divide("xxxxx"); // NaN
  ```

  typescript에서는 위의 코드가 실행조차 되지 않도록 미리 막아줌 (빨간줄로 에러 발생 사용자에게 알려줌)

- typescript는 두가지 접근 방식을 결합

  - 데이터와 변수의 타입을 명시적으로 정의
  - 혹은 그냥 javascript처럼 변수만 생성 (typescript가 타입을 추론)

    ```ts
    let a = "hello"; // typescript가 변수 a가 string일거라 추론함

    a = "bye"; // string -> string 허용
    a = 1; // 에러발생 , string -> number 허용 안됨 (js에서는 가능)

    let b: boolean = true; // 명시적으로 정의

    b = false; // 가능
    b = "x"; // 에러 발생

    let c = [1, 2, 3]; // let c : number[] = [1,2,3]
    c.push("1"); // 에러 발생, typescript가 변수 c의 타입을 number로 이루어진 array라고 추정하여 string이 들어오는 것을 막음

    const player = {
      name: "nico",
    };
    player.hello(); // 실행되지 못하도록 에러 발생
    ```

  - 보통 typescript가 스스로 추론하는 방법을 추천 (코드를 덜 쳐도 되고 가독성도 좋음)

- Optional Parameter

  ```ts
  // string은 반드시 필요하고 age는 있어도되고 없어도 될 때 -> ?(optional parameter) 사용
  const player: {
    name: string;
    // ?를 붙이지 않으면 age도 반드시 필요한 값이 되어 에러 발생
    age?: number; // age의 type은 'number' or 'undefined'
  } = {
    name: "nico",
  };

  // player.age가 undefined이므로 에러 발생
  if (player.age < 10) {
  }

  // 정상 동작
  if (palyer.age && player.age < 10) {
  }
  ```

- Alias(별칭)

  많은 타입을 재사용할 수 있도록 함

  ```ts
  // 변수명 첫 번째 글자 대문자로 작성
  type Player = {
    name: string;
    age?: number;
  };

  const nico: Player = {
    name: "nico",
  };

  const lynn: Player = {
    name: "lynn",
    age: 12,
  };

  function playerMaker(name: string) {
    return {
      name, // name:name (변수명과 vlaue같을때는 축약해서 사용)
    };
  }

  const nico = playerMaker("nico");
  nico.age = 12; // age값이 없으므로 에러 발생

  function playerMaker2(name: string): Player {
    return {
      name,
    };
  }
  // const playerMaker2 = (name:string) : Player => {{name}}

  const nico = playerMaker2("nico");
  nico.age = 12; // nico가 Player타입이라는 것을 알기 때문에 age값을 바꾸도록 허용 해줌
  ```

- readonly

  ```ts
  type Player = {
    readonly name: string;
    age?: number;
  };

  const playerMaker2 = (name: string): Player => {
    {
      name;
    }
  };

  const nico = playerMaker2("nico");
  nico.age = 12;
  nico.name = "las"; // 에러 발생 (readonly로 선언된 변수는 수정 불가)

  const numbers: readonly number[] = [1, 2, 3, 4];
  numbers.push(1); // 에러 발생
  ```

- Tuple

  튜플은 array를 생성할 수 있게 해주며 최소한의 길이를 가져야하고 특정 위치에 특정 타입이 있어야 한다.

  ```ts
  // 항상 정해진 갯수의 요소를 가져야 하는 array를 지정할 수 있음
  const player: [string, number, boolean] = ["nico", 1, true];

  player[0] = 1; // 에러 발생
  player[0] = "hi"; // 정상 동작

  const player2: readonly [string, number, boolean] = ["nico", 1, true];

  player2[0] = "hi"; // 에러 발생
  ```

- any

  비어있는 값들을 쓰면 기본값으로 any 타입 부여 (typescript의 보호장치로부터 빠져나오고 싶을 때 사용 : typescript -> javascript)

  ```ts
  let arr = []; // any타입

  const a = [1, 2, 3, 4];
  const b = true;
  a + b; // 에러 발생

  const a: any[] = [1, 2, 3, 4];
  const b: any = true;
  a + b; // 에러가 발생하지 않음
  ```

- unknown

  변수의 타입을 미리 알지 못 할때 사용

  ```ts
  let a: unknown;

  let b = a + 1; //에러 발생

  if (typeof a === "number") {
    //정상 동작
    let b = a + 1;
  }

  if (typeof a === "string") {
    //정상 동작
    let b = a.toUpperCase();
  }
  ```

- void

  - 아무것도 return하지 않는 함수를 대상으로 사용
  - typescript에서 함수가 아무것도 return하지 않으면 자동으로 void로 인식하기 때문에 별도로 지정해주지 않아도 됨

  ```ts
  function hello() {
    // void
    console.log("x");
  }
  const a = hello();
  a.toUpperCase(); // 에러 발생
  ```

- never

  함수가 절대 return하지 않을 때 발생 (함수에서 예외가 발생할 때)

  ```ts
  function hello(): never {
    throw new Error("xxx");
  }

  function hi(name: string | number) {
    if (typeof name === "string") {
      // name은 string 타입
    } else if (typeof name === "number") {
      // name은 number 타입
    } else {
      // 실행되서는 안됨
      // name은 never 타입
    }
  }
  ```
