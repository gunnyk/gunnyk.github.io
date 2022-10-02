---
title: JavaScript - 실행 컨텍스트(Execution Context)
author: gunny
date: 2021-07-17 05:30:00 +0900
categories: [Programming, JavaScript]
tags:
  [
    javascript,
    execution context and scope,
    execution context,
    scope,
    activation object,
    variable object,
    scope chain,
    this value,
    global execution context,
    execution phase,
    function execution context,
    creation phase,
    자바스크립트,
    실행 컨텍스트와 스코프,
    실행 컨텍스트,
    스코프,
    활성화 객체,
    변수 객체,
    스코프 체인,
    this 값,
    전역 실행 컨텍스트,
    함수 실행 컨텍스트,
    실행 컨텍스트의 생성 단계,
    실행 컨텍스트의 실행 단계,
  ]
---

## **실행 컨텍스트**

실행 컨텍스트는 코드의 런타임 평가를 추적하는 데 사용한다.

즉, 실행 컨텍스트는 자바스크립트 코드가 실행되는 환경을 말하며 코드 실행을 위해 필요한 정보를 담고 있다.

실행 컨텍스트는 추상화된 개념이므로 코드로 직접 접근은 불가능하다. 실행 컨텍스트에는 변수 객체, 스코프 체인, this 값이 있다.

실행 컨텍스트의 종류로는 전역 실행 컨텍스트, 함수 실행 컨텍스트가 있다.

> 실행 컨텍스트의 종류에는 Eval 실행 컨텍스트도 있다. `eval` 함수가 호출되면 자체적인 실행 컨텍스트를 갖지만 `eval` 함수의 사용을 권장하지 않으므로 서술하지 않았다.

ECMAScript를 구현한 환경에 따라 전역 실행 컨텍스트를 부르는 이름이 다른데, 웹 브라우저에서는 `window`가 전역 실행 컨텍스트이며 전역으로 정의된 모든 변수와 함수는 `window` 객체의 프로퍼티와 메서드이다.

파일이 웹 브라우저에 로드되면 기본적으로 전역 실행 컨텍스트를 생성한다.

전역 실행 컨텍스트는 오직 한 개만 생성하며, 모든 것을 관리하는 환경으로 브라우저를 종료하거나 해당 페이지를 떠날 때까지 존재한다.

> JS 엔진은 싱글 스레드이므로 코드를 실행 하는 데 하나의 전역 환경만 가능하다.

함수 실행 컨텍스트는 함수 호출 시마다 생성하며 각각의 함수 실행 컨텍스트는 함수 실행이 마치면 소멸한다. 이때 해당 컨텍스트에서 정의한 변수와 함수도 함께 소멸한다.

실행 컨텍스트를 생성하면 각 실행 컨텍스트는 순차적으로 실행 컨텍스트 스택에 쌓이는데, 이 정보로 실행 컨텍스트를 추적한다.

실행 컨텍스트 스택에 사용하는 자료구조는 LIFO(last in first out)이다.

```javascript
var a = "one";

function fnA() {
  var b = "two";

  function fnB() {
    var c = "three";
  }
  fnB();
}

fnA();
```

![Execution context stack](/blog.images/210717/execution_context_stack.png){: .shadow }
_실행 컨텍스트 스택에서 실행 컨텍스트의 생성과 소멸 과정_

1. 가장 먼저 전역 실행 컨텍스트를 생성하고 실행 컨텍스트 스택에 추가한다. 전역 실행 컨텍스트에서 코드가 실행된다.
2. 12번 줄에서 `fnA` 함수를 호출하면 `fnA` 함수 실행 컨텍스트를 생성하고 실행 컨텍스트 스택에 추가한다.
3. `fnA` 함수 내에서 `fnB` 함수가 호출되면 컨트롤이 `fnA`에서 `fnB`로 넘어가는데, 이때 `fnB` 함수 실행 컨텍스트를 생성하고 실행 컨텍스트 스택에 추가한다.
4. `fnB` 함수 실행을 마치면 `fnB` 함수 실행 컨텍스트를 실행 컨텍스트 스택에서 제거하고 컨트롤을 이전 컨텍스트인 `fnA` 함수로 반환한다.
5. 마찬가지로 `fnA` 함수의 실행이 마치면 `fnA` 함수 실행 컨텍스트를 실행 컨텍스트 스택에서 제거하고 컨트롤은 전역 실행 컨텍스트로 반환한다.
6. 모든 코드의 실행을 완료하면 실행 컨텍스트 스택에서 전역 실행 컨텍스트를 제거한다.

실행 컨텍스트는 생성 단계와 실행 단계로 나뉘어 수행한다.

## **실행 컨텍스트 - 생성 단계와 실행 단계**

생성 단계에서 JS 엔진은 함수를 호출하지만 실행하지는 않는다.

이 단계에서 JS 엔진은 컴파일을 위해 코드를 스캔하고 다음 작업을 수행하며 실행 컨텍스트를 생성한다.

✓ 활성화 객체/변수 객체 생성
: 함수의 `arguments`, 변수 및 함수 선언에 관한 모든 정보를 포함한다.

✓ 스코프 체인 생성
: 활성화 객체/변수 객체를 생성한 후 스코프 체인을 초기화한다. 스코프 체인은 현재 함수의 변수 객체를 포함한 모든 상위 함수의 변수 객체와 전역 실행 컨텍스트의 변수 객체를 갖는다.

✓ `this` 값 결정
: 스코프 체인을 생성한 후 `this` 값을 초기화한다.

**🤖 예제 코드**

```javascript
x = 10;
var a = "one";

y = function () {
  var z = 20;
};

function fnA(param1) {
  var b = 2;
  a = 1;

  function fnB() {
    var c = "three";
  }
  fnB();
}

fnA(70);
```

### **생성 단계 - 전역 실행 컨텍스트**

위 예제 코드는 생성 단계에서 전역 실행 컨텍스트를 다음과 같이 생성한다.

```javascript
globalExecutionContextObj = {
  variableObj: {
    a: undefined,
    fnA: Pointer to the function definition
  },
  scopeChain: [GEC variable object],
  this: window
}
```

1. 먼저 JS 엔진은 전역에 선언된 변수와 함수를 찾는다. 키워드가 없는 것은 함수나 변수 선언으로 판단하지 않기 때문에 변수 `x`는 변수 객체에 추가하지 않는다.
2. 변수 `a`는 전역에 선언된 변수이므로 전역 실행 컨텍스트에서 변수 객체의 프로퍼티로 설정하고 `undefined` 값으로 초기화한다.
3. 4번 줄에 변수 `y` 역시 키워드가 없기 때문에 변수 선언으로 판단하지 않고 무시힌다.
4. JS 엔진이 8번 줄로 이동하면 JS 엔진은 `fnA` 함수 정의를 힙 메모리에 저장하고 이곳을 가리키는 포인터 값을 설정한다.
5. 스코프 체인은 전역 실행 컨텍스트의 변수 객체 하나만 존재한다.
6. 전역 실행 컨텍스트의 `this` 값은 `window` 객체로 결정한다.

### **실행 단계 - 전역 실행 컨텍스트**

생성 단계를 마치면 JS 엔진이 코드를 다시 한번 스캔하며 변수 객체를 변수의 값으로 업데이트하고 코드를 실행한다.

실행 단계에서 전역 실행 컨텍스트 객체는 다음과 같다.

```javascript
globalExecutionContextObj = {
  variableObj: {
    a: 'one',
    fnA: Pointer to the function definition,
    x: 10,
    y: Pointer to the function definition
  },
  scopeChain: [GEC variable object],
  this: window
}
```

1. 먼저 JS 엔진은 변수 `x`가 변수 객체에 존재하지 않는 것을 확인하고 전역 컨텍스트 객체의 프로퍼티로 추가한 뒤 `10`으로 초기화한다.
2. 변수 `a`는 변수 객체에 존재하므로 값을 `one`로 업데이트한다.
3. 변수 `y`는 변수 객체에 존재하지 않으므로 전역 컨텍스트 객체의 프로퍼티로 추가한 뒤 `y`에 대입된 함수 정의를 힙 메모리에 저장하고 이곳을 가리키는 포인터 값을 설정한다.

### **생성 단계 - 함수 실행 컨텍스트**

위 예제 코드는 생성 단계에서 함수 실행 컨텍스트를 다음과 같이 생성한다.

```javascript
fnAExecutionContextObj = {
  activationObj: {
    argumentsObj: {
      0: param1,
      length: 1
    },
    pram1: 70,
    b: undefined,
    fnB: Pointer to the function definition
  },
  scopeChain: [fnA variable object, GEC variable object],
  this: window
}
```

1. `fnA` 함수를 호출하면 JS 엔진은 다시 컴파일 단계에 들어가며 함수 실행 컨텍스트를 생성하기 위해 함수를 스캔한다.
2. 컨텍스트가 함수인 경우 활성화 객체를 변수 객체로 사용한다. 활성화 객체는 항상 `argumentsObj` 객체로 시작한다. 이 객체는 전역 실행 컨텍스트의 변수 객체에는 존재하지 않는다.
3. `fnA` 함수는 `param1`을 매개변수로 가지므로 `fnAExecutionContextObj` 객체의 `argumentsObj` 객체에 `param1`을 추가하고 활성화 객체의 프로퍼티로 `param1`을 만든다.
4. 변수 `b`가 현재 함수의 활성화 객체에 존재하는 프로퍼티인지 확인하고 찾는다. 변수 `b`는 현재 함수에 존재하지 않으므로 활성화 객체의 프로퍼티로 `b`를 추가하고 `undefined` 값으로 초기화한다.
5. 변수 `a`는 선언이 아니므로 다음 줄을 진행한다.
6. JS 엔진은 `fnB` 함수 정의를 힙 메모리에 저장하고 이곳을 가리키는 포인터 값을 설정한다.

### **실행 단계 - 함수 실행 컨텍스트**

생성 단계를 마치면 JS 엔진이 함수의 코드를 다시 한번 스캔하며 변수 객체를 변수의 값으로 업데이트하고 코드를 실행한다.

실행 단계에서 함수 실행 컨텍스트 객체는 다음과 같다.

```javascript
fnAExecutionContextObj = {
  activationObj: {
    argumentsObj: {
      0: param1,
      length: 1
    },
    pram1: 70,
    b: 2,
    fnB: Pointer to the function definition
  },
  scopeChain: [fnA variable object, GEC variable object],
  this: window
}
```

1. 변수 `b`는 `fnA` 활성화 객체에 존재하므로 값을 `2`로 업데이트한다.
2. JS 엔진은 변수 `a`가 `fnA` 함수 실행 컨텍스트에서 활성화 객체의 프로퍼티가 아님을 확인하고 스코프 체인을 통해 전역 실행 컨텍스트의 변수 객체에 접근한다.
3. 전역 실행 컨텍스트의 변수 객체에 `a`라는 이름의 프로퍼티가 있는지 확인하고, 존재 한다면 `a`의 값을 `1`로 업데이트한다. 만약 존재하지 않는다면 JS 엔진은 전역 실행 컨텍스트의 변수 객체에 `a`라는 이름으로 프로퍼티를 만들고 초기화한다.

이후 `fnB` 함수를 호출하면 위와 같은 단계를 거쳐 `fnB` 함수 실행 컨텍스트를 생성하고 실행 컨텍스트 스택에 추가한다.

## **각주**

- Nicholas C. Zakas(2012), “JavaScript for Web Developers”
- ECMAScript® 2022 Language Specification, [Execution Contexts](https://tc39.es/ecma262/#sec-execution-contexts)
- javaTpoint, [JavaScript Execution Context](https://www.javatpoint.com/javascript-execution-context)
- ui.dev, [The Ultimate Guide to Hoisting, Scopes, and Closures in JavaScript](https://ui.dev/ultimate-guide-to-execution-contexts-hoisting-scopes-and-closures-in-javascript/)
