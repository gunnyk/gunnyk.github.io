---
title: Electron - 일렉트론 앱 만들기(Create an Electron App)
author: gunny
date: 2021-07-02 03:25:00 +0900
last_modified_at: 2021-07-11 01:40:00 +0900
categories: [Framework, Electron]
tags:
  [
    electron,
    electron app,
    create an electron app,
    getting started with electron,
    일렉트론,
    일렉트론 앱,
    일렉트론 앱 만들기,
    일렉트론 시작하기,
  ]
---

## **일렉트론 시작하기**

### **초기 셋업**

일랙트론을 시작하기 위해서는 Node.js, npm, Electron, Chromium을 설치해야한다.

Node.js와 npm 설치 후 터미널에 다음 명령을 실행해 관련 패키지를 설치한다.

```terminal
npm i -D electron@latest
```

프로젝트 폴더를 만들고 터미널에 다음 명령을 실행해 관련 패키지를 설치한다.

```terminal
npm install --save-dev electron
```

`package.json` 파일의 형식은 다음과 같아야 한다.

```json
{
  "name": "my-electron-app",
  "version": "1.0.0",
  "description": "Hello World!",
  "main": "main.js",
  "author": "my name",
  "license": "MIT"
}
```

- `name` : 애플리케이션 이름

- `version` : 앱 버전

- `main` : 애플리케이션의 entry point로 `main.js`로 설정해야한다.

개발 모드에서 앱을 열 수 있도록 `scripts`를 추가한다.

```json
"scripts": {
    "start": "electron ."
}
```

### **웹 페이지 생성하기**

앱에 로드될 콘텐츠를 만들기 위해 루트 폴더에 `index.html`을 생성한다.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <!-- https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP -->
    <meta
      http-equiv="Content-Security-Policy"
      content="default-src 'self'; script-src 'self'"
    />
    <meta
      http-equiv="X-Content-Security-Policy"
      content="default-src 'self'; script-src 'self'"
    />
    <title>Hello World!</title>
  </head>
  <body>
    <h1>Hello World!</h1>
    We are using Node.js <span id="node-version"></span>, Chromium
    <span id="chrome-version"></span>, and Electron
    <span id="electron-version"></span>.
  </body>
</html>
```

### **브라우저 윈도우에 웹 페이지 로드하기**

앱에 웹 페이지를 로드하기 위해 entry point로 설정했던 `main.js`를 생성하고 두 가지 모듈을 추가한다.

**- 앱의 이벤트 라이프사이클을 제어하는 `app` 모듈**

```javascript
const { app, BrowserWindow } = require("electron");
```

**- 앱을 생성하고 관리하는 `BrowserWindow` 모듈**

```javascript
function createWindow() {
  const win = new BrowserWindow({
    width: 800,
    height: 600,
  });

  win.loadFile("index.html");
}
```

**`createWindow` 함수를 호출해 앱을 실행 👏**

Electron에서 브라우저 윈도우는 앱 모듈에 `ready` 이벤트가 전달된 후에 생성할 수 있다.

`app` 모듈에 `whenReady` API를 사용해서 이벤트를 기다리고 `then` 메서드가 `Promise`를 리턴한 후 `createWindow` 함수를 호출하도록 한다.

```javascript
app.whenReady().then(() => {
  createWindow();
});
```

터미널에 `npm start` 명령을 실행해 앱을 오픈한다.

![Electron App Window](/blog.images/210702/app_window.png)
_일렉트론 앱 실행 윈도우_

## **윈도우 라이프사이클 관리하기**

### **모든 윈도우가 닫히면 앱 종료하기(Windows & Linux)**

윈도우즈와 리눅스에서는 보통 모든 윈도우가 닫히면 앱이 종료된다.

이를 구현하기 위해 `app` 모듈에 `window-all-closed` 이벤트가 전달되면 `app.quit()`를 호출해서 앱을 종료하도록 함수를 작성한다.

하지만 맥OS의 경우 보통 열려 있는 윈도우가 없어도 앱이 계속 실행되며, 사용 가능한 윈도우가 없을 때 앱을 활성화하면 새 앱이 열린다.

즉, 맥OS 환경에서는 사용자가 명시적으로 앱을 종료시키지 않는 이상 모든 윈도우가 닫혀도 앱이 계속 실행되도록 해야하기 때문에 프로세스의 플랫폼 값을 체크하는 조건문을 추가해야한다.

```javascript
app.on("window-all-closed", function () {
  if (process.platform !== "darwin") app.quit();
});
```

### **열려있는 윈도우가 없으면 새 윈도우 열기(macOS)**

맥OS 환경에서 열려있는 윈도우가 없을 때 앱을 활성화하면 새 앱이 열리게 하는 방법은 다음과 같다.

`app` 모듈에 `activate` 이벤트가 전달되면 열려있는 윈도우 개수를 체크해서 0개이면 `createWindow` 메서드를 호출해 새 앱을 열 수 있도록 한다.

윈도우는 `ready` 이벤트가 발생하기 전에는 생성할 수 없으므로 앱이 초기화 된 후에 `activate` 이벤트를 받아야한다.

`whenReady` 콜백 내에서 이벤트 리스너를 연결하면 된다.

```javascript
app.whenReady().then(() => {
  createWindow();

  app.on("activate", function () {
    if (BrowserWindow.getAllWindows().length === 0) createWindow();
  });
});
```

## **Node.js API에 엑세스하기**

`main` 프로세스에서 Node의 전역 프로세스 객체를 통해 정보에 접근 하는 것은 가능하지만 `renderer`의 문서 컨텍스트에는 접근할 수 없기 때문에 `DOM`을 수정하는 것은 불가능하다.

`renderer`에 `preload` 스크립트를 추가하면 문서 컨텍스트에 접근할 수 있다.

`preload` 스크립트는 `renderer` 프로세스가 로드되기 전에 실행되며, `renderer` 전역과 Node.js 환경에 모두 접근할 수 있다.

모든 Node.js API는 `preload` 프로세스에서 사용할 수 있다.

`preload.js` 파일을 생성하고 다음 코드를 추가한다.

```javascript
window.addEventListener("DOMContentLoaded", () => {
  const replaceText = (selector, text) => {
    const element = document.getElementById(selector);
    if (element) element.innerText = text;
  };

  for (const dependency of ["chrome", "node", "electron"]) {
    replaceText(`${dependency}-version`, process.versions[dependency]);
  }
});
```

위 코드는 Node.js의 `process.versions` 객체에 접근해 프로세스 버전을 `HTML` 요소에 삽입하는 함수를 실행한다.

이 스크립트를 `renderer` 프로새스애 연결하기 위해 `preload` 스크립트 경로를 `BrowserWindow` 생성자의 `webPreferences.preload` 옵션에 추가해야한다.

`main.js` 파일에 다음 코드를 추가한다.

```javascript
const { app, BrowserWindow } = require("electron");
const path = require("path");

function createWindow() {
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, "preload.js"),
    },
  });

  win.loadFile("index.html");
}
// ...
```

2번 줄에 Node.js `path` 모듈을 추가하고 8번 줄에 `preload.js`를 연결하는 코드를 작성했다.

## **각주**

- [일렉트론 공식 문서](https://www.electronjs.org)
