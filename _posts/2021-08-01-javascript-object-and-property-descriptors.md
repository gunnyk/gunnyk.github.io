---
title: JavaScript - 객체 & 프로퍼티 디스크립터(Object & Property Descriptors)
author: gunny
date: 2021-08-01 19:15:00 +0900
last_modified_at: 2021-11-05 23:18:00 +0900
categories: [Programming, JavaScript]
tags:
  [
    javascript,
    types of properties,
    property descriptors,
    data property descriptor,
    accessor property descriptor,
    data property,
    accessor property,
    자바스크립트,
    프로퍼티 타입,
    디스크립터 프로퍼티,
    데이터 속성 서술자,
    접근자 속성 서술자,
    데이터 속성,
    접근자 속성,
  ]
---

## **프로퍼티 타입**

객체 프로퍼티의 각 속성은 두 가지 타입의 특별한 프로퍼티 디스크립터(property descriptors)를 갖는 데, 이는 객체 프로퍼티의 각 속성을 조작하는 데 사용한다.

디스크립터는 JS 엔진 내부에서 구현하는 것으로 자바스크립트에서 직접적으로 접근할 수는 없다.

> ECMAScript 원문에는 객체의 키(key)와 값(value)을 한 쌍으로 하는 데이터의 집합을 지칭할 땐 ‘Properties’로 쓰고 단일 프로퍼티를 지칭할 때는 ‘Attributes’를 쓴다. 한글로 번역하면 두 단어 모두 ‘속성’이지만 본문에서는 이를 구분하기 위해 ‘Properties’는 ‘프로퍼티’로 쓰고 ‘Attributes’는 ‘속성’으로 썼다.

### **데이터 프로퍼티 디스크립터**

데이터 디스크립터에는 네 가지 속성(attributes)이 있다.

| 속성명           | 데이터 타입 | 설명                                                                                                                                                                                                                                                                                                                                                       |
| ---------------- | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [[Configurable]] | Boolean     | 객체 프로퍼티를 `delete` 연산자로 제거하거나, 디스크립터의 속성을 변경하거나, 엑세서 디스크립터로 변경 가능 여부를 나타낸다.<br>속성 값이 `false` 일 경우 예외적으로 [[Value]] 값은 변경할 수 있다. [[Writable]] 값도 변경할 수 있지만 `true`에서 `false`로 한 번만 변경 가능하다.<br>[[Configurable]] 속성 값은 `false`로 설정하면 다시는 수정할 수 없다. |
| [[Enumerable]]   | Boolean     | for-in문에서 객체 프로퍼티의 열거 여부를 나타내며, `flase`이면 해당 프로퍼티를 열거하지 않는다.                                                                                                                                                                                                                                                            |
| [[Writable]]     | Boolean     | 객체 프로퍼티의 값 변경 가능 여부를 나타내며 `false`이면 값을 바꿀 수 없다.                                                                                                                                                                                                                                                                                |
| [[Vlaue]]        | Any type    | 객체 프로퍼티의 실제 값                                                                                                                                                                                                                                                                                                                                    |

```javascript
var person = {
  name: "Gunn",
  age: 30,
  getName() {
    console.log(this.name);
  },
};

Object.defineProperty(person, "name", {
  writable: false,
});

person.name = "Jun";
console.log(person.name); // Gunn
```

`Object.defineProperty` 메서드는 객체에 새로운 프로퍼티를 정의하거나 기존 프로퍼티의 디스크립터 속성 값을 변경하고 그 객체를 반환한다.

첫 번째 인수는 객체를. 두 번째 인수는 속성명을. 세 번째 인수는 디스크립터를 넘긴다.

위 코드에서 `name`의 데이터 디스크립터 속성 중 `writable` 속성 값을 `false`로 설정했다.

`person` 객체 `name`의 속성 값을 `Jun`으로 할당했지만 값은 바뀌지 않았다.

> 🧐 `Object.defineProperty` 메서드를 사용해 새로운 프로퍼티를 정의할 때 디스크립터 속성 값을 명시적으로 설정하지 않으면 초기 값은 `false`가 된다.
> `value`는 `undefined`를 초기 값으로 할당한다.

객체의 특정 프로퍼티를 선택해 디스크립터 객체의 각 속성 값을 확인하는 방법은 아래와 같다.

```javascript
Object.getOwnPropertyDescriptor(person, 'name');

// 반환된 디스크립터 객체
{
  value: 'Gunn',
  configurable: true,
  enumerable: true,
  writable: false
}
```

`Object.getOwnPropertyDescriptor` 메서드는 객체의 특정 프로퍼티의 디스크립터 객체를 반환한다.

첫 번째 인수로 객체를 넘기고 두 번째 인수로 해당 객체에서 찾을 속성명을 넘긴다.

### **엑세서 프로퍼티 디스크립터**

엑세서 디스크립터에는 네 가지 속성(attributes)이 있다.

엑세서 디스크립터는 데이터 디스크립터와 달리 `value`와 `enumerable` 속성이 없는 대신에 `get`과 `set` 함수가 있다.

`get` 함수는 객체 프로퍼티를 읽을 때 실행되고, `set` 함수는 객체 프로퍼티에 값을 할당할 때 실행된다.

| 속성명           | 데이터 타입 | 설명                                                                                                                                                                                                 |
| ---------------- | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [[Configurable]] | Boolean     | 객체 프로퍼티를 `delete` 연산자로 제거하거나, 디스크립터의 속성을 변경하거나, 데이터 디스크립터로 변경 가능 여부를 나타낸다.<br>[[Configurable]] 속성 값은 `false`로 설정하면 다시는 수정할 수 없다. |
| [[Enumerable]]   | Boolean     | for-in문에서 객체 프로퍼티의 열거 여부를 나타내며, `flase`이면 해당 프로퍼티를 열거하지 않는다.                                                                                                      |
| [[Get]]          | Object      | 프로퍼티를 읽을 때 호출할 매서드로 매개변수가 없다.                                                                                                                                                  |
| [[Set]]          | Object      | 프로퍼티 값을 변경할 때 호출할 메서드로 하나의 매개변수가 있다.                                                                                                                                      |

```javascript
let person = {
  _lastName: "Gunn",
  firstName: "Kim",

  get name() {
    return `${this._lastName} ${this.firstName}`;
  },
  set name(newValue) {
    if (this._lastName != newValue) {
      return (this._lastName = newValue);
    }
    alert("현재 이름과 다른 이름을 입력하세요.");
  },
};

person.name; // Gunn Kim
person.name = "Jun"; // Jun

person.name; // Jun Kim
```

엑세서 디스크립터의 `get` 메서드와 `set` 메서드는 `Object.defineProperty` 메서드를 사용해 정의해도 되지만, 객체를 생성할 때 함께 정의할 수도 있다.

위 코드에서 `person.name`으로 프로퍼티를 읽을 때 `get` 메서드가 호출되며, 메서드 몸체에 정의한 구문을 실행한다.

`set` 메서드는 17번 줄의 `person.name = 'Jun';`와 같이 프로퍼티에 값을 할당할 때 호출되며, 할당한 값을 매개변수로 받아 `set` 메서드를 실행한다.

> **💡 Tip**
> 단일 프로퍼티 정의 또는 단일 프로퍼티 읽기가 아닌 복합적으로 프로퍼티를 정의하거나 읽을 수 있는 메서드가 있다.
> `Object.defineProperties` 메서드는 여러 프로퍼티를 한 번에 정의할 수 있고, `Object.getOwnPropertyDescriptors` 메서드는 여러 프로퍼티를 한 번에 읽을 수 있다.

생성자 함수와 `get` 메서드와 `set` 메서드를 활용하면 인스턴스마다 고유한 값을 만들어 내는 동작을 실행할 수 있다.

```javascript
function Person(name, _birthday) {
  this.name = name;
  this._birthday = new Date(_birthday);

  Object.defineProperty(this, "age", {
    get() {
      return new Date().getFullYear() - this._birthday.getFullYear();
    },
  });
}

let person1 = new Person("Jun", "1999.1.21");
person1.age;
```

위 코드는 `Person`의 인스턴스를 생성할 때 `age` 프로퍼티를 정의하고 `get` 메서드를 지정했다.

`person1` 인스턴스의 `age` 프로퍼티를 읽으면 `get` 메서드가 호출되어 사람의 나이를 알 수 있다.

## **각주**

- Nicholas C. Zakas(2012), “JavaScript for Web Developers”
- ECMAScript® 2022 Language Specification, [The Object Type](https://tc39.es/ecma262/#sec-object-type)
- JAVASCRIPT.INFO, [프로퍼티 getter와 setter](https://ko.javascript.info/property-accessors)
- MDN Web Docs, [Object prototypes](https://developer.mozilla.org/ko/docs/Learn/JavaScript/Objects/Object_prototypes)
