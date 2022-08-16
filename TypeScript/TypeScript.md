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

<hr>

## Functions

### Call Signatures

함수위에 마우스를 올렸을 때 보이는 함수 호출 방법 (인자의 타입과 반환 타입)

```ts
// call signature
type Add = (a: number, b: number) => number;

const add: Add = (a, b) => a + b;
```

### Overloading

함수가 서로 다른 여러개의 call signatures를 가지고 있을 때 발생

```ts
type Add = {
  (a: number, b: number): number;
  (a: number, b: string): number;
};

const add: Add = (a, b) => a + b; // 에러 발생 -> b의 타입이 number일수도 string일수도 있기 때문에

// 다른 call signature를 가지지만 같은 파라미터 개수를 가짐
type Config = {
  path: string;
  state: object;
};

type Push = {
  (path: string): void;
  (config: Config): void;
};

const push: Push = (config) => {
  if (typeof config === "string") console.log(config);
  else console.log(config.path, config.state);
};

// 다른 call signature에 파라미터의 개수도 다름
type Add = {
  (a: number, b: number): number;
  (a: number, b: number, c: number): number;
};

const add: Add = (a, b, c) => {
  // 에러 발생 -> c가 아마도 number일 것이라 명시해줘야 함
  return a + b;
};

const add: Add = (a, b, c?: number) => {
  // 정상 동작
  if (c) return a + b + c;
  return a + b;
};
```

### Polymorphism(다형성) - generic

다양한 형태를 가짐

```ts
type SuperPrint = {
  (arr: number[]): void;
  (arr: boolean[]): void;
};

const superPrint: SuperPrint = (arr) => {
  arr.forEach((i) => console.log(i));
};

superPrint([1, 2, 3, 4]);
superPrint([true, false, true]);
superPrint([1, 2, true, false]); // 에러 발생 -> generic 사용해서 해결

// generic : call signature를 작성하는데 타입을 알 수 없을 때 사용 -> 사용자가 요구하는대로 call signature를 생성함
// 타입스크립트는 제네릭이 처음 사용되는 지점을 기반으로 타입이 무엇인지 알게 됨
type SuperPrint = {
  <T>(arr: T[]): T;
};

const superPrint: SuperPrint = (arr) => {
  arr.forEach((i) => console.log(i));
};

// generic을 통해 typescript가 타입을 추론
superPrint([1, 2, 3, 4]);
superPrint([true, false, true]);
superPrint([1, 2, true, false]);

const superPrint2: SuperPrint = (arr) => arr[0]

const a = superPrint2([1, 2, 3, 4]); // a: number
const b = superPrint2([true, false, true]); // b: boolean
const c = superPrint2([1, 2, true, false]); // c: number | boolean

c.toUpperCase() // 실행전 보호 받음 (제네릭이 아닌 any타입으로 할 경우 보호받지 못하므로 제네릭을 사용해야 함)


// generic을 여러개 사용할 수 있음
type SuperPrint = <T,M>(a: T[], b: M): T;

const superPrint: SuperPrint = (a) => a[0]

const a = superPrint2([1, 2, 3, 4], "x");
const b = superPrint2([true, false, true],1);
const c = superPrint2([1, 2, true, false], false);



type A = Array<number> // number로 이루어진 Array
let a:A = [1,2,3,4];

function printAllNumbers(arr: Array<number>){ // Array<number> -> number[]

}
```

## Classes

추상클래스, 추상메소드, protected, public, private는 TypeScript에서 제공하는 기능으로 JavaScript로 번역할 경우는 나타나지 않음

```ts
// 추상클래스 : 다른 클래스가 상속받을 수 있는 클래스 (직접 새로운 인스턴스를 만들 수 없음)
abstract class User{
    constructor(
        // private는 외부, 자식 클래스 내에서 모두 접근 불가, protected는 외부에선 접근 불가하지만 상속 받은 자식 클래스내에서는 접근 가능, 별도 선언 없을 경우 기본적으로 public
        private firstName: string,
        private lastName: string,
        protected nickname: string
    ){}
    // 메소드
    private getFullName(){
        return `${this.firstName} ${this.lastName}`
    }
     // 추상 클래스안에서 추상 메소드를 만들 수 있음 -> 이때 메소드를 구현하면 안되고 call signature만 적어둬야 함
     // 추상 메소드란 추상 클래스를 상속받는 모든 인스턴스들이 구현을 해야하는 메소드
     abstract getNickName():void

}

const nico = new User("nico", "las", "니꼬"); // 에러 발생 -> 추상클래스로 새로운 인스턴스를 만들 수 없기 때문에

class Player extends User {
    // 추상 클래스를 상속받을 경우 추상 메소드를 반드시 구현해줘야 함
    getNickName)(){
        console.log(this.nickname); // nickname이 protected이므로 자식 클래스내에서 접근이 가능, firstName이나 lastName은 private이므로 접근 불가
    }
}

const nico = new Player("nico", "las", "니꼬"); // 정상 작동

nico.getFullName() // 에러 발생 -> 추상클래스의 메소드 (getFullName())가 private로 선언되었기 때문에 사용 불가
nico.nickname // 에러 발생 -> protected도 외부에선 접근 불가
```

```ts
// 제한된 양의 property 혹은 key를 가지는 타입을 정의해주는 법 -> property의 이름은 모르지만 타입만은 알 때 사용
type Words = {
    [key:string]: string
}

class Dict :Words = {
    private words: Words
    constructor(){ // words를 initializer없이 선언하고 constructor에서 수동으로 초기화해도 됨
        this.words = {}
    }
    add(word: Word){ // class도 type처럼 사용 가능
        // 사전에 해당 단어가 없다면 추가
        if(this.words[word.term] === undefined) {
            this.words[word.term] = word.def;
        }
    }
    def(term: string){
        return this.words[term]
    }
}

class Word {
    constructor(
        public readonly term: string,
        public readonly def : string
    ){}
}

const kimchi = new Word("kimchi", "한국의 음식");
kimchi.def = "xxx" // 에러 발생 -> readonly이므로 수정 불가

const dict = new Dict()

dict.add(kimchi)
dict.def("kimchi"); // '한국의 음식'

```

## Interfaces

- interface는 object의 모양을 특정해주기 위함 (class와 비슷)
- type과 비교하여 type은 더 유연하게 사용가능하고, interface는 class와 비슷하며 type과는 다르게 중복선언하여 property들을 축적할 수 있다.

```ts
// 아래와 같은 방법으로도 type 사용 가능
type Team = "red" | "blue" | "yellow"
type Health = 1 | 5 | 10

type Player = {
    nickname: string,
    team: Team
    health: Health
}

const nico : Plyaer = {
    nickname:"nico",
    team: "red" // red, blue, yellow 외에는 에러 발생
    health: 10 // 1,5,10 외에는 에러 발생
}
```

```ts
// 위의 코드 예시를 interface를 활용하여 변경할 수 있음
type Team = "red" | "blue" | "yellow"
type Health = 1 | 5 | 10

// 에러 발생 -> 인터페이스는 오로지 object의 모양을 설명해주기 위해서만 사용 가능
interface Hello = string;

interface Player {
    nickname: string,
    team: Team,
    health: Health
}

const nico : Plyaer = {
    nickname:"nico",
    team: "red" // red, blue, yellow 외에는 에러 발생
    health: 10 // 1,5,10 외에는 에러 발생
}
```

```ts
interface User {
  name: string;
}

interface Player extends User {
  // interface도 상속 가능
}

const nico: Plyaer = {
  name: "nico",
};

// interface는 property 들을 축적시킬 수 있음
interface User {
  name: string;
}
interface User {
  lastName: string;
}
interface User {
  health: number;
}

const nico: User = {
  name: "nico",
  lastName: "n",
  health: 10,
};

// type으로는 축적 불가능
type User = {
  name: string;
};
type User = {
  // 중복으로 인한 에러 발생
  lastName: string;
};
```

- 인터페이스를 사용할 경우 ts->js 번역시, 인터페이스와 관련된 코드들은 자바스크립트 코드로 컴파일되지 않음 (추상 클래스등을 사용하지 않아 코드가 가벼워지고 파일 사이즈를 줄일 수 있음 -> 추상 클래스는 자바스크립트에서 일반적인 클래스로 변경됨)
- 하지만 private, protected 를 사용할 수 없고 constructor가 없음

```ts
interface User {
  firstName: string;
  lastName: string;
  sayHi(name: string): string;
  fullName(): string;
}

interface Human {
  health: number;
}

// extends 대신 implements를 사용할 경우 코드가 가벼워짐(인터페이스는 타입스크립트에서만 존재하기 때문에 -> 자바스크립트에서는 존재하지 않는 문법)
// 인터페이스를 상속할 때에는 property를 private나 protected로 만들지 못함 -> public만 가능
class Player implements User, Human {
  constructor(
    public firstName: string,
    public lastName: string,
    public health: number
  ) {}
  fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
  sayHi(name: string) {
    return `Hello ${name}. My name is ${this.fullName()}`;
  }
}
```

```ts
// 제네릭을 클래스로 보내고, 클래스는 제네릭을 인터페이스로 보낸 뒤에 인터페이스가 제네릭을 사용하는 순서로 동작
interface SStorage<T> {
  [key: string]: T;
}
class LocalStorage<T> {
  private storage: SStorage<T> = {}; // 제네릭을 인터페이스로 보냄
  set(key: string, value: T) {
    this.storage[key] = value;
  }
  remove(key: string) {
    delete this.storage[key];
  }
  get(key: string): T {
    return this.storage[key];
  }
  clear() {
    this.storage = {};
  }
}

const stringsStorage = new LocalStorage<string>(); // 제네릭에 string 타입 전달
stringsStorage.get("key"); // string타입의 결과를 리턴 받음
stringsStorage.set("hello", "hi"); // 타입스크립트가 set 메소드의 제네릭 타입의 value를 string타입의 value로 바꿔줌 -> string값만 인자로 허용

const booleansStorage = new LocalStorage<boolean>(); // 제네릭에 boolean 타입 전달
stringsStorage.get("xxx"); // boolean타입의 결과를 리턴 받음
stringsStorage.set("hello", true); // 타입스크립트가 set 메소드의 제네릭 타입의 value를 boolean타입의 value로 바꿔줌 -> boolean값만 인자로 허용
```
