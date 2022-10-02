---
title: JavaScript - DOM(The Document Object Model)
author: gunny
date: 2021-08-07 02:50:00 +0900
last_modified_at: 2021-08-21 18:18:00 +0900
categories: [Programming, JavaScript]
tags:
  [
    javascript,
    the document object model,
    dom level 1,
    hierarchy of nodes,
    the node type,
    the document type,
    the element type,
    the text type,
    the attr type,
    node-list object,
    자바스크립트,
    문서 객체 모델,
    dom 레벨 1,
    노드의 계층 구조,
    node 타입,
    document 타입,
    element 타입,
    text 타입,
    attr 타입,
    node-list 객체,
  ]
---

## **노드 계층 구조**

웹 페이지는 일종의 문서이며, 웹브라우저 호스트 환경에선 DOM을 문서에 엑세스하고 조작하기 위한 API로 사용한다.

DOM을 통해 개발자는 페이지의 각 부분을 추가하거나 제거, 수정할 수 있다.

DOM은 문서를 노드의 계층 구조로 표현하며, 자료구조에서 트리 구조와 같다.

노드에는 여러 타입이 있는데 각 노드 타입엔 서로 다른 데이터, 메서드가 있다.

노드의 계층 구조를 마크업과 다이어그램으로 표현하면 다음과 같다.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Gunn’s Blog</title>
  </head>
  <body>
    <h1>Blog</h1>
    <p>
      <em>Greeting: </em>
      Hello!
    </p>
  </body>
</html>
```

![DOM Tree](/blog.images/210807/dom_tree.png){: .shadow }
_DOM Tree_

`Document` 노드는 각 문서의 루트이며 자식 노드로 `html` 요소 하나를 갖는다.

`html` 요소는 모든 요소의 최상위 요소이다.

## **Node 타입**

DOM 레벨 1에는 `Node`라는 인터페이스가 있으며 모든 노드 타입은 `Node` 인터페이스를 구현한다.

노드 타입엔 12가지 타입이 있으며 각 타입은 숫자형 상수로 나타낸다. 이 상수는 `Node` 타입에 정의되어 있다.

```javascript
Node.ELEMENT_NODE; // 1
Node.ATTRIBUTE_NODE; // 2
Node.TEXT_NODE; // 3
Node.DOCUMENT_NODE; // 9
// ...
```

`Node`의 프로토타입에는 `nodeType` 프로퍼티가 있다.

`nodeType` 프로퍼티를 사용해 요소의 노드 타입을 확인할 수 있다.

```javascript
document.nodeType == Node.DOCUMENT_NODE; // true
```

### **노드의 이름, 값 확인**

`nodeName`과 `nodeValue` 프로퍼티를 사용해 노드의 정보를 알 수 있다. 노드 타입별로 반환하는 값은 완전히 다르다.

요소 노드와 속성 노드, 텍스트 노드에 `nodeName`과 `nodeValue` 프로퍼티를 사용했을 때 반환하는 값은 다음과 같다.

**- HTML**

```html
<nav class="gnb" role="navigation">
  <ul class="gnb__list">
    <li class="gnb__item gnb__item--home">HOME</li>
    <li class="gnb__item">CATEGORIES</li>
    <li class="gnb__item">TAGS</li>
  </ul>
</nav>
```

**- JavaScript**

```javascript
var gnb = document.querySelector(".gnb");
var gnbItemHome = document.querySelector(".gnb__item--home");

console.log(gnb.nodeType); // 1
console.log(gnb.nodeName); // NAV
console.log(gnb.nodeValue); // null

console.log(gnb.attributes); // NameNodeMap {0: class, 1: role, ...}
console.log(gnb.attributes[1].nodeType); // 2
console.log(gnb.attributes[1].nodeName); // role
console.log(gnb.attributes[1].nodeValue); // navigation

console.log(gnbItemHome.childNodes[0].nodeType); // 3
console.log(gnbItemHome.childNodes[0].nodeName); // #text
console.log(gnbItemHome.childNodes[0].nodeValue); // HOME
```

| Node Type      | nodeName                                       | nodeValue                                     |
| -------------- | ---------------------------------------------- | --------------------------------------------- |
| Element Node   | 태그 이름을 반환하며 데이터 타입은 문자열이다. | 모든 요소 노드의 `nodeValue` 값은 `null`이다. |
| Attribute Node | 속성명을 반환하며 데이터 타입은 문자열이다.    | 속성 값을 반환한다.                           |
| Text Node      | `#text`를 반환하며 데이터 타입은 문자열이다.   | 문자열을 반환한다.                            |

### **NodeList 객체**

각 노드에는 `childNodes` 프로퍼티가 존재하며, 이 프로퍼티에는 `NodeList` 객체가 있다.

`NodeList` 객체는 노드의 컬렉션으로 자식 노드를 배열 형태로 저장하는데 대괄호 표기법으로 자식 노드에 접근할 수 있다.

`NodeList` 객체는 배열과 같이 `length` 프로퍼티로 자식 노드의 개수를 알아내거나 `forEach` 메서드로 자식 노드를 순회하며 함수를 실행할 수 있다. 단, `NodeList`는 `Array`의 인스턴스는 아니다.

`NodeList`는 DOM 구조에 대한 쿼리 결과이며 DOM이 수정되면 실시간으로 `NodeList`에 반영된다.

```javascript
var gnbList = document.querySelector(".gnb__list");
var gnbChildNodes = gnbList.childNodes;

console.log(gnbChildNodes); // NodeList(7) [text, li.gnb__item.gnb__item--home, ...]

var li = document.createElement("li");
gnbList.appendChild(li);

console.log(gnbChildNodes); // NodeList(8) [text, li.gnb__item.gnb__item--home, ..., li]
```

`gnbList`의 자식 노드 컬렉션 정보를 갖고 있는 `NodeList` 객체를 `gnbChildNodes` 변수에 할당했다.

`li` 요소를 생성하고 `appendChild` 메서드를 사용해 `gnbList`의 자식 노드로 `li` 요소를 추가했다.

`gnbChildNodes` 변수에 저장된 `NodeList` 객체의 노드 개수가 7개에서 8개로 실시간 반영된 것을 확인할 수 있다.

> **💡 `ul` 요소의 자식 요소에 텍스트 노드가 있는 이유는?**  
> 코드의 가독성을 위해 줄바꿈을 하면 이스케이프 문자 `\n`을 입력한 것으로 판단하기 때문에 텍스트 노드를 자식 노드로 갖게 된다.

#### **children 프로퍼티와 childNodes 프로퍼티**

`children` 프로퍼티는 `childNodes` 프로퍼티와 달리 `HTMLCollection` 객체를 반환한다.

`HTMLCollection` 객체는 `children` 프로퍼티를 호출한 요소의 모든 자식요소를 포함한다.

즉, `TEXT_NODE`, `COMMENT_NODE`와 같은 비요소는 포함하지 않는다.

`HTMLCollection` 객체 역시 `NodeList` 객체와 같이 라이브 객체이기 때문에 DOM의 변화에 따라 실시간으로 반영한다.

### **노드 사이의 관계**

모든 노드에는 부모, 자식, 형제 노드에 접근할 수 있는 프로퍼티가 존재한다.

```javascript
var gnbList = document.querySelector(".gnb__list");

console.log(gnbList.childNodes); // NodeList(7) [text, li.gnb__item.gnb__item--home, text, li.gnb__item, text, li.gnb__item, text]

console.log(gnbList.childNodes[0].parentNode); // <ul class="gnb__list">...</ul>
console.log(gnbList.childNodes[0].nextSibling); // <li class="gnb__item gnb__item--home">...</li>
console.log(gnbList.childNodes[0].previousSibling); // null

console.log(gnbList.firstChild); // #text
console.log(gnbList.lastChild); // #text
```

`gnbList` 노드의 자식 노드에는 총 7개의 자식 노드가 존재한다.

`gnbList` 노드의 자식 노드에 `parentNode` 프로퍼티를 사용하면 모두 `gnbList` 노드를 반환한다.

`gnbList` 노드의 자식 노드 간에 관계를 가리키는 프로퍼티로 `nextSibling`와 `previousSibling` 프로퍼티가 있으며, 이전 또는 다음 노드가 존재하지 않으면 `null` 값을 반환한다.

자식 노드 중 첫 번째 노드와 마지막 노드를 가리키는 프로퍼티에는 `firstChild`와 `lastChild` 프로퍼티가 있다.

비요소 노드를 제외한 요소 노드에만 접근하는 프로퍼티는 다음과 같다.

`parentElement`, `nextElementSibling`, `previousElementSibling`, `firstElementChild`, `lastElementChild` 프로퍼티를 사용한다.

### **노드 조작**

#### **appendChild 메서드**

`appendChild` 메서드는 `NodeList` 객체의 마지막 노드에 새로운 노드를 추가하고 새로 추가한 노드를 반환한다.

`appendChild` 노드를 실행하면 새로 추가한 노드, 부모 노드, 새로 추가한 노드의 이전 노드의 모든 관계 포인터가 업데이트된다.

```javascript
var returnedNode = someNode.appendChild(newNode);
console.log(returnedNode == newNode); // true
console.log(someNode.lastChild == newNode); // true
```

1번 줄에서는 `someNode`의 자식노드로 `newNode`를 추가하고 `appendChild` 메서드가 반환한 새로 추가한 노드를 변수 `returnedNode`에 저장했다.

2번 줄에서 `newNode`와 `appendChild` 메서드가 반환한 값을 비교하면 `true`이다.

3번 줄에서는 새로 추가한 노드가 `someNode`의 마지막 자식 노드와 같음을 알 수 있다.

만약 `appendChild` 메서드의 인자로 넘겨진 노드가 `document`에 이미 존재하는 노드를 참조하고 있다면 해당 노드는 현재 위치에서 새로운 위치로 이동한다.

DOM 트리에 수많은 노드가 포인터로 연결되어 있지만 노드가 동시에 두 위치에 존재할 수는 없기 때문이다.

```javascript
var returnedNode = someNode.appendChild(someNode.firstChild);
console.log(returnedNode == someNode.firstChild); // false
console.log(returnedNode == someNode.lastChild); // true
```

1번 줄에서 `someNode`의 마지막 자식 노드로 `someNode`의 첫 번째 자식 노드를 추가하는 `appendChild` 메서드를 실행하고 반환한 노드를 변수 `returnedNode`에 저장했다.

2번 줄에서 `returnedNode`와 `someNode.firstChild` 노드를 비교했지만 결과는 `false`이다. `someNode.firstChild` 노드를 복사했기 때문에 `returnedNode`와 다른 참조 값을 가져 결과가 `false`가 되었다고 생각할 수 있지만 3번 줄의 코드 실행 결과를 보면 `returnedNode`와 `someNode.lastChild` 노드가 같은 노드인 것과 기존의 `someNode.firstChild` 노드가 마지막 노드로 위치가 바뀐 것을 알 수 있다.

`somdeNode` 노드에 `childNodes` 프로퍼티를 사용하면 그 결과를 명확히 확인해 볼 수 있다.

#### **insertBefore 메서드**

특정 부모 노드에 `insertBefore` 메서드를 호출하면 인자로 넘긴 노드를 해당 부모 노드의 자식 노드로 삽입할 수 있다.

`insertBefore` 메서드에 두 개의 인자를 전달하며 첫 번째 인자에는 삽입할 노드를 입력하고 두 번째 인자에는 삽입할 노드가 자식 노드의 몇 번째에 위치할지 결정할 기준 노드를 입력한다. 삽입할 노드는 기준 노드의 `previousSibling` 노드가 된다.

두 번째 인자에 `null`을 전달하면 `appendChild` 메서드와 같이 마지막 자식 노드로 삽입한다.

`insertBefore` 메서드는 실행 후에 삽입한 노드를 반환한다.

```javascript
var returnedNode = parentNode.insertBefore(nodeToInsert, null);
console.log(parentNode.lastChild == returnNode); // true
```

1번 줄에선 `parentNode`의 자식 노드로 `nodeToInsert` 노드를 삽입한다.

`insertBefore` 메서드의 두 번째 인자로 `null`을 전달했기 때문에 삽입할 노드는 `parentNode`의 마지막 자식 노드가 되며, `insertBefore` 메서드의 실행 결과로 삽입한 노드가 변수 `returnedNode`에 저장되었다.

`insertBefore` 메서드를 실행해 삽입하는 노드는 메서드를 호출하는 부모 노드가 DOM 트리에서 어느 depth에 있느냐에 따라 이동이 자유롭다.

예를 들어 DOM 트리에서 가장 깊은 depth에 있는 노드를 `body` 요소의 첫 번째 자식 요소로 삽입할 수 있다.

#### **replaceChild 메서드**

`replaceChild` 메서드는 특정 부모 노드의 자식 노드를 다른 노드로 교체한다.

`replaceChild` 메서드에 두 개의 인자를 전달하며 첫 번째 인자에는 교체할 새로운 노드를 입력하고 두 번째 인자에는 교체될 노드를 입력한다.

교체되는 노드는 DOM 트리에서 제거되며 `replaceChild` 메서드 실행이 마치면 교체된 노드를 반환한다.

교체된 노드가 DOM 트리에서 제거 됐다고 해서 최상위 노드인 `document`의 소유에서 완전히 제거된 것은 아니다.

```javascript
var gnbList = document.querySelector(".gnb__list");
var gnbItemHome = document.querySelector(".gnb__item--home");

var li = document.createElement("li");
li.textContent = "HOMEPAGE";

console.log(gnbList.replaceChild(li, gnbList.firstElementChild)); // <li class="gnb__item gnb__item--home">HOME</li>
console.log(gnbItemHome.parentNode); // null
console.log(gnbItemHome.ownerDocument); // #document
```

7번 줄에서 `gnbList`의 첫 번째 자식 요소를 새로 생성한 `li` 요소로 대체했다.

`replaceChild` 메서드의 실행 결과로 대체된 기존 요소가 반환된 것을 알 수 있다.

8번 줄에서 기존에 존재하던 `gnbList`의 첫 번째 자식 요소가 새로운 노드로 대체되면서 부모 노드가 `null`이 됐다.

하지만 `gnbItemHome` 노드의 최상위 노드가 여전히 `document` 노드인 것을 확인할 수 있다.

#### **removeChild 메서드**

`removeChild` 메서드는 하나의 인자만 전달 받으며 메서드를 호출한 노드의 자식 노드 중 인자로 전달한 노드를 제거한다.

`replaceChild` 메서드와 마찬가지로 `removeChild` 메서드로 제거한 노드는 DOM 트리에서만 제거 됐을 뿐 여전히 `document` 노드를 최상위 노드로 가리킨다.

```javascript
console.log(gnb.removeChild(gnb.firstElementChild)); // <ul class="gnb__list">...</ul>
```

#### **cloneNode 메서드**

`cloneNode` 메서드는 메서드를 호출한 노드를 복제하고 복제된 노드를 반환한다.

`cloneNode` 메서드는 `boolean` 타입의 인자 하나를 전달 받으며 `true`이면 자손 노드 전체를 복제하고 인자를 전달하지 않거나 `false`를 전달하면 해당 노드 하나만 복제한다.

복제된 노드는 부모 노드가 없기 때문에 `appendChild` 메서드와 같은 메서드로 DOM 트리에 추가해야 한다.

```javascript
var dupNode = gnbList.cloneNode(true);

console.log(dupNode); // <ul class="gnb__list">...</ul>
console.log(dupNode.parentNode); // null
```

## **각주**

- Nicholas C. Zakas(2012), “JavaScript for Web Developers”
- DOM Standard, [Old-style collections: NodeList and HTMLCollection](https://dom.spec.whatwg.org/#nodelist)
