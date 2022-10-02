---
title: JavaScript - 데이터 불변성(Data Immutability)
author: gunny
date: 2021-10-16 04:30:00 +0900
categories: [Programming, JavaScript]
tags:
  [
    javascript,
    data immutability,
    data mutabiliry,
    immutable data,
    mutable data,
    immutable data type,
    mutable data type,
    primitive data,
    reference data,
    primitive data type,
    reference data type,
    데이터 불변성,
    데이터 가변성,
    불변 데이터,
    가변 데이터,
    불변 데이터 타입,
    가변 데이터 타입,
    원시 값,
    참조 데이터,
    원시형 데이터,
    참조형 데이터,
  ]
---

## **데이터 불변성**

데이터 불변성이란 변경할 수 없는 데이터의 성질을 말한다.

자바스크립트 변수에 할당하는 데이터의 타입엔 원시 타입과 참조 타입이 있다.

원시 타입 데이터는 불변 데이터이고 참조 타입 데이터는 가변 데이터이다.

자바스크립트는 이 두 데이터 타입을 각각 다른 방식으로 변수에 저장하고 접근한다.

원시 타입 데이터에 접근할 때는 값으로 접근하고 참조 타입 데이터에는 값이 저장된 메모리 공간에 대한 참조 값으로 접근한다.

### **원시 타입 데이터에 값으로 접근**

```javascript
let str01 = "Gunn";
let str02 = str01;

str01 = "Jun";

console.log(str01); // Jun
console.log(str02); // Gunn
```

![Memory Space : Primitive](/blog.images/211016/momory_space_primitive.png){: .shadow }
_메모리 공간에 저장된 원시 데이터_

변수 `str01`에 `Gunn`을 할당하고 변수 `str02`에 변수 `str01`의 값을 할당했다.

이때 자바스크립트는 변수 `str01`이라는 식별자가 저장된 메모리 공간을 찾고 `str01`이 가리키는 메모리 주소 5001을 변수 `str02`도 가리키게 한다.

4번 줄에서 변수 `str01`에 `Jun`을 재할당 할 때 새로운 메모리 공간에 `Jun`을 저장하고 변수 `str01`을 찾은 다음 새로운 메모리 주소인 5002를 가리키게 한다.

즉, 변수 `str01`에 새로운 값을 재할당할 때 메모리 주소 5001에 저장된 `Gunn`이라는 값이 `Jun`으로 바뀌는 것이 아닌 새로운 메모리 공간에 `Jun`이라는 값을 저장하고 변수 `str01`이 그 메모리 공간을 가리키게 하는 것이다.

이를 불변 데이터라고 하는 것이다.

### **참조 타입 데이터에 참조 값으로 접근**

```javascript
let obj01 = {
  name: "Gunn",
  height: 181,
};
let obj02 = obj01;

obj01.name = "Jun";

console.log(obj01); // {name: 'Jun', height: 181}
console.log(obj02); // {name: 'Jun', height: 181}
```

![Memory Space : Primitive](/blog.images/211016/momory_space_reference.png){: .shadow }
_메모리 공간에 저장된 참조 데이터_

변수 `obj01`에 객체 `{name: 'Gunn', height: 181}`를 할당하고 변수 `obj02`에 변수 `obj01`의 값을 할당했다.

자바스크립트는 참조 타입 데이터를 참조 값으로 접근하는데 객체의 프로퍼티가 저장된 메모리 공간에 대한 참조 값으로 값에 접근한다.

> 참조 값은 객체가 저장된 메모리 공간의 주소이다.

7번 줄에서 변수 `obj01`의 저장된 객체의 `name` 프로퍼티 값을 `Jun`으로 재할당을 했다.

이때 `Jun`을 새로운 메모리 공간에 저장하고 변수 `obj01`이라는 식별자가 저장된 메모리 공간을 찾는다.

`obj01`은 메모리 주소 3001을 가리키고 이 메모리 주소는 다시 객체의 프로퍼티가 저장된 메모리 주소 5001~500x를 가리킨다.

`name` 프로퍼티의 메모리 주소가 가리켰던 메모리 주소 3002를 `Jun`이 저장된 메모리 주소 3004로 업데이트한다.

프로퍼티 `name`이 가리키는 메모리 주소는 바꼈지만, 변수 `obj01`과 `obj02`가 가리키는 메모리 주소 3001은 변함이 없다.

이를 가변 데이터라고 하는 것이다.

변수 `obj01`과 `obj02`는 같은 참조 값을 가지므로 같은 객체를 가리키고 객체의 프로퍼티가 업데이트되면 같은 객체를 가리키므로 10~11번 줄과 같이 같은 값이 출력된다.

## **각주**

- HowToCreateApps, [Mutable and Immutable Types in JavaScript (With Examples)](https://howtocreateapps.com/mutable-and-immutable-types-in-javascript-with-examples/)
