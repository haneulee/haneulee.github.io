---
layout: post
title: "Svelte"
date: 2019-11-10 23:03:00 +0900
categories: [Development, Svelte]
tags: [javascript, svelte]
---

# Svelte

## Svelte란?

svelte란 날씬한, 호리호리한이란 뜻입니다.

이름답게 적은 코드, 적은 용량 등을 자랑하는 프레임워크입니다.

공식 문서에 따르면 새로운 웹 어플리케이션 개발 방법을 제시하는 컴파일러라고도 소개되어 있습니다.

vue, react와는 다르게 가상돔을 사용하지 않습니다.

svelte는 빌드 타임에 실행되며, 효과적으로 DOM을 업데이트하는 코드로 컴파일됩니다.



## 기본 문법

svelte는 vue 처럼 .svelte라는 별도의 파일 확장자를 갖습니다.

기본문법은 html과 비슷합니다.

h1 태그 내에 {name}은 스크립트의 선언한 name 변수를 참조해서 출력합니다.

css 또한 똑같이 작성하시면 됩니다.

```
// App.svelte
<script>
const name = 'ashnamuh'
</script>

<style>
  h1 {
    color: blue;
  }
</style>

<h1>Hello {name}!</h1>

```

위 코드는 빌드 후 다음과 같이 변환됩니다.

css가 자동으로 scoped 되어서 현재 컴포넌트에만 적용이 되는군요!

```
<body cz-shortcut-listen="true">
  <h1 class="svelte-1x1ajbk">
    Hello ashnamuh!
  </h1>
</body>
{}문법은 html 속성에도 똑같이 쓸 수 있습니다.

// TheHeader.svelte
<script>
const elementId = 'the-header'
</script>

<header id={elementId}>
  It is a header
</header>
```

## 컴포넌트

컴포넌트는 .svelte파일을 바로 불러와서 사용할 수 있습니다.

위에서 만든 TheHeader.svelte를 사용하는 예시입니다.

```
// App.svelte
<script>
import TheHeader from './components/TheHeader.svelte'

const name = 'ashnamuh'
</script>

<style>
  h1 {
    color: blue;
  }
</style>

<TheHeader></TheHeader>
<h1>Hello {name}!</h1>
```
컴포넌트의 루트 엘리먼트가 꼭 하나일 필요는 없습니다.

vue는 루트 엘리먼트가 반드시 하나여야하고, react는 <React.Fragment>를 사용해서 그룹화를 해야합니다.

## props

props로 사용할 변수를 자식 컴포넌트에서 export해서 사용할 수 있습니다.
```
// Person.svelte
<script>
export let name
export let age
</script>

<p>My name is {name}. I am {age} years old.</p>
```
다음은 위 Person.svelte 컴포넌트를 사용하는 예시입니다.

객체 spread 문법으로 자식에게 데이터를 전달할 수도 있습니다.
```
<script>
import Person from './components/Person.svelte'

const ashnamuh = {
  age: 23
}

const jaemok = {
  name: 'Jaemok',
  age: 23
}

</script>

<Person name="ashnamuh" age={ashnamuh.age}></Person>
<Person {...jaemok}></Person>
```

## 이벤트

:on 디렉티브를 사용해서 돔 이벤트를 리스닝할 수 있습니다.

```
<button on:click={() => alert('Hello!')}>인라인 js</button>
스크립트 내에 함수를 연결할 수도 있습니다.

<script>
const showAlert = () => alert('Hi!')
</script>

<button on:click={showAlert}>함수 분리</button>
```
또한 |를 이용해서 이벤트 수식어를 붙일 수 있습니다.

다음은 이벤트 수식어를 설정한 이벤트 캡쳐링 예시입니다.
```
<script>
const clickGrandParent = () => console.log('clicked grand parent')
const clickParent = () => console.log('clicked parent')
const clickChild = () => console.log('clicked child')
</script>

<div class="grand-prent" on:click|capture={clickGrandParent}>
  <div class="parent" on:click|capture={clickParent}>
    <div class="child" on:click|capture={clickChild}>
      이벤트 캡쳐링
    </div>
  </div>
</div>
```
## 반응성

script 영역에 선언한 것은 반응성을 지원합니다.

이 말은 값이 바뀌면 자동으로 DOM을 갱신한다는 의미입니다.

다음은 setInterval 함수로 1초마다 count 값을 증가시키는 코드입니다.

count 값이 변함에 따라 p태그의 출력도 달라지지요.
```
// Count.svelte
<script>
let count = 0

setInterval(() => {
  count++
}, 1000)
</script>

<p>count: {count}</p>
```
## $

특정한 값이 변함에 따라 반응해서 변하는 값도 선언 가능합니다.

다음은 $ 로 count 값에 따라 변하는 doubled 변수를 선언한 예시입니다.

vue의 computed와 비슷한 맥락입니다.

svelte의 $문법이 조금 낯설게 느껴질 수도 있습니다.
```
// Count.svelte
<script>
let count = 0

$: doubled = count * 2

setInterval(() => {
  count++
}, 1000)
</script>

<p>count: {count}</p>
<p>doubled: {doubled}</p>
```

$문법으로 값 뿐만 아니라 값이 변할 때 호출되는 함수도 선언할 수 있습니다.

지금보니 vue의 watch 같은 기능도 할 수 있군요!

다음은 위 count 값이 변하면 콘솔을 출력합니다.

$: console.log(`the count is ${count}`)

## 주의할 점

배열이나 객체의 값이 변경될 땐 재할당해야만 반응성을 지원합니다.

다음처럼 객체의 속성을 직접 추가하거나,Array.prototype.push 등은 반응성이 지원되지 않습니다.
```
<script>
let arr = []
let obj = {name: 'ashnamuh'}

const handleClick = () => {
  arr = [...arr, 'item']
  // arr.push('item') Not working

  obj = {...obj, age: 23 }
  // obj.age = 23 Also not working
}
</script>

<button on:click={handleClick}>
  click me to add item
</button>
<p>{arr.join(' ')}</p>
<p>{Object.values(obj)}</p>
```
## 제어문

## if-else

순수 자바스크립트가 아닌 svelte에서 제공하는 문법으로 if문을 사용할 수 있습니다.

다음은 유저의 loggedIn 값에 따라 버튼을 다르게 보여주는 예시입니다.
```
<script>
let user = { loggedIn: false }

const toggle = () => {
  user.loggedIn = !user.loggedIn
}
</script>

{#if user.loggedIn}
  <button on:click={toggle}>
    Log out
  </button>
{/if}

{#if !user.loggedIn}
  <button on:click={toggle}>
    Log in
  </button>
{/if}
```
if-else를 쓰면 다음과 같이 표현할 수도 있습니다. 반드시 {/if}로 if문을 닫아줘야 합니다.

```
{#if user.loggedIn}
  <button on:click={toggle}>
    Log out
  </button>
{:else}
  <button on:click={toggle}>
    Log in
  </button>
{/if}
```
다음처럼 if문과 if-else문을 사용할 수도 있습니다.
```
<script>
let count = 7
</script>

{#if count > 10}
  <p>{count}는 10 초과</p>
{:else if 5 > count}
  <p>{count}는 5 미만</p>
{:else}
  <p>{count}는 5이상 10 이하</p>
{/if}
```

## each

여러개의 똑같은 엘리먼트를 반복적으로 보여줄 때 유용한 기능입니다.

vue에서는 v-for, angular에선 ngFor, react에서는 Array.prototype.map으로 사용할 수 있는 기능입니다.

each를 사용해서 사람리스트를 보여주는 예시입니다.
```
<script>
let people = [
  { id: 1, name: 'ashnamuh' },
  { id: 2, name: 'Jaemok Lee' },
  { id: 3, name: 'Ash Lee' }
]
</script>

<ul>
  {#each people as person, index}
    <li>
      index: {index}
      id: {person.id}
      name: {person.name}
    </li>
  {/each}
</ul>
위 코드는 다음 html로 변환됩니다.

<ul>
  <li>index: 0
      id: 1
      name: ashnamuh
  </li>
  <li>index: 1
      id: 2
      name: Jaemok Lee
  </li>
  <li>index: 2
      id: 3
      name: Ash Lee
  </li>
</ul>
```
## await

svelte는 비동기 작업을 처리하는 await이라는 제어문도 제공합니다.

then, catch 등 Promise와 비슷한 문법으로 비동기 작업의 결과에 따라 분기할 수 있습니다.

다음은 fetch 요청 결과에 따라 DOM 렌더링을 분기하는 예시입니다.
```
<script>
let promise = getRandomNumber()

const getRandomNumber = async () => {
  const res = await fetch(`https://svelte.dev/tutorial/random-number`)
  const text = await res.text()

  if (res.ok) {
    return text
  } else {
    throw new Error(text)
  }
}

const handleClick = () => {
  promise = getRandomNumber()
}
</script>

<button on:click={handleClick}>
  generate random number
</button>

{#await promise}
  <p>...waiting</p>
{:then number}
  <p>The number is {number}</p>
{:catch error}
  <p style="color: red">{error.message}</p>
{/await}
```
