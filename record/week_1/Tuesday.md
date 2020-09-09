## 우아한 테크러닝 React&TypeScript 1회차

2020년 09월 01일 화요일 

### 강의 목표

1. 내가 지금 잘 하고 있을까? ( 주니어 개발자 공통 고민 )
2. 네트워킹 인프라 
3. 코드 품질, 아키텍처, 적정 기술

### 강의에서 주로 다룰 키워드

-   상태 (State) : 어플리케이션은 즉 상태를 관리하는 것이 주 목표
-   환경 (Env) : 많은 환경에 대한 대응 및 개발 환경에 대한 관심
-   제품 (Product) : 개발을 하는 것뿐만 아니라 제품적인 측면으로 생각
-   목표 (Mission) : 많은 도구를 조합해 만드는 제품이 해결할 미션
-   코드 (Quality) : 코드의 퀄리티를 높일 수 있는 방법
-   상대적 (E=mc^2) : 위에 있는 모든 것은 사람마다 상대적

## 강의 내용

### TypeScript에서 타입을 지정하는 방법

1. 타입 추론을 통한 암시적 타입 지정

```js
let foo = 10;
```
위와 같이 `JavaScript`와 동일하게 코드 작성 할 수 있다.
`타입추론`을 통해 `TypeScript` 또한 별다른 오류를 발생시키지 않는다.

```tsx
let foo = 10;
foo = false;
```

하지만 위와 같이 다른 타입의 값을 대입할 경우 문제가 발생한다.
`foo`변수에 `10`이라는 `number`타입 값이 지정 되면서 다른 타입의 값은 대입할 수 없다.

2. 명시적으로 타입을 지정하는 법

`변수명: 타입`과 같이 사용해 타입을 명시할 수 있다. 

```tsx
let foo:number = 10;
foo = false;
```

다른 타입의 값을 대입할 경우 **타입추론**을 통한 타입 지정과 동일하게 오류가 발생한다.

>읽는 사람을 위한 코드는 명시적이 좋다. 

### 타입 별칭

```js
let age: number = 10;
let weight: number = 72;
```

`age`와 `weight` 변수는 `number` 타입으로 별 문제없이 사용할 수 있다.
하지만 코드의 가독성을 위해 아래와 같이 **타입별칭**을 이용해 타입을 지정할 수 있다.

```tsx
type Age = number; //실질적으로 컴파일이 될때 사라진다.

let a: Age = 10;
```
여기서 type Alias는 실질적으로 컴파일이 될때 사라진다.

변수 `a`는 `Age`와 동일하게 `number`타입이지만 `Age`라는 타입 별칭을 사용해 가독성을 증가시킨다.

> Type Alias를 통해 보다 깔끔한 명시적 추론이 가능하다.

### 객체 타입

객체의 타입은 아래와 같이 `type` 키워드를 사용해 정의할 수있다.

```tsx
type Foo = {
    age: Age;
    name: string;
}

const foo: Foo = {
    age: 10,
    name: 'yoonjangwon',
}
```
객체 타입을 정의하는 다른 방법으로 `interface`키워드가 존재한다.
```tsx
interface Bar {
    age:Age;
    name: string;
}

const bar : Bar = {
    age: 10,
    name: 'yoonjangwon'
}
```
`type` 키워드로 선언한 객체 타입과 동일하게 `interface`를 이용해 객체 타입을 선언할 수 있다.
`type`을 이용한 타입 선언 방식과 `interface`를 이용한 타입 선언 방식은 차이가 존재한다.

### TypeScript&React 프로젝트 생성하기

`npm`패키지의 `create-react-app`을 이용해 간단하게 프로젝트 생성을 할 수 있다.

* yarn

```yarn create react-app <app_name> --template typescript```

* npm
```npx create-react-app <app_name> --template typescript```

### 간단한 React 컴포넌트 만들기

아래와 같이 `function`키워드를 이용해 함수 컴포넌트를 생성할 수 있다.

- `src/index.tsx`

```tsx
import React from "react";
import ReactDom from "react-dom";

function App() {
    return (
        <h1>Jangwon Hello!</h1>
    );
}

ReactDom.render(
    <React.StricMode>
        <App/>
    </React.StrictMode>,
    document.getElementById("root")
)
```

```ReactDom```의 ```render```함수는 virtualDom(가상돔)에 컴포넌트를 그리는 함수이며 두 개의 인자를 받는다.
첫 번째인자는 렌더링될 **컴포넌트며** 두 번째 인자는 렌더링된 컴포넌트를 넣을 **HTML** 요소다.

### Babel을 통한 React 컴포넌트 변환

작성한 `App` 컴포넌트는 `babel`이라는 트랜스파일러를 통해 아래와 같이 변환된다.

 * 변환 전 `App` 컴포넌트
 ```js
 function App(){
     return <h1 id='header'>Jangwon Hello!</h1>;
 }
 ```

 * 변환 후 `App` 컴포넌트
```tsx
function App() {
    return /*@__PURE__*/ React.createElement(
        "h1",
        {
            id: "header",
        },
        "Hello Tech"
    );
}
```
작성한 jsx코드가 위와 같이 React의 createElement함수로 변환된다.

### 작성한 App 컴포넌트의 확장

아래와 같이 `App` 컴포넌트에서 외부에서 가지고 있는 값을 필요로하는 경우가 생길 수 있다.
```jsx
ReactDom.render(
    <React.StricMode>
        <App title="Tech Hello?" color="red"/>
    </React.StrictMode>,
    document.getElementById("root")
)
```
```<App title="Tech Hello?" color="red"/>```와 같이 넘어간 값은 객체 타입으로 전달된다.

```js
fucntion App(props) {
    return <h1 id="header">{props.title}</h1>;
}
```

`jsx` 내부에서 `JavaScript` 코드를 사용하기 위해서는 `{}`를 내부에 작성하면 된다.
```tsx
interface AppProps {
    title: string;
    color: string;
}

function App(props: AppProps) {
    return <h1 id="header">{props.title}</h1>;
}
```

interface를 이용해 App 컴포넌트가 받을 속성의 객체 타입인 AppProps를 정의할 수 있다.
App 컴포넌트에서 받을 속성 객체의 타입을 함수 컴포넌트의 매개변수에 명시할 수 있다.

### 전역적 상태관리 

- 전역적으로 상태를 관리하는 대표적인 아키텍쳐로는 **Flux** 아키텍쳐가 존재한다.
- 이를 개선하고 정형화 한 것이 **redux**라는 패키지이다.
- redux와 비슷하게 전역적으로 상태를 관리하는 패키지는 **MobX**가 존재한다.
- redux와 다르게 MobX는 기능이 많으며 다양한 방법으로 사용할 수 있다.
- 기능이 많다는 뜻은 유연하게 기능들을 사용할 수 있다는 의미를 가지고 있다.
- 반면에 유연하게 기능을 사용할 수 있는 만큼 개발자가 실수를 할 수 있는 여지가 많다.
- 따라서 실수를 줄이기 위해서 기술을 사용하기 전 아키텍쳐 설계 또한 굉장히 중요하다.
