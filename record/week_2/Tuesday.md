## 우아한 테크러닝 2주차 20.09.08

## React 만들어보기

### 간단하게 리액트의 구조 파악
```js
const list = [
  { title: 'React에 대해 알아봅시다.' },
  { title: 'Redux에 대해 알아봅시다.' },
  { title: 'TypeScript에 대해 알아봅시다.' },
];

const rootElement = document.getElementById("root");

function app(items) {
  rootElement.innerHTML = `
    <ul>
      ${items.map(item => `<li>${item.title}</li>`).join("")}
    </ul>
  `;
}

app(list);
```

특정 데이터(list)를 어떠한 형태(app)로 어디(rootElement)에 그려주는 일련의 과정.(위의 간단한 예시와 같이) 위 app 함수는 직접 DOM 에 접근해 다루기에 권장하지 않는다.
규모가 커지면 리팩토링 등 겉잡을 수 없이 개발이 어려워지기 때문이다. 즉 복잡도가 높아진다. 
이런 이유로 Virtual DOM 형태의 프레임워크(라이브러리)들이 개발되어지는 것이다. 그 중 하나가 Facebook 의 [React.js](https://ko.reactjs.org/).

---
### React의 컨셉 
`virtualDom`을 만든 후 `RealDom`(우리가 보편적으로 아는 DOM)으로 변환하는 개념 
react에서 요소를 만드는데 `React.createElement`대신 JSX를 사용하여 `RealDom`으로 변환한다.  

<img width="517" alt="11" src="https://user-images.githubusercontent.com/33803975/92600273-4e671600-f2e6-11ea-8262-50d5617d2583.png">

### React 만들기

> DOM - Virtual DOM - JavaScript, 다루기 쉬운 구조(가상 돔,VDOM)를 사이에 DOM 과 js 사이에 둔다. 또한 사용성에 용이하기 위해 jsx까지 만든 것. 이를 babel 이 트랜스파일링.. 하는 일련의 과정.

`react` 의 createElement 를 간략화한 코드.  
`type` 은 HTML 태그의 type / `props` 는 전달할 props / `children` 은 자식 노드(배열)  
> 가상돔을 만드는 역할, JavaScript 영역에서는 createElement 로 가상돔을 만들어주고 그건 jsx 형태로 만들며 babel 이 이를 컴파일해준다.

```js
function createElement(type, props, ...children) {
  return { type, props, ...children };
}
```

`vdom` 구조 형태 예시
> '까다로운 문자열(html)을 객체로 바꾼다면 편리하지 않을까?' 하는 이유로 Virtual DOM 을 만들었다. 기본적인 리액트 디자인의 형태는 단 하나의 Virtual DOM(트리 형태의 최상위)으로 이뤄져있다.
```js
const vdom = {
  type: 'ul',
  props: {},
  children: [
    { type: 'li', props: { className: "item" }, children: "React" },
    { type: 'li', props: { className: "item" }, children: "Redux" },
    { type: 'li', props: { className: "item" }, children: "TypeScript" },
    { type: 'li', props: { className: "item" }, children: "mobx" },
    // ...
  ],
}
```

`ReactDOM.render` 함수의 역할은 vdom 을 훑으면서 real dom 으로 그려주는(예를 들면 `innerHTML` 과 같은 api로).
```js
ReactDOM.render(<App />, document.getElementByID("root"));
```

> 최상단에 `/* @jsx createElement */` 을 넣어주면 babel 로 트랜스파일링 되는 vdom 의 역할을 줄 수 있다.(트랜스파일러의 역할)
```js
/* @jsx createElement */

// ...
```

---

`App.js`
```js
/* @jsx createElement */
import { createElement, render } from './tiny-react';

function Hello(props) {
  return <li className="item">{props.label}</li>;
}

function App() {
  return (
    <div>
      <h1>Hello World</h1>
      <ul className="board" onClick={() => null}>
        <Hello label="Hello" />
        <Hello label="World" />
        <Hello label="React" />
      </ul>
    </div>
  );
}

render(<App />, document.getElementById("root));
```

`tiny-react.js`
```js
function renderElement(node) {
  if (typeof node === 'string') {
    return document.createTextNode(node);
  }

  const el = document.createElement(node.type);

  node.childern.map(renderElement).forEach(element => {
    el.appendChild(element);
  }); // node 는 Tree 형태이기에 재귀 호출이 필요, children 이 있으면 ? tree 형태의 자식 node 를 만들 것.
  return el;
}

export function render(vdom, container) {
  // 실제 이 영역에서 vdom 과 비교해서 바뀐 경우만 렌더링하는 역할도 포함되어있을 것

  container.appendChild(renderElement(vdom));
  // container.appendChild(el) <- render 에서 return 된 el 이 여기에 들어가는 것.
}

export function createElement(type, props, ...children) {
  if (typeof type === 'function') {
    return type.apply(null, [props, ...children]); // children 의 타입이 배열이므로 apply 를 써야한다.
  }
  return { type, props, children };
}
```

---

### React, 클래스형 컴포넌트 & 함수형 컴포넌트(& 훅)

`index.js`
```js
import React, { useState } from 'react';
import ReactDOM from 'react-dom';

// 클래스형 컴포넌트 예시
class Hello extends React.Component {
  constructor(props) {
    super(props); // 리액트한테 받은 props 를 넘겨주는 역할.

    this.state = {
      count: 1,
    }
  }

  componentDidMount() {
    this.setState({ count: this.state.count + 1 });
  }

  render() {
    return (
      <div>
        <p>안녕하세요!</p>
      </div>
    )
  }
}

// 함수형 컴포넌트, useState 훅 예시
function App() {
  const [counter, setCounter] = useState(1);

  return (
    <div>
      <h1 onClick={() => setCounter(counter + 1)}>상태 {counter}</h1>
      <Hello />
    </div>
  );
}

ReactDOM.render(<App />, document.getElementById("root"));
```

### useState 훅 간단한 매커니즘

useState 가 어떻게 값을 기억하는가? 리액트 createElement 가 호출되는데, 어떤 함수(함수형 컴포넌트)가 내부에서 훅 계열의 함수를 호출 했다면 useState 초기값, 배열의 인덱스를 `훅 전역 배열`에 넣어둔다.  

만약 그 배열 안에 아무것도 들어있지 않다면 initial 값(`useState(value) 의 value`를 사용, 뭔가 들어있다면 **초기값을 무시**하고 그 배열 내부 인덱스를 참조한다.

훅은 리액트 함수형 컴포넌트 내에서만 호출되어야 하고 conditional하게 호출하면 index 를 참조하지 못한다. 컴포넌트가 렌더링 된 순서대로 훅을 호출하는 것이 그 이유이다.

[훅 규칙](https://ko.reactjs.org/docs/hooks-rules.html) 참조.

---

### 수업 내용 외 코멘트, Q&A 기타

1. 라이브러리를 만든 이유와 적용 방안을 생각하면서 사용하는 것과 무턱대고 사용하는 건 크게 차이가 있다.(개발 소비자가 되지 말자. `why` 가 중요해.) 리액트가 어떻게 돌아가게끔 되어 있는지 직접 만들어보자.

2. `같은 것끼리 묶고 다른 것은 분리한다.` 는 아키텍쳐의 기본, 중심이고 역량에 따라 얼마나 잘 묶는지에 차이가 있을 뿐. 어떤 기술을 쓰고 어느정도의 실력인지도 많이 중요하지만 높은 수준부터 바라보지 않고 기본부터 충실하자. 그 기본엔 `네이밍`과 같은 예가 있다.(70%는 먹고 들어가자! 고 표현해주셨다.)

3. 리액트에서 사용자 컴포넌트와 일반 태그의 차이는 대문자로 시작하는지의 여부로 판단한다. 대문자로 시작하는 걸 바벨에서 리액트 사용자 컴포넌트로 이해한다.(아주 간단한 방법이지만 확실하게)

4. 면접에서 회사에 궁금한 점 등 질문을 할 경우 질문이 아예 없으면 면접에서 감점이라는 생각이다. 궁금한 게 없다고? 왜 지원했지? 라고 생각할 수도 있다고 하셨다. (면접 마무리에서 회사에 궁금한 것은 꼭 물어보는 것이 무조건 좋다.)

5. 라이브러리 코드를 까보는 팁은 초기 릴리즈 태그까지 찾아가서 보는 것이다. 초기 모델과 기본 컨셉은 크게 바뀌지 않는데, 방어 코드들이나 코드베이스 확장되어있지 않은 상태(규모가 작은)이기에 더 보기가 좋다.

6. 어떤 부분이 `컴파일 타임`, 어떤 부분이 `런 타임`에 일어나는지 확실히 구분하고 이해하자. 브라우저가 직접 이해할 수 없는 언어로 개발하는 요즘 시대에는 무조건 구분할 수 있어야 한다.

### 레퍼런스
- [강의 예제](https://gist.github.com/ibare/c736f63fba835c172e60aa98a996dada)
- [React Docs](https://ko.reactjs.org/docs)
-(https://pomb.us/build-your-own-react/)
-https://github.com/textuel/Woowa_Tech_Learning_React_Typescript/blob/master/ms/week_2/Tuesday.md