# var, let, const 차이

## 1. 변수 선언 방식

var는 변수 선언 방식에 있어서 큰 단점을 가지고 있음

```ts
var name = "bathingape";
console.log(name); // bathingape

var name = "javascript";
console.log(name); // javascript
```

위의 예시에서 변수를 한 번 더 선언했음에도 불구하고, 에러가 나오지 않고 각기 다른 값이 출력된다.

이는 유연한 변수 선언으로 간단한 테스트에는 편리 할 수 있겠으나, 코드량이 많아 진다면 어디에서 어떻게 사용 될지도 파악하기 힘들뿐더러 값이 바뀔 우려가 있기 때문에 ES6 이후, 이를 보완하기 위해 let 과 const 변수 선언 방식이 추가됬다.

```ts
let name = "bathingape";
console.log(name); // bathingape

let name = "javascript";
console.log(name);
// Uncaught SyntaxError: Identifier 'name' has already been declared
```

name이 이미 선언 되었다는 에러 메세지가 나온다. (const도 마찬가지) -> 변수 재선언이 되지 않는다.

### let과 const의 차이

이 둘의 가장 큰 차이점은 immutable 여부이다.

let 은 변수에 재할당이 가능

```ts
let name = "bathingape";
console.log(name); // bathingape

let name = "javascript";
console.log(name);
// Uncaught SyntaxError: Identifier 'name' has already been declared

name = "react";
console.log(name); //react
```

const는 변수 재선언, 변수 재할당 모두 불가능

```ts
const name = "bathingape";
console.log(name); // bathingape

const name = "javascript";
console.log(name);
// Uncaught SyntaxError: Identifier 'name' has already been declared

name = "react";
console.log(name);
//Uncaught TypeError: Assignment to constant variable.
```

## 2. 호이스팅

호이스팅(Hoisting)이란, var 선언문이나 function 선언문 등을 해당 스코프의 선두로 옮긴 것처럼 동작하는 특성을 말함.

자바스크립트는 ES6에서 도입된 let, const를 포함하여 모든 선언(var, let, const, function, function\*, class)을 호이스팅한다.

하지만, var 로 선언된 변수와는 달리 let 로 선언된 변수를 선언문 이전에 참조하면 참조 에러(ReferenceError)가 발생한다.

```ts
console.log(foo); // undefined
var foo;

console.log(bar); // Error: Uncaught ReferenceError: bar is not defined
let bar;
```

이는 let 로 선언된 변수는 스코프의 시작에서 변수의 선언까지 일시적 사각지대(Temporal Dead Zone; TDZ)에 빠지기 때문이다.

참고로, 변수는 선언 단계 > 초기화 단계 > 할당 단계 에 걸쳐 생성되는데

var 으로 선언된 변수는 선언 단계와 초기화 단계가 한번에 이루어진다.

```ts
// 스코프의 선두에서 선언 단계와 초기화 단계가 실행된다.
// 따라서 변수 선언문 이전에 변수를 참조할 수 있다.

console.log(foo); // undefined

var foo;
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```

let 로 선언된 변수는 선언 단계와 초기화 단계가 분리되어 진행된다.

```ts
// 스코프의 선두에서 선언 단계가 실행된다.
// 아직 변수가 초기화(메모리 공간 확보와 undefined로 초기화)되지 않았다.
// 따라서 변수 선언문 이전에 변수를 참조할 수 없다.

console.log(foo); // ReferenceError: foo is not defined

let foo; // 변수 선언문에서 초기화 단계가 실행된다.
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```

## 3. 정리

그렇다면, 어떤 변수 선언 방식을 써야할까?

변수 선언에는 기본적으로 const를 사용하고, 재할당이 필요한 경우에 한정해 let을 사용하는 것이 좋다.

그리고 객체를 재할당하는 경우는 생각보다 흔하지 않기 때문에 const를 사용하면 의도치 않은 재할당을 방지해 주기 때문에 let을 사용하는 것보다 안전하다.

재할당이 필요한 경우에 한정해 let 을 사용하며 이때, 변수의 스코프는 최대한 좁게 만든다.
