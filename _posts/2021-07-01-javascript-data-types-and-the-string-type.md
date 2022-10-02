---
title: JavaScript - 데이터 타입 & 문자열 타입(Data Types & The String Type)
author: gunny
date: 2021-07-01 22:55:00 +0900
categories: [Programming, JavaScript]
tags:
  [
    javascript,
    data types,
    the string type,
    자바스크립트,
    데이터 타입,
    문자열 타입,
    문자열 관련 메서드,
  ]
---

## **문자열 타입**

자바스크립트는 타 언어와 달리 글자 하나만 저장하는 데이터 타입이 없다.

모든 글자는 문자열 형태로 저장되며, 인코딩 방식에 상관없이 UTF-16 형식을 따른다.[^footnote1]

```javascript
"use strict";

let str = "Hello";

str[0] = "J"; // TypeError: Attempted to assign to readonly property.
console.log(str[0]); // 'H'
```

자바스크립트의 문자열은 immutable이기 때문에 글자를 바꾸려고 하면 읽기 전용 속성에 할당을 시도했다며 TypeError가 발생한다.

문자열을 바꾸려면 새로운 문자열을 생성한&nbsp;후 변수에 할당하면 된다.

### **문자열 관련 메서드**

#### **특정 위치의 글자 찾기**

**- charAt**

문법
: `str.charAt(index)`

```javascript
let str = "Hello";

str[0]; // 'H'
str.charAt(0); // 'H'

str[9]; // undefined
str.charAt(9); // ''
```

두 방법 모두 특정 위치의 글자에 접근할 수 있지만 접근하려는 위치에 글자가 없으면 `str[]`는 `undefined`를 반환하고, `charAt`은 빈&nbsp;문자열을 반환한다.

#### **부분 문자열 찾기**

**- indexOf**

문법
: `str.indexOf(searchValue[, fromIndex])`

```javascript
let str = "Hello Human!";

str.indexOf("H"); // 0
str.indexOf("H", 1); // 6
str.indexOf("man"); // 8
```

첫 번째 인수로 문자열을 입력하면 문자열의 첫&nbsp;글자가 어느 위치부터 시작하는지 알 수 있다.

두 번째 인수로 정수값을 넘기면 문자열을 찾기 시작하는 위치를 지정할 수 있다.

글자의 위치를 찾을 수 없으면 `-1`을 반환한다.

**- includes**

문법
: `str.includes(searchString[, position])`

```javascript
let str = "Hello Human!";

str.includes("Hello"); // true
str.includes("girl"); // false

str.includes("l", 4); // false
```

단순히 문자열의 포함 여부만 확인할 수 있다.

두 번째 인수로 정수값을 넘기면 문자열을 찾기 시작하는 위치를 지정할 수 있다.

**- startsWith & endsWith**

문법
: `str.startsWith(searchString[, position])`
: `str.endsWith(searchString[, length])`

```javascript
let str = "Hello Human!";

str.startsWith("Hello"); // true
str.startsWith("Human"); // false

str.startsWith("Human", 6); // true
```

첫 번째 인수로 넘긴 문자열로 시작하는지의 여부를 확인할 수 있다.

두 번째 인수로 정수값을 넘기면 그 위치부터 확인한다.

`endsWith` 메서드는 첫 번째 인수로 넘긴 문자열로 끝나는지의 여부를 확인할 수 있다.

## **각주**

- Nicholas C. Zakas(2012), “JavaScript for Web Developers”

[^footnote1]: 위키백과 : [UTF-16](https://ko.wikipedia.org/wiki/UTF-16)
