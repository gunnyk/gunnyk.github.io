---
title: JavaScript - 함수의 인수(Argument of a function)
author: gunny
date: 2021-06-27 14:10:00 +0900
categories: [Programming, JavaScript]
tags:
  [
    javascript,
    functions,
    parameter,
    arguments,
    signature,
    function overloading,
    자바스크립트,
    함수,
    매개변수,
    인수,
    시그니처,
    함수 오버로딩,
  ]
---

## **함수의 인수**

```javascript
function sum() {
  let total = 0;

  for (arg in arguments) {
    total += arguments[arg];
  }
  console.log(total);
}

sum(10, 5); // 15
sum(10, " 더하기 ", 5, "는?"); // 10 더하기 5는?
```

sum함수의 매개변수를 선언하지 않았지만 sum함수를 호출할 때 여러 개의 인수를 매개변수로 넘겼다.

하지만 인터프리터는 에러로 판단하지 않으며, 프로그램은 정상적으로 동작한다.

이는 자바스크립트의 언어의 특징으로 함수 시그니처를 약타입으로 정의하기 때문에 함수에 선언된 매개변개수나 타입에 상관없이 인수로 값을 전달할 수 있다.

자바스크립트의 매개변수는 다음과 같은 특징이 있다.

1. 자바스크립트 함수의 매개변수는 내부적으로 배열로 표현되기 때문에 함수 시그니처를 만들고 매개변수의 개수나 타입을 체크하지 않는다.[^footnote1]
2. 매개변수는 함수 내부에서 `arguments`라는 객체를 통해 매개변수의 값에 접근할 수 있다.

### **arguments 객체**

```javascript
function sum(num1, num2, num3) {
  if (arguments.length == 1) {
    console.log(arguments[0] + 10);
  } else if (arguments.length == 2) {
    console.log(arguments[0] + arguments[1]);
  } else if (arguments.length == 3) {
    console.log(arguments[0] + arguments[1] + num3);
  }
}

sum(10); // 20
sum(10, 5); // 15
sum(10, 5, 15); // 30
```

인수가 1개이면 if문이 실행되고, 2개 또는 3개이면 else if문이 실행된다.

`arguments` 객체를 사용해 `num1`, `num2`, `num3` 변수의 값에 접근했다.

이런 자바스크립트 언어의 특징을 이용하면 내부적으로 매개변수의 개수나 타입을 체크하지 않더라도 오버로딩과 비슷하게 동작하는 함수를 만들 수 있다.

`arguments` 객체는 다음과 같은 특징이 있다.

1. `arguments`는 [] 표기법으로 매개변수 값에 접근할 수 있다.
2. 매개변수명 대신에 `arguments`를 사용할 수 있고, 혼용해서 사용할 수도 있다.
3. `arguments.length`로 매개변수의 개수를 알 수 있어 배열처럼 동작하지만 Array의 인스턴스는 아니다.
4. n 번째 매개변수의 값과 n 번째 `argument[n]` 값은 서로 대응 관계이기 때문에 어느 한쪽에 값이 바뀌면 다른 한쪽에서도 값이 바뀐다. 하지만 같은 메모리 공간을 사용하는 것은 아니다.
5. strict mode에서 n번째 매개변수의 값과 `argument[n]` 값은 서로 대응하지 않는다.

**- 4번 특징 예시**

```javascript
function sum(num1, num2) {
  arguments[0] = 20;

  console.log(num1, num2);
  console.log(arguments[0], num2);

  num1 = 10;
  console.log(arguments[0], num2);
}

sum(10, 20);
// 20 20
// 20 20
// 10 20
```

non-strict mode에서 `arguments[0]`값과 `num1`값은 서로 대응하기 때문에 어느 쪽에서 값이 바뀌면 다른 한쪽에도 바뀐 값이 반영된다.

하지만 strict mode에서는 두 값이 서로 대응하지 않는다.

**- 5번 특징 예시**

```javascript
"use strict"; // Using strict mode

function sum(num1, num2) {
  arguments[0] = 20;

  console.log(num1, num2);
  console.log(arguments[0], num2);
  num1 = 10;
  console.log(arguments[0], num2);
}

sum(10, 20);
// 10 20
// 20 20
// 20 20
```

---

`arguments` 객체에는 값을 재할당하는 것이 가능하다.

단, strict mode에서는 에러를 발생시킨다.

```javascript
function sum(num1, num2) {
  arguments = [20, 30];
  console.log(arguments[0], arguments[1]);
}

sum(10, 20);
// 20 30
```

## **각주**

- Nicholas C. Zakas(2012), “JavaScript for Web Developers”

[^footnote1]: 자바스크립트의 [함수 시그니처(signature)](https://developer.mozilla.org/ko/docs/Glossary/Signature/Function)는 약타입(loosely typed)으로 정의한다.
