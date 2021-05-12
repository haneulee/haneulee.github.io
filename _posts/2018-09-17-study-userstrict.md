---
layout: post
title: "Javascript use strict"
date: 2018-09-17 17:03:00 +0900
categories: [Development, Javscript]
tags: [javascript, use, strict]
---

자바스크립트 use strict 모드
===========

: strict 모드는 ES5(ECMA Script 5)에 추가된 키워드
strict 모드는 자바스크립트가 묵인했던 에러들의 에러 메시지를 발생시킨다.

- 선언하지 않고 전역 변수를 만들 수 없다.
```{.javascript}
"use strict"
temp = 4;
```

- writable이 false로, 읽기 전용 객체에 쓰는 것이 불가능. (read only 객체 수정 불가능)
```{.javascript}
"use strict";
var testObj = Object.defineProperties({}, {
    prop1: {
        value: 10,
        writable: false // by default
    },
    prop2: {
        get: function () {
        }
    }
});
testObj.prop1 = 20;
testObj.prop2 = 30;
```

- get으로 선언된 객체는 수정할 수 없다. (getter-only property 수정 불가능)
```{.javascript}
"use strict";
var obj2 = { get x() { return 17; } };
obj2.x = 5; // throws a TypeError
```

- extensible 특성이 false로 설정된 객체에 속성을 확장 할 수 없다. (확장 불가 객체 확장 불가능)
```{.javascript}
"use strict";
var testObj = new Object();
Object.preventExtensions(testObj);
testObj.name = "Bob";
```

- delete를 호출 할 수 없다.
```{.javascript}
"use strict";
var temp = 4;
delete temp;
```

- 리터럴 객체는 동일한 이름의 property를 가질 수 없다.(ES6는 가능)
```{.javascript}
"use strict";
var o = { p: 1, p: 2 }; // !!! syntax error
```
- 함수의 동일한 매개 변수 이름을 선언하는 것이 불가능
```{.javascript}
"use strict";
function testFunc(param1, param1) {
    return 1;
};
```
- 8진수 숫자 리터럴 및 이스케이프 문자를 사용 할 수 없다.
```{.javascript}
"use strict";
var testoctal = 010;
var testescape = \010;
```

- primitive values의 속성 설정이 불가능.
```{.javascript}
function() {
"use strict";
false.true = "";         // TypeError
(14).sailing = "home";     // TypeError
"with".you = "far away"; // TypeError
}
```
- with를 사용할 수 없다.
- eval 함수는 주변 스코프에 새로운 변수를 추가하지 않는다.
- eval을 변수 또는 함수, 매개 변수의 이름으로 사용할 수 없다.
```{.javascript}
var x = 17;
var evalX = eval("'use strict'; var x = 42; x");
console.log(x);
console.log(evalX);
```
- arguments를 변수 또는 함수, 매개 변수의 이름으로 사용할 수 없다.
- 인자값을 수정함으로 arguments의 값이 수정되지 않는다.
```{.javascript}
function f(a){
    "use strict";
    a = 42;
    return [a, arguments[0]];
}
var pair = f(17);
console.log(pair[0]);
console.log(pair[1]);
```
- callee 지원 안 함.
- 함수 선언은 스크립트나 함수의 최상위에서 해야함.
- 예약된 키워드의 이름으로 변수 또한 함수를 생성할 수 없다.
- callee, caller를 통해 stack 검색이 불가능.
```{.javascript}
function restricted() {
    "use strict";
    restricted.caller;    // throws a TypeError
    restricted.arguments; // throws a TypeError
}
function privilegedInvoker() {
    return restricted();
}
privilegedInvoker();
```
- this의 값이 null 또는 undefined인 경우 전역 객체로 변환하지 않는다. (undefined가 그대로 보임)
```{.javascript}
"use strict";
function fun() { return this; }
console.log(fun());
console.log(fun.call(2));
console.log(fun.apply(null));
console.log(fun.call(undefined));
console.log(fun.bind(true)());
```
