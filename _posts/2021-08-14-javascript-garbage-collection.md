---
title: JavaScript - 가비지 컬렉션(Garbage Collection)
author: gunny
date: 2021-08-14 23:55:00 +0900
categories: [Programming, JavaScript]
tags:
  [
    javascript,
    garbage collection,
    garbage collector,
    garbage collection algorithm,
    reference counting,
    circular reference,
    mark-and-sweep,
    managing memory,
    memory leaks,
    자바스크립트,
    가비지 컬렉션,
    가비지 컬렉터,
    가비지 컬렉션 알고리즘,
    참조 카운팅,
    순환 참조,
    표시하고 지우기,
    메모리 관리,
    메모리 누수,
  ]
---

## **가비지 컬렉션**

자바스크립트는 실행 환경에서 코드를 실행하는 중에 메모리를 관리하는데, 개발자가 직접 메모리를 관리하지 않아도 자바스크립트는 필요한 메모리를 할당하고 더 이상 사용하지 않는 메모리는 회수한다.

예를 들어, 함수 내에 선언된 지역 변수는 함수를 실행할 때 생성된다.

이 지역 변수는 함수의 실행이 마치면 더 이상 사용하지 않게 되고 가비지 컬렉터가 이를 추적해 해당 변수가 차지하고 있던 메모리를 회수한다.

```javascript
var obj = {
  key: "value",
};
```

변수 `obj`는 `{key: value}` 객체가 저장된 메모리를 가리키는 참조 값을 갖는다.

이 객체가 차지하고 있는 메모리 공간을 변수 `obj`가 참조하고 있는 한 가비지 컬렉터는 메모리를 회수하지 않는다.

하지만 다음과 같은 경우 가비지 컬렉터는 이 객체를 더 이상 사용하지 않는 객체로 판단하고 메모리를 회수한다.

```javascript
obj = null;
```

변수 `obj`에 `null`을 할당했으므로 변수 `obj`는 객체가 저장된 메모리를 가리키는 참조 값을 잃게 된다.

이때 어디에서도 이 객체를 참조하고 있지 않다면 가비지 컬렉터는 메모리에 저장된 객체를 더 이상 사용하지 않는 값으로 판단하고 메모리를 회수한다.

가비지 컬렉터가 가비지 컬렉션을 수행하는 방법에는 다양한 가비지 컬렉션 알고리즘이 존재한다.

널리 쓰이는 기본적인 가비지 컬렉션 알고리즘엔 Mark-and-Sweep이 있으며, 그 외에 Reference Counting 등이 있다.

가비지 컬렉션 알고리즘은 브라우저마다 다르다.

## **가비지 컬렉션 알고리즘**

### **Reference Counting**

참조 카운팅 방식은 메모리에 저장된 값이 얼마나 참조되었는지를 추적한다.

변수를 선언하고 참조 값이 할당되면 참조 카운트는 1이 된다.

다른 변수가 같은 값을 참조하고 있다면 참조 카운트는 1 증가한다. 만약 이 변수에 다른 값을 할당하거나 참조 값을 제거하면 원래 참조하고 있던 값의 참조 카운트가 1 감소한다.

값의 참조 카운트가 0이 되면 해당 값에 접근할 방법이 없으므로 가비지 컬렉터는 이 메모리를 회수한다.

```javascript
function outerFn() {
  var objA = {}; // objA RC 1

  function innerFn() {
    var objB = {}; // objB RC 1
    objA.ref = objB; // objB RC 2
  }
  innerFn();
  // objB RC 1
}

outerFn();
// objA RC 0, objB RC 0
```

`outerFn` 함수 안에 `objA` 변수를 선언하고 빈 객체를 할당해 초기화했다. 이때 변수 `objA`의 참조 카운트는 1이 된다.

`innerFn` 함수 안에는 `objB` 변수를 선언하고 빈 객체를 할당해 초기화했다. 그리고 `objA`의 프로퍼티에 `objB`를 할당해 `objA.ref`가 `objB`를 참조하도록 했다. `objB`와 `objA.ref`가 같은 객체를 참조하고 있으므로 `objB`의 참조 카운트는 2가 된다.

`innerFn` 함수의 실행이 마치면 변수 `objB`는 스코프를 벗어나게 되고, 더이상 사용할 수 없으므로 참조 카운트는 1 감소한다.

`outerFn` 함수의 실행이 마치면 변수 `objA`도 스코프를 벗어나게 되고, 더이상 사용할 수 없으므로 참조 카운트는 0이 된다.

변수 `objA`의 참조 카운트가 0이 되면서 `objA.ref`로 참조하던 변수 `objB`를 더이상 참조할 수 없게되고 `objB`의 참조 카운트도 0이 된다.

가비지 컬렉터는 참조 카운트가 0이 된 두 객체를 더이상 사용하지 않는 것으로 판단하고 메모리를 회수하게 된다.

#### **순환 참조**

참조 카운팅 방식에서 순환 참조는 메모리 누수의 원인이 된다.

```javascript
function circularRef() {
  var objA = {}; // objA RC 1
  var objB = {}; // objB RC 1

  objA.ref = objB; // objB RC 2
  objB.ref = objA; // objA RC 2
}
circularRef();
// objA RC 1, objB RC 1
```

`circularRef` 함수를 실행하면 변수 `objA`와 `objB`가 선언되고 참조 값이 할당되면서 각각 참조 카운트가 1 증가한다.

그리고 `objA`와 `objB`는 프로퍼티를 통해 서로를 참조하고 있다.

이때 참조 카운트가 1 증가하면서 각각의 참조 카운트는 2가 된다.

함수 실행을 마치면 두 변수 모두 스코프를 벗어났음에도 참조 카운트가 0이 되지 않기 때문에 가비지 컬렉터는 메모리를 회수하지 않는다.

만약 이 함수가 여러번 호출되면 회수하지 못하고 낭비하는 메모리가 늘어난다.

### **Mark-and-Sweep**

mark-and-sweep 방식은 roots라는 최상위 객체에서 시작해서 도달할 수 없는 객체에 대해 가비지 컬렉션을 수행한다.

```javascript
var globalA = 'A';
var globalB = 'B';
var globalC = {
  refGlobalA: globalA;
}

function globalFn() {
  var localA = 'a';
  var localB = 'b';
  globalC.refLocalA = localA;
}
globalFn();
```

가비지 컬렉터가 동작하면 먼저 메모리에 저장된 모든 변수에 표시를 남긴다.

그다음 roots에 속하는 `globalA`, `globalB`, `globalC`, `globalFn`부터 도달 가능한 모든 객체를 탐색하며 표시를 지운다.

위 코드에서 `globalFn` 함수의 실행이 마치면 지역 변수 `localA`는 `globalC.refLocalA`를 통해 roots로부터 도달할 수 있지만 변수 `localB`에는 더이상 도달할 수 없게되고 표시가 남아있게 된다.

가비지 컬렉터는 변수 `localB`를 더이상 사용하지 않는 값으로 판단하고 메모리를 회수한다.

이런 방식은 참조 카운팅 방식의 순환 참조 문제를 해결한다.

## **메모리 관리**

웹 브라우저 환경에서 사용할 수 있는 메모리는 일반적인 데스크톱 애플리케이션의 가용 메모리에 비해 매우 적은 편이다.

웹 브라우저가 적은 양의 가용 메모리를 할당받는 주된 이유는 웹 페이지에서 실행하는 자바스크립트가 시스템 메모리를 전부 사용해서 운영체제가 다운되는 일을 방지하기 위함이다.

이러한 메모리의 제한은 변수 할당뿐만 아니라 호출 스택, 스레드에서 실행할 수 있는 문장 수에도 영향을 미친다.

이런 환경에서 웹 페이지의 성능을 최대한으로 향상시킬 수 있는 방법은 코드 실행에 필요한 데이터만 유지하는 것이다.

더 이상 사용하지 않는 데이터에 `null`을 할당해서 참조를 제거하는 것이 좋다.

지역 변수 같은 경우 컨텍스트를 벗어나면 자동으로 참조가 제거되기 때문에 명시적으로 참조를 제거해줄 필요가 없다.

변수의 참조를 제거한다고 해서 메모리가 자동으로 반환되는 것은 아니다.

참조 제거의 요점은 값의 컨텍스트를 없애서 가비지 컬렉터가 이를 추적해 메모리를 회수하도록 하는 것이다.

## **각주**

- Nicholas C. Zakas(2012), “JavaScript for Web Developers”
- JAVASCRIPT.INFO, [가비지 컬렉션](https://ko.javascript.info/garbage-collection)
- MDN Web Docs, [자바스크립트의 메모리관리](https://developer.mozilla.org/ko/docs/Web/JavaScript/Memory_Management)
