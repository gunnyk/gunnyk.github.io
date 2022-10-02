---
title: JavaScript - 스코프와 호이스팅(Scopoe and Hoisting)
author: gunny
date: 2021-10-23 05:51:00 +0900
categories: [Programming, JavaScript]
tags:
  [
    javascript,
    scope,
    global scope,
    function scope,
    block scope,
    scope chain,
    hoisting,
    스코프,
    전역 스코프,
    함수 스코프,
    블록 스코프,
    스코프 체인,
    호이스팅,
  ]
---

## **스코프**

스코프는 식별자, 즉 변수에 접근할 수 있는 유효 범위를 말한다.

ES6 이전까지 JavaScript엔 전역 스코프와 함수 스코프만 존재 했다. 블록 스코프 개념은 ES6부터 등장한다.

변수의 선언 위치는 코드에서 변수의 접근 가능 여부에 영향을 미친다.

### **ES6 이전까지의 스코프**

```javascript
var globalVar = "Global";

function fnA() {
  var localVarA = "LocalA";
  console.log(globalVar, localVarA); // Global, LocalA

  function fnB() {
    var localVarB = "LocalB";
    var localVarA = "Redeclaration of LocalVarA";
    console.log(globalVar, localVarA, localVarB); // Global, Redeclaration of LocalVarA, LocalB
  }

  fnB();
  console.log(globalVar, localVarA); // Global, LocalA
}
fnA();
```

변수 `globalVar`는 전역 스코프에 포함되며 웹 앱이 종료될 때까지 유지된다.

> 웹 브라우저 환경에선 `window` 객체를 사용해 전역 스코프를 생성하므로 전역에 선언한 모든 변수와 함수는 `window` 객체의 프로퍼티와 메서드가 된다.
> `window.globalVar`, `window.fnA()`로 확인해 볼 수 있다.

함수 `fnA` 내에 선언한 변수 `localVarA`와 함수 `fnB`는 `fnA` 함수 스코프에 포함되며 `fnA` 함수의 실행이 마치면 제거된다.

함수 `fnA` 내에서는 전역 스코프에 정의된 변수나 함수에도 접근이 가능한데 이는 자바스크립트가 생성한 스코프 체인을 따라 상위 스코프로 접근하기 때문이다.

> 자바스크립트에선 스코프를 실행 컨텍스트로 설명할 수 있다. 실행 컨텍스트는 자바스크립트 코드의 실행을 위해 필요한 정보를 담고 있으며, 그 정보에는 변수 객체, 스코프 체인, this 값이 있다. [실행 컨텍스트](https://gunnyk.github.io/posts/javascript-execution-context/)를 함께 학습하면 본문을 이해하는데 도움이 된다.

함수 `fnB` 내에서는 변수 `localVarB`와 `localVarA`를 선언하고 초기 값을 할당했다.

`var`를 사용해 선언한 변수는 가장 가까운 스코프에 포함되는데 `fnA` 함수 내에 `localVarA`가 선언되어 있더라도 `fnB` 함수 내에서 같은 이름의 식별자로 재선언이 이루어 졌기 때문에 `fnB` 함수 내에서 `localVarA` 변수에 접근하면 `Redeclaration of LocalVarA` 값을 출력한다.

함수 `fnB`도 `fnA`와 마찬가지로 스코프 체인을 따라 상위 스코프로 접근이 가능하다.

만약 함수 `fnB` 내에서 변수 `localVarA`의 재선언이 이루어지지 않았다면 스코프 체인을 따라 상위 스코프에서 해당 변수를 찾게되고 `fnA` 함수 스코프에 존재하는 변수 `localVarA`를 찾아 `LocalA` 값을 출력했을 것이다.

`fnA` 함수에서도 변수 `localVarA`를 조회할 수 없다면 전역 스코프까지 조회하게 되고 해당 변수를 찾을 수 없다면 에러가 발생한다.

현재 스코프에선 상위 스코프에 접근이 가능하지만 상위 스코프에선 하위 스코프에 접근이 불가능하다.

예를 들어 `fnA` 함수 스코프에선 `fnB` 함수 스코프에 접근할 수 없기 때문에 `fnA` 함수 스코프에서 변수 `localVarB`의 값을 출력하려고 하면 에러가 발생한다.

위 코드의 스코프를 다이어그램으로 나타내면 다음과 같다.

![Global Scope and Function Scope](/blog.images/211023/scope.png){: .shadow }
_전역 스코프와 함수 스코프_

### **ES6 이후 스코프 : 호이스팅**

#### **자바스크립트의 블록 스코프**

C나 C++, JAVA와 같은 언어에선 중괄호로 감싼 코드 블록마다 스코프가 생성되므로 조건에 따라 변수를 선언해서 사용할 수 있다.

예를 들어 `if`, `for`, `while`문과 같은 조건식은 중괄호로 코드 블록을 만들어 특정 조건에 따라 명령을 실행한다.

블록 스코프 내에 선언한 변수는 해당 스코프 내에서만 존재하며 상위 스코프에서 접근할 수 없다.

하지만 ES6 이전까지의 자바스크립트에선 블록 스코프의 개념이 없었기 때문에 개발자들에게 혼란이 되었다.

```javascript
for (var i = 0; i < 5; i++) {
  console.log(i);
}

console.log(i); // 5
```

변수 `i`가 5보다 작으면 `for`문 내에 명령문을 반복해서 실행한다.

변수 `i`가 5가 되면 조건이 성립하지 않으므로 조건문을 마친다.

`for`문의 중괄호 코드 블록이 블록 스코프를 생성했다면 변수 `i`는 `for`문 실행이 마치고 제거되어야 하지만 `for`문의 상위 스코프에서 해당 변수에 접근해 값을 출력했다.

#### **호이스팅**

호이스팅이란 자바스크립트 엔진이 변수나 함수 선언과 같은 식별자를 만나면 현재 스코프에서 해당 선언을 최상단으로 끌어 올리는 것과 같이 동작하는 것을 말한다.

자바스크립트로 작성된 프로그램이 동작할 때는 두 단계를 거쳐 실행한다. 컴파일을 위해 모든 코드를 읽는 구문 분석 단계와 구문 분석된 코드를 실행하는 실행 단계이다.

구문 분석 단계에서 호이스팅이 발생하며 변수 선언에 대한 메모리 할당, 스코프 생성과 같은 일도 이 단계에서 이루어진다.

호이스팅 전과 후를 코드로 표현하면 다음과 같다.

**- 호이스팅 전**

```javascript
function fnA() {
  var localA = "A";
  var localB = "B";

  if (true) {
    var localC = "C";
  }

  function fnB() {
    console.log(localA, localB, localC);
  }

  fnB();
}
```

**- 호이스팅 후**

```javascript
function fnA() {
  var localA;
  var localB;
  var localC;
  function fnB() {
    console.log(localA, localB, localC); // A, B, C
  }

  localA = "A";
  localB = "B";

  if (true) {
    localC = "C";
  }

  fnB();
}
```

> 호이스팅에 관한 이해를 돕기 위한 코드 일뿐 코드가 이동하는 것은 아니다.

호이스팅 후 코드를 보면 `fnA` 함수 스코프 내에서 선언한 모든 변수와 함수가 현재 스코프의 최상단으로 끌어 올려진 것을 알 수 있다.

변수 초기화와 `fnB` 함수 실행은 변수와 함수 선언보다 아래쪽에 위치하며 실행 단계에서 동작한다.

### **ES6 이후 스코프 : 블록 스코프**

위에 “호이스팅 후” 코드에서 `if`문 내에 선언한 변수 `localC`가 `fnA` 함수 스코프에 포함된 것을 알 수 있다.

이는 자바스크립트가 블록 스코프를 생성하지 않기 때문이다.

하지만 ES6에서 변수를 선언하는 키워드인 `let`과 `const`가 추가됨에 따라 블록 스코프를 생성할 수 있게 되었다.

```javascript
function fnA() {
  var localA = "A";

  if (true) {
    let localB = "B";
  }

  console.log(localA, localB); // ReferenceError!
}
```

위 코드는 “호이스팅 후” 코드와 달리 `fnA` 함수 스코프 내에서 선언한 변수 `localB`의 값을 출력하려할 때 `ReferenceError` 에러가 발생한다.

변수 `localB`는 `let` 키워드를 사용했기 때문에 블록 스코프에 포함되며 상위 스코프인 `fnA` 함수 스코프에서 접근할 수 없다.

## **각주**

- Nicholas C. Zakas(2012), “JavaScript for Web Developers”
- JavaScript Is Sexy, [Understanding ES2015 In Depth, Part 1: Block Scope with let and const](http://javascriptissexy.com/understanding-es2015-in-depth-part-1-block-scope-with-let-and-const/)
