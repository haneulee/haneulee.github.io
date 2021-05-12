---
layout: post
title: "Why Do We Write super(props)?"
date: '2018-12-10'
categories: React study
spoiler: There’s a twist at the end.
categories: [Development, React]
tags: [javascript, hooks, super]

---

# Why Do We Write super(props)?

: 왜 super(props)를 써야 하는가?

요즘에는 [훅스(Hooks)](https://reactjs.org/docs/hooks-intro.html)가 새로운 트렌드이다. 
[클래스 사용 지양]

웃기게도 나는 클래스 컴포넌트에 대한 재미있는 것들을 설명하면서 이 블로그를 시작하고 싶다. 

**이런 것들은(클래스 컴포넌트) 리액트를 생산적으로 사용하는데 별로 중요하지 않다. 그러나 이것들이 어떻게 작동하는지 깊게 알고 싶다면 아마 놀라울 것이다.** 

첫번째,

---

나는 정말 `super(props)`를 내가 알고싶은 것보다 더 많이 사용했다.

```jsx{3}
class Checkbox extends React.Component {
  constructor(props) {
    super(props);
    this.state = { isOn: true };
  }
  // ...
}
```

물론, [클래스 필드 제안](https://github.com/tc39/proposal-class-fields)은 이 과정을 생략하게 한다. 

```jsx
class Checkbox extends React.Component {
  state = { isOn: true };
  // ...
}
```

이런 문법은 리액트 0.13이 2015년 자바스크립트 클래스에 대한 지원이 추가될 때 계획되었다. 
constructor를 정의하고, super(props)를 부르는 것은 클래스 필드가 편리한 대안을 제공하기 전까지 항상 임시적인 해결책이었다. 

그러나 es2015 기능만 사용하는 아래 예제를 보자.

```jsx{3}
class Checkbox extends React.Component {
  constructor(props) {
    super(props);
    this.state = { isOn: true };
  }
  // ...
}
```

왜 우리는 `super`를 사용할까? 사용하지 않으면 안될까?
만약 사용한다 해도 props를 넘기지 않으면 무슨일이 일어날까?
어떤 인자들이 있을까?
찾아보자.

-----

자바스크립트에서는 `super`가 부모 클래스 생성자를 참조한다. 
(여기 예제에서는 React.Component 구현을 가리킨다.)

중요한 것은 부모 생성자를 호출할 때까지 생성자 안에서 this를 사용할 수 없다.

```jsx
class Checkbox extends React.Component {
  constructor(props) {
    // 🔴 Can’t use `this` yet
    super(props);
    // ✅ Now it’s okay though
    this.state = { isOn: true };
  }
  // ...
}
```

왜 자바스크립트가 this 전에 부모 생성자가 실행되게 하는데에는 좋은 이유가 있다. 클래스 계층 구조를 생각해보라.

```jsx
class Person {
  constructor(name) {
    this.name = name;
  }
}

class PolitePerson extends Person {
  constructor(name) {
    this.greetColleagues(); // 🔴 This is disallowed, read below why
    super(name);
  }
  greetColleagues() {
    alert('Good morning folks!');
  }
}
```

`super` 전에 `this`가 사용된다고 상상해봐라. 한 달 후에, 메세지에서 사람의 이름을 사용하기 위해 greetColleagues를 수정할 수 있다.

```jsx
greetColleagues() {
    alert('Good morning folks!');
    alert('My name is ' + this.name + ', nice to meet you!');
}
```

그러나 super() 호출이 this를 설정하기 전에 this.greetColleagues()가 불려진다는 것을 잊었다.
그래서 this.name은 아직 정의되지 않았다. 보다시피, 이런 코드는 생각하기 매우 어렵다. 

이런 문제를 피하기 위해 자바스크립트는 생성자에서 this를 사용하고 싶을 때, super를 먼저 호출하게 한다.
이런 제한은 클래스로 정의된 리액트 컴포넌트에도 해당된다.


```jsx
constructor(props) {
    super(props);
    // ✅ Okay to use `this` now
    this.state = { isOn: true };
}
```

이는 또 다른 질문을 남긴다.
: 왜 props를 전달할까?

기본 React.Component 생성자가 this.props를 초기화할 수 있도록 super를 통해 props를 전달하는 것이 필수라고 생각할 수 있다. 

```jsx
// Inside React
class Component {
  constructor(props) {
    this.props = props;
    // ...
  }
}
```

그리고 그것은 진실과 멀지 않다. [사실 그렇게 하는것이다.](https://github.com/facebook/react/blob/1d25aa5787d4e19704c049c3cfa985d3b5190e0d/packages/react/src/ReactBaseClasses.js#L22) 
그러나 어떻게든 props 인자 없이 super()를 호출하더라도 render 또는 다른 메소드에서도 this.props에 접근할 수 있다. 
(직접 시도해봐라!)
어떻게 이게 가능할까? 리액트는 생성자를 호출한 직후에 인스턴스에 props를 할당하는 것으로 밝혀졌다.

```jsx
// Inside React
const instance = new YourComponent(props);
instance.props = props;
```


그래서 super()에 props를 전달하는 것을 잊어버려도 리액트는 나중에 props를 set 해준다. 여기에는 이유가 있는데,
리액트가 클래스를 지원할 때, 단지 es6 클래스 자체만 지원하는 것이 아니다. 목표는 가능한 한 광범위한 클래스 추상화를 지원하는 것이었다. ClojureScript, CoffeeScript, ES6, Fable, Scala.js, TypeScript 또는 기타 솔루션이 컴포넌트를 정의하는데 얼마나 성공적이었는지 [분명하지 않다](https://reactjs.org/blog/2015/01/27/react-v0.13.0-beta-1.html#other-languages).  
그래서 리액트는 es6 클래스가 그렇더라도 super()를 호출하는 것이 필요한지에 대해 의도적으로 알려주지 않은 것이다. 
그렇다면 `super(props)` 대신 `super()`를 사용할 수 있다는 것인가? 
**여전히 혼란스럽기 때문에 아니다.** 
물론 리액트는 생성자가 *실행된 후에* this.props를 할당할 것이다. 하지만 this.props는 super 호출과 생성자 마지막 사이에서 undefiend가 될 것이다. 

```jsx
// Inside React
class Component {
  constructor(props) {
    this.props = props;
    // ...
  }
}

// Inside your code
class Button extends React.Component {
  constructor(props) {
    super(); // 😬 We forgot to pass props
    console.log(props);      // ✅ {}
    console.log(this.props); // 😬 undefined 
  }
  // ...
}
```

생성자에서 호출된 일부 메소드에서 이 이슈가 발생하면 디버깅하기 어려울 수 있다. 그래서 꼭 필요한 것은 아니지만 항상 `super(props)`를 하는것을 추천한다. 

```jsx
class Button extends React.Component {
  constructor(props) {
    super(props); // ✅ We passed props
    console.log(props);      // ✅ {}
    console.log(this.props); // ✅ {}
  }
  // ...
}
```

이렇게 하면 생성자가 끝나기 전에 `this.props`가 설정된다. 

-----

오랫동안 리액트 사용자들이 궁금해할만한 하나가 있다. 
Context API를 클래스에서 사용할 때(레거시 `contextTypes` 또는 React16.6에 추가된 최신 `contextType API`의 경우) 컨텍스트가 생성자의 두번째 인자로 전달된다는 사실을 알고 있을 것이다. 

그렇다면 왜 우리는 `super(props, context)`를 써야하나?
컨텍스트가 자주 사용되지 않기 때문에 이 문제가 그렇게 많이 발생하지는 않는다. 

**[클래스 필드 제안](https://github.com/tc39/proposal-class-fields)으로 모든 문제는 대부분 없어진다.** 명시적 생성자 없이는 모든 인자들이 자동으로 전달된다. 이는 필요하다면 `this.props` 나 `this.context`에 대한 참조를 포함하도록 `state = {}`와 같은 표현식을 허용한다. 

훅스(Hooks)는 `super`나 `this`가 없다. 이는 다음 글에서 다룰 것이다. 



















### [Dan Abramov blog](https://overreacted.io/why-do-we-write-super-props/) 참고
