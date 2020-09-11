## 우아한 테크러닝 React&TypeScript 4회차

2020년 09월 01일 화요일 

## 강의 목표

* 컴포넌트 디자인 및 비동기

- `index.js`

```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

const rootElement = document.getElementById('root');

const sessionList = [
  { id: 1, title: '1회차: Overview' },
  { id: 2, title: '2회차: Redux 만들기' },
  { id: 3, title: '3회차: React 만들기' },
  { id: 4, title: '4회차: 컴포넌트 디자인 및 비동기' },
];

ReactDOM.render(
  <React.StrictMode>
    <App store={{sessionList}} />
  </React.StrictMode>,
  rootElement
);
```

- 클래스형 `App.js`

```js
export default class App extends React.Component {
  constructor(props) {
    super(props);

    this.onToggleDisplayOrder = this.onToggleDisplayOrder.bind(this);
    this.toggleDisplayOrder = this.toggleDisplayOrder.bind(this);

    this.state = {
      displayOrder: 'ASC',
    }
  }

  onToggleDisplayOrder() {
      this.setState({
          displayOrder:displayOrder === 'ASC' ? 'DESC' : 'ASC',
      })
  }

  toggleDisplayOrder = () => {
    this.setState({
      displayOrder: displayOrder === 'ASC' ? 'DESC' : 'ASC',
    })
  }

  render() {
    return (
      <div>
        여기여기
        <button onClick={this.toggleDisplayOrder}>정렬</button>
      </div>
    )
  }
}
```

> `Arrow Function` 은 lexical scope 를 따르기에 this 를 bind 해줄 필요가 없다.(this 가 고정된다.)

---

- 함수형 `App.js`


```js
import React, { useState } from 'react';

const SessionItem = ({ title }) => <li>{title}</li>;

const App = props => {
  const [displayOrder, toggleDisplayOrder] = useState('ASC');
  const { sessionList } = props.store;
  const orderedSessionList = sessionList.map((session, i) => {
    ...session,
    order: i,
  })

  const onToggleDisplayOrder = () => {
    toggleDisplayORder(displayOrder === 'ASC' ? 'DESC' : 'ASC');
  }

  return (
    <div>
      <header>
        <h1>React & TypeScript</h1>
      </header>
      <p>전체 세션 갯수: 4개 {displayOrder}</p>
      <button onClick={onToggleDisplayOrder}>재정렬</button>
      <ul>
        {sessionList.map(session => <SessionItem title={session.title} />)}
      </ul>
    </div>
  )
}

export default App;
```
>`상태(state)`는 클래스형 컴포넌트에서만 사용 가능했다. 하지만 hook(useState)이 나온 이후엔 함수형 컴포넌트도 상태를 가질 수 있다.

### 제너레이터(Generator)와 비동기(Asynchronous)

- `Promise`
```js
const p = new Promise(function(rseolve, reject) {
  // 
  setTimeout(() => {
    resolve('1');
  }, 1000);
})

p.then(function(r) {
  console.log(r); // 1
})
```

- `제너레이터 함수(코루틴)`

함수는 인자(입력값)를 받고 무언가 계산을 한 뒤에 값을 return 하는 것이다. 함수인데 return 을 여러번 할 수 있게하면 어떨까? 마지막 return 지점부터 다시 시작(반복)되는 컨셉이다.

`yield` 는 제너레이터 함수의 return 과 유사하다고 생각하자. 함수를 종료시키진 않지만 정지시키고 바깥으로 내보낸다? 정도로 이해. `next` 메소드를 사용하면 다음 번 `yield` 가 있는 곳까지 실행한다. 제너레이터 함수의 특징은 return 으로 종결되지 않으니 값을 쥐고 있다는 점!

> `코루틴`이란 caller 가 함수를 call 하고, 함수가 caller 에게 값을 return 하면서 종료하는 것에 더해
> return 하는 대신 suspend(혹은 yield)하면 caller 가 나중에 resume 하여 중단된 지점부터 실행을 이어갈 수 있다.

```js
function* makeNumber() {
  let num = 1;

  while(true) {
    yield num++;
  }
}

const i = makeNumber();

console.log(i.next()); // { value: 1, done: false }
console.log(i.next()); // { value: 2, done: false }

const delay = ms => new Promise(resolve => setTimeout(resolve, ms));

function* main() {
  console.log('시작');
  yield delay(3000);
  console.log('3초 뒤');
}

const iter = main();
const { value } = iter.next(); // value: Promise
value.then(() => {
  iter.next();
})
```

- `비동기 async await`

제너레이터 함수와 유사하다. `async` 키워드와 함께 `yield` 키워드가 await 가 된 정도.

async await 는 Promise 에 최적화 되어있는 함수. 제너레이터는 Promise 비동기 뿐 아니라 다양한 케이스에서 사용이 가능하기에 응용 범위가 더 넓다. 포인트는 `async` 내부도 제너레이터로 이뤄져있다는 점!

```js
const delay = ms => new Promise(resolve => setTimeout(resolve, ms));

async function main() {
  console.log('시작');
  await delay(3000);
  console.log('3초 뒤');
}
```
---

### 수업 내용 Reference

### 반복기 이해하기 

배열을 반복할 때 `for...in`과 `for...of` 구문을 사용할 수 있다.<br/>
이 중 `for...of` 구문은 아래처럼 배열에 담긴 값을 차례로 얻는데 쓰인다.<br/>

```typescript
const numArray: number[] = [1, 2, 3];

for (let value of numArray) {
    console.log(value); // 1  2 3
}

const strArray: string[] = ["Hello", "World", "!"];

for (let value of strArray) {
    console.log(value); // Hello World !
}
```

`for...of` 구문은 다른 프로그래밍 언어에서도 **반복기**(**Iterator**)라는 주제로 볼 수 있다.<br/>
대부분 프로그래밍 언어에서 **반복기**는 아래와 같은 특징이 있는 객체다.<br/>

-   `next`라는 이름의 메서드를 제공한다.
-   `next`메서드는 `value`와 `done`이라는 두 개의 속성을 가진 객체를 반환한다.<br/>

### 반복기는 왜 필요한가?

앞의 예제의 실행 결과는 `1`부터 `3`까지의 정수를 출력한다.<br/>
즉 `next` 메서드가 반복 **호출될 때마다 각각 다른 값**이 출력된다.<br/>
**반복기 제공자**가 생성한 값을 배열에 담지 않고 마치 `for`문을 돌며 값을 출력한 것처럼 보인다.<br/>
**반복기 제공자**는 이처럼 어떤 범위의 값을 한꺼번에 생성하지 않고 **값이 필요할 때만 생성**한다.<br/>

### `function*`

function* 선언 (끝에 별표가 있는 function keyword) 은 generator function 을 정의하는데, 이 함수는 Generator 객체를 반환합니다.

* `설명`

Generator는 빠져나갔다가 나중에 다시 돌아올 수 있는 함수입니다. 이때의 컨텍스트(변수 값)은 출입과정에서 저장된 상태로 남아 있습니다.

> 컨텍스트가 저장된 상태로 남아있기 때문에 상태의 느낌으로 생각 할 수도 있다.

Generator 함수는 호출되어도 즉시 실행되지 않고, 대신 함수를 위한 <a href=https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#iterator>Iterator</a>객체가 반환됩니다.Iterator의 `next()` 메서드를 호출하면 Generator함수가 실행되어 `yield`문을 만날 때까지 진행하고, 해당 표현식이 명시하는 Iterator로 부터의 반환값을 반환합니다. `yield`의 표현식을 마주칠 경우, 다른 Generator함수가 위임되어 진행됩니다.

이후 `next()` 메서드가 호출되면 진행이 멈췄던 위치에서부터 재실행합니다. 는 yield문이 반환할 값(yielded value)을 나타내는 `value` 속성과, Generator 함수 안의 모든 yield 문의 실행 여부를 표시하는 boolean 타입의 `done` 속성을 갖습니다. ```next()``` 를 인자값과 함께 호출할 경우, 진행을 멈췄던 위치의 `yield` 문을  `next()` 메서드에서 받은 인자값으로 치환하고 그 위치에서 다시 실행하게 됩니다.

### `Generator`

* `설명`
Generator 객체는 generator function 으로부터 반환된 값이며 반복자와 반복자 프로토콜을 준수합니다.

### `yield`

yield 키워드는 제너레이터 함수 (function* 또는  레거시 generator 함수)를 중지하거나 재개하는데 사용됩니다.

* `설명`

`yield` 키워드는 제너레이터의 함수의 실행을 중지시키거난 yield 키워드 뒤에오는 표현식의 값은 제너레이터의 caller로 반환된다. 

> 제너레이터 버전의 `return`이라고 생각하면된다. 하지만 yield는 return과 다르게 함수 그대로를 끝내지 않는다.

yield 키워드는 실질적으로  `value` 와 `done` 이라는 두 개의 속성을 가진 `IteratorResult` 객체를 반환한다. value 속성은 yield 표현(expression)의 실행 결과를 나타내고, `done` 속성은 제너레이터 함수가 완전히 종료되었는지 여부를 불린(Boolean) 형태로 보여준다.

 * `yield` 는 제너레이터가  한번 멈추게 하고 제너레이터의 새로운 값을 반환한다. 다음번의 next()가 호출된 후, yield 이후에 선언된 코드가 바로 실행된다.
* `throw`는 제네레이터에서 예외를 설정할 때 사용된다. 예외가 발생할 경우 제너레이터의 전체적으로 실행이 중지되고, 그리고  다시 켜는 것이 일반적으로 실행된다.
* 제너레이터 함수가 종료가 되었다; 이 경우, 제너레이터 실행이 종료되고  IteratorResult는 caller 로  값이 `undefined`이고 done의 값이 `true` 로 리턴한다.
* return 문에 도달했다. 이 경우에는, 이 값이 종료되고 `IteratorResult`는 caller 로 `return` 문에 의해 반환되는 값과 done의 값이 true  로 리턴한다.



### 수업 내용 외 코멘트, Q&A 기타

1. 아주 기본적인 `소프트 스킬(Soft Skill)` 중 커뮤니케이션에 있어서 가장 중요한 건 `약속(프로토콜, Protocol)`이라고 생각한다. 함께 일하다보면 그게 맞지 않는 경우가 많은데 의식조차 못하는 사람도 대다수. 정보를 주고받는다는 건 `이해의 수준(레이어, Layer)`이 일치(유사, 동등)해야한다. 비개발자에게 개발 언어를 사용하면 이해하는 것이 어렵 듯 상대에 따라 단어 선택도 바꿔야할 필요가 있다. `상대(Target Layer)` 를 잘 이해(의식)하고 커뮤니케이션을 할 수 있길, 커뮤니케이션뿐 아니라 어떤 분야던.

2. 학습도 물론 마찬가지. 교육 과정이나 강의 등을 고를 때를 예로 들면 유명하고 인기 있는(핫한) 게 좋은 것이 아니라 `내가 이해할 수 있는 영역인지?`, `정말 필요한지?` 가 중요하다고 받아들여진다. 고급 테크닉만 쫓는다고 좋은 건 아니다. 주니어 시절에 갖추는 본질과 기본이 중요하다.(편식 No! 차곡차곡!)

3. `제너레이터(Generator)`는 비동기를 동기적으로 사용하기 위해 만든 것이 아니라 단지 `코루틴`이라는 매커니즘을 활용해 만든 것. 만들고 보니 `비동기를 동기처럼 사용할 수 있는 방식으로 사용이 가능하겠다.`하여 사용하는 것이지 단지 그 목적으로 만들어진 것이 아니다. 뭐가 우선인지 컨셉을 이해하는 게 정말 중요하다.

### 레퍼런스
-[](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/yield)
-[](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/function*)
-[](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Generator)
- [](https://github.com/soongyu/woowa-tech-learning-react-typescript/blob/master/week02-2.md)
-[](https://gist.github.com/ibare/c7020756170aa7ed3d1cc84f86972409)

