---
title: JavaScript - Part 1. 프로토타입 기반 프로그래밍(Prototype Based Programming)
author: gunny
date: 2021-11-06 06:42:00 +0900
categories: [Programming, JavaScript]
tags:
  [
    javascript,
    object-oriented programming,
    prototype-based programming,
    data abstraction,
    object creation,
    the constructor pattern,
    the prototype pattern,
    prototype,
    inheritance,
    자바스크립트,
    객체 지향 프로그래밍,
    프로토타입 기반 프로그래밍,
    자료 추상화,
    객체 생성,
    생성자 패턴,
    프로토타입 패턴,
    프로토타입,
    상속,
  ]
---

## **객체 지향 프로그래밍과 프로토타입 기반 프로그래밍**

프로토타입 기반 프로그래밍은 객체 지향 프로그래밍의 한 형태로 C++이나 Java와 같은 객체 지향 프로그래밍 언어에서 사용하는 class가 없는 것이 특징이다.

> class가 없는 프로토타입 기반 프로그래밍을 클래스리스(class-less) 프로그래밍으로 부르기도 한다.

자바스크립트는 프로토타입 기반 프로그래밍 언어이다.

클래스의 상속을 자바스크립트에서는 객체의 프로토타입 체인을 사용해서 구현한다.

Part 1에선 객체 지향 프로그래밍의 개념에 관해서 간단히 설명하고 class를 사용한 객체 생성과 자바스크립트의 객체 생성과의 차이점을 알아본다.

### **객체 지향 프로그래밍**

프로그래밍에서 객체는 실세계를 반영한 실체와 같으며, 이 객체는 상태를 나타내는 속성과 동작이 있다.

객체는 다양한 데이터 타입과 이런 데이터에 연산을 수행하는 메소드들의 집합으로 이루어져 있다.

객체 지향 프로그래밍 언어는 공통적으로 필요한 정보만을 간추려 클래스로 정의하고 클래스를 자료형으로 사용해 인스턴스(객채)를 생성한다.

불필요한 정보는 걷어 내고 필요한 정보만 간추려 표현하는 것을 자료 추상화라고 하며, 자료 추상화를 통해 만든 클래스를 추상 자료형이라고 한다.

클래스 정의에는 생성할 인스턴스의 속성과 메소드들을 포함하고 있으며 클래스를 사용해 생성한 인스턴스를 객체라고 한다.

예를 들어 인간을 머리, 몸통, 팔, 다리 4가지 속성으로 간추려 추상화할 수 있다. 여기에 걷기, 뛰기, 점프하기와 같은 동작은 메소드가 된다.

> 💡 자료를 추상화한 클래스는 객체의 설계도로. 생성된 객체는 설계도대로 만든 실체라고 생각하면 된다.

![OOP : Object Creation](/blog.images/211106/OOP_object-creation.png){: .shadow }
_객체 지향 프로그래밍 : 객체 생성_

객체 지향 프로그래밍은 다뤄야 할 데이터의 종류와 양이 방대해 질수록 효율적인 처리가 가능하다.

## **객체 생성**

객체 지향 프로그래밍 언어와 프로토타입 기반 프로그래밍 언어의 객체 생성 과정과 차이점을 비교하면 다음과 같다.

> 클래스를 사용해 객체를 생성하는 예시 코드는 Java 언어의 문법을 일부 차용해서 작성했다.  
> 독자가 이해하기 쉽도록 간소화 했으므로 요점에 집중해서 보면 된다.

```java
// class 정의
class Person {
  String name;
  int age;

  Person(String name, int age) { // 생성자
    this.name = name;
    this.age = age;
  }

  void introduce() {
    System.out.println("이름: " + name + " 나이: " + age);
  }
}

Person p1, p2; // 객체 참조 변수 선언
p1 = new Person("Gunn", 30); // 객체 생성
p2 = new Person("Jun", 20);

p1.introduce(); // 이름: Gunn 나이: 30
p2.introduce(); // 이름: Jun 나이: 20
```

위 코드는 아래에 자바스크립트로 작성된 코드와 완전히 동일한 동작을 한다고 봐도 무방하다.

```javascript
// 생성자 정의
function Person(name, age) {
  this.name = name;
  this.age = age;

  this.introduce = function () {
    console.log(`이름: ${this.name} 나이: ${this.age}`);
  };
}

let p1, p2; // 변수 선언
p1 = new Person("Gunn", 30); // 객체 생성
p2 = new Person("Jun", 20);

p1.introduce(); // 이름: Gunn 나이: 30
p2.introduce(); // 이름: Jun 나이: 20
```

## **각주**

- Wikipedia, [Object-oriented programming](https://ko.wikipedia.org/wiki/객체_지향_프로그래밍)
- Wikipedia, [Prototype-based programming](https://ko.wikipedia.org/wiki/프로토타입_기반_프로그래밍)
- MDN Web Docs, [Object-oriented JavaScript for beginners](https://developer.mozilla.org/ko/docs/Learn/JavaScript/Objects/Object-oriented_JS)
