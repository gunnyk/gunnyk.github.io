---
title: JavaScript - 참조 타입 & 함수 타입(Reference Types & Function Type)
author: gunny
date: 2021-07-24 04:45:00 +0900
last_modified_at: 2021-10-23 06:25:00 +0900
categories: [Programming, JavaScript]
tags:
  [
    javascript,
    reference types,
    function type,
    function declarations versus function expressions,
    function properties and methods,
    name property,
    length property,
    prototype property,
    custom properties,
    apply method,
    call method,
    bind method,
    자바스크립트,
    참조 타입,
    함수 타입,
    함수 선언 vs 함수 표현식,
    함수 프로퍼티와 메서드,
    name 프로퍼티,
    length 프로퍼티,
    prototype 프로퍼티,
    커스텀 프로퍼티,
    apply 메서드,
    call 메서드,
    bind 메서드,
  ]
---

## **Function 타입**

ECMAScript에서 함수는 객체이다.

모든 함수는 `Function` 타입의 인스턴스이며 객체와 마찬가지로 프로퍼티와 메서드가 있다.

함수의 이름은 단순히 함수 객체를 가리키는 포인터 역할을 할 뿐이다.

```javascript
function greetings(name) {
  console.log(`Hello! ${name}.`);
}

let greet = greetings;
greet("Gunn"); // "Hello! Gunn."

greetings = null;
greet("Gunn"); // "Hello! Gunn."
```

`greetings` 함수를 생성하고 변수 `greet`에 `greetings` 함수를 대입했다.

`greetings` 함수의 이름은 함수 정의가 저장된 메모리를 가리키는 포인터이기 때문에 변수 `greet`는 `greetings`와 같은 참조 값을 갖는다.

그러므로 `greetings`와 `greet`는 같은 함수 객체를 가리킨다.

8번 줄에서 `greetings`에 `null`을 할당하면 함수 객체를 가리키는 참조가 제거되지만 `greet`에는 영향이 없다.

### **함수 선언 vs 함수 표현식**

함수 선언문과 함수 표현식의 가장 큰 차이는 JS 엔진이 함수를 생성하는 시점에 있다.

> 자바스크립트로 작성된 프로그램이 동작할 때는 두 단계를 거쳐 실행한다. 첫 번째로 컴파일을 위한 구문 분석 단계와 코드 실행을 위한 실행 단계이다.  
> 구문 분석 단계에서 함수 선언과 함수 표현식은 다르게 분석되기 때문에 구문 분석 단계에서 일어나는 일에 관한 이해가 필요하다. [스코프와 호이스팅](https://gunnyk.github.io/posts/javascript-scope-and-hoisting/)과 [실행 컨텍스트](https://gunnyk.github.io/posts/javascript-execution-context/)를 학습하면 본문을 이해하는 데 도움이 된다.

함수 선언은 코드를 실행하기 전에 모든 실행 컨텍스트에서 접근하고 실행할 수 있지만 함수 표현식은 코드 실행이 해당 줄까지 진행하기 전에는 사용할 수 없다.

```javascript
sum(5, 10); // 15

function sum(num1, num2) {
  console.log(num1 + num2);
}
```

JS 엔진이 코드를 스캔하면 가장 먼저 변수와 함수 선언을 찾는데, 이때 함수 선언 호이스팅이란 과정을 통해 함수 선언을 실행 컨텍스트에 추가한다.

실행 컨텍스트의 생성 단계에서 이미 함수 선언 호이스팅이 발생하기 때문에 소스 코드에서 함수 실행이 함수 선언보다 먼저 작성되어 있어도 에러를 내지 않는다.

하지만 함수 표현식은 함수 선언이 아닌 변수 선언 이후 초기화 문장에 해당하기 때문에 소스 코드에서 함수 실행이 함수 표현식보다 먼저 작성될 경우 에러가 발생한다.

```javascript
sum(5, 10); // Error

let sum = function (num1, num2) {
  console.log(num1 + num2);
};
```

#### **`use strict` 모드에서 함수 선언**

```javascript
"use strict";

let age = prompt("나이를 입력하세요");

if (age < 20) {
  function greeting() {
    console.log("Hello!");
  }
} else {
  function greeting() {
    console.log("Hi!");
  }
}
greeting(); // Error
```

strict mode에서 코드 블록 내에 함수 선언문이 있는 경우 코드 블록 외부에선 함수에 접근이 불가능하다.

### **함수 프로퍼티와 메서드**

ECMAScript의 함수는 객체이므로 프로퍼티와 메서드를 가진다.

모든 함수에는 공통적으로 `name`, `length`, `prototype` 프로퍼티가 있다.

#### **함수 공통 프로퍼티**

**- `name` 프로퍼티**

`name` 프로퍼티를 사용해 함수의 이름을 알 수 있다.

```javascript
function sayName() {
  console.log(name);
}

console.log(sayName.name); // "sayName"
```

함수 표현식으로 정의된 함수에도 사용할 수 있다.

```javascript
let sayName = function () {
  console.log(name);
};

console.log(sayName.name); // "sayName"
```

익명 함수에 `name` 프로퍼티를 사용하면 빈 문자열을 반환한다.

```javascript
(function () {}.name); // ""
```

객채 메서드의 이름도 `name` 프로퍼티를 사용해 알 수 있다.

```javascript
let person = {
  name: "Gunn",
  getName: function () {
    console.log(this.name);
  },
};

console.log(person.getName.name); // "getName"
```

객체에 `constructor` 프로퍼티와 `name` 프로퍼티를 사용하면 객체의 `class`를 알 수있다.

```javascript
function person() {}

let student = new person();

console.log(student.constructor.name); // person
```

**- `length` 프로퍼티**

`length` 프로퍼티를 사용해 함수 매개변수의 개수를 알 수 있다.

```javascript
function sayName(lastName, firstName) {
  console.log(`${lastName} ${firstName}`);
}

console.log(sayName.length); // 2
```

**- 커스텀 프로퍼티**

사용자가 직접 프로퍼티를 생성해 함수에 추가할 수 있다.

```javascript
function sayName(lastName, firstName) {
  console.log(`${lastName} ${firstName}`);
  sayName.counter++;
}

sayName.counter = 0;

sayName("Gunn", "Kim"); // "Gunn Kim"

console.log(`호출 횟수: ${sayName.counter}회`); // 호출 횟수: 1회
```

**💡 프로퍼티는 변수가 아니다**

`sayName.counter = 0;`는 함수 내에 지역 변수의 선언이 아닌 프로퍼티이다.

`let counter;`와는 관계가 없다.

#### **함수 메서드**

함수에는 `apply`와 `call`, `bind` 메서드가 있다.

`apply`와 `call` 메서드는 함수 호출 시 함수 내부의 `this` 객체 값을 바꾸거나 매개변수를 전달할 수 있다.

**- `apply` 메서드**

**`apply` 문법**
: `func.apply(thisArg, [argsArray])`

```javascript
const person = {
  lastName: "Gunn",
  firstName: "Kim",
};

function getName(lastName, firstName) {
  console.log(`${this.lastName} ${this.firstName}`);
}

getName.apply(person, ["Gunn", "Kim"]); // Gunn Kim
```

`getName` 함수는 전역에 선언되었으므로 함수 내부에 `this`는 `window` 객체를 가리킨다.

> strict mode에서는 `apply`나 `call` 메서드를 사용해 명시적으로 `this` 값을 정하지 않으면 `this`는 `undefined`가 된다.

`getName` 함수에 `apply` 메서드를 호출해서 `this` 값으로 사용할 `person` 객체와 매개변수로 사용할 배열을 넘겼다.

이렇게 `apply` 메서드를 활용하면 객체마다 메서드를 정의하지 않아도 범용적으로 사용할 수 있다.

두 번째 인자에는 Array의 인스턴스 뿐만 아니라 `arguments` 객체를 넘길 수도 있다.

```javascript
function sum() {
  let total = 0;

  for (arg of arguments) {
    total += arg;
  }
  return total;
}

function callSum() {
  return sum.apply(null, arguments);
}

callSum(5, 2, 10); // 17
```

**- `call` 메서드**

**`call` 문법**
: `func.call(thisArg[, arg1[, arg2[, ...]]])`

`call` 메서드는 `apply` 메서드와 같은 동작을 하지만 매개변수를 전달하는 방식이 다르다.

`apply` 메서드는 매개변수를 배열 또는 유사 배열 객체로 전달하는 데 반해 `call` 메서드는 각각의 매개변수를 콤마(,)로 구분해 나열한다.

**- `bind` 메서드**

**`bind` 문법**
: `func.bind(thisArg[, arg1[, arg2[, ...]]])`

`bind` 메서드를 호출하면 새로운 함수 인스턴스를 생성한다.

```javascript
const person = {
  lastName: "Gunn",
  firstName: "Kim",
};

const admin = {
  name: "관리자",
};

function getName(lastName, firstName) {
  if (arguments.length == 0) {
    return this.name;
  }
  console.log(`${this.lastName} ${this.firstName}`);
}

let getAdmin = getName.bind(admin);

getName.apply(person, ["Gunn", "Kim"]); // Gunn Kim
getAdmin(); // 관리자
```

17번 줄에서 `getName` 함수에 `bind` 메서드를 호출하면서 `admin` 객체를 넘겼다.

이때 원본 함수 `getName` 함수의 복제본이 반환되면서 새로운 함수 인스턴스를 생성한다.

`getAdmin` 함수는 전역에서 호출을 하더라도 항상 `admin` 객체가 `this` 값이다.

## **각주**

- Nicholas C. Zakas(2012), “JavaScript for Web Developers”
- JAVASCRIPT.INFO, [함수 표현식](https://ko.javascript.info/function-expressions)
- JAVASCRIPT.INFO, [객체로서의 함수와 기명 함수 표현식](https://ko.javascript.info/function-object)
- MDN Web Docs, [Function.prototype.apply()](https://developer.mozilla.org/ko/docs/orphaned/Web/JavaScript/Reference/Global_Objects/Function/apply)
- MDN Web Docs, [Function.prototype.call()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/call)
- MDN Web Docs, [Function.prototype.bind()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
