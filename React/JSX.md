# JSX 문법

## JSX란? <hr>
* JSX는 리액트로 코드를 작성할 시 보다 편리하게 화면에 나타내기위해 사용하는 자바스크립트의 확장 문법이다. 
<br></br>


## JSX문법의 규칙 <hr>
###  1. 속성명 카멜 케이스(Camel Case)로 작성    <br></br>

* 여러 단어가 조합된 속성을 사용할 시 카멜 케이스로 작성해야 한다. 

  ex) onclick -> onClick , onblur -> onBlur , onfocus -> onFocus   
  ```js
  import ReactDOM from 'react-dom';  

  ReactDOM.render(
    <button onClick= ... >클릭!</button>,
    document.getElementById('root')
  );
  ```   
  하지만 비표준 속성을 다룰 때 사용하는 data-* 속성은 카멜 케이스로 작성하지 않고 기존 문법 사용.
  ```js
  import ReactDOM from 'react-dom';

  ReactDOM.render(
    <div>
      상태 변경: 
      <button className="btn" data-status="대기중">대기중</button>
      <button className="btn" data-status="진행중">진행중</button>
      <button className="btn" data-status="완료">완료</button>
    </div>,
    document.getElementById('root')
  );
  ```
   
### 2. 자바스크립트 예약어와 같은 속성명은 사용 불가   <br></br>

* for, class처럼 JS 문법에 해당하는 예약어와 같은 이름의 속성명 사용불가   

   ex) HTML의 for는  JS의 반복문 키워드인 for와 겹치기 때문에 -> htmlFor , 마찬가지로 class -> className으로 사용해야 한다.
   ```js
   import ReactDOM from 'react-dom';

   ReactDOM.render(
    <form>
      <label htmlFor="name">이름</label>
      <input id="name" className="name-input" type="text" />
    </form>,
    document.getElementById('root')  
  );

###  3. 하나의 요소로 감싸주기 ( Fragment : <></> ) <br></br>
* JSX 문법 사용시 Fragment를 사용해서 하나의 요소로 반드시 묶어줘야함

  ```js
  import ReactDOM from 'react-dom';

  ReactDOM.render(
    <>
      <p>안녕</p>
      <p>리액트!</p>
    </>,
    document.getElementById('root')
  );
  ```   


### 4. JS 표현식을 사용할 경우 중괄호 {}를 이용 <br></br>
* 선언해둔 변수 혹은 함수등을 JSX문법에서 중괄호로 감싸줘서 사용가능 ( JS 표현식을 다루는 것이기 때문에 for, if문등 문장은 다룰 수 없음)
* 중괄호 안에 문자열을 조합하거나 변수에 이미지의 주소를 할당하여 속성값을 전달해주는 등  다양하게 쓰임   

  ```js
  import ReactDOM from 'react-dom';

  const product = 'MacBook';
  const model = 'Air';
  const imageUrl = 'https://upload.wikimedia.org/wikipedia/commons/thumb/1/1e/MacBook_with_Retina_Display.png/500px-MacBook_with_Retina_Display.png'

  function handleClick(e) {
    alert('곧 도착합니다!');
  }

  ReactDOM.render(
    <>
      <h1>{product + ' ' + model} 주문하기</h1>
      <img src={imageUrl} alt="제품 사진" />
      <button onClick={handleClick}>확인</button>
    </>,
    document.getElementById('root')
  );
  ```