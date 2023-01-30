---
title: "[자바스크립트] 웹 브라우저에서의 입출력"
---

# 대화상자 표시하기

# `console`

# 이벤트 처리기 등록하기와 타이머 알아보기

## 이벤트 처리기

> **이벤트 주도형 프로그램(Event driven program)**: 이벤트가 발생할 때까지 기다렸다가 이벤트가 발생했을 때 미리 등록해 둔 작업을 수행.

> **이벤트 처리기**: 이벤트가 발생했을 때 실행되는 함수.

함수를 이벤트 처리기로 등록하는 방법:
- HTML 요소의 속성으로 등록.
- DOM 요소의 프로퍼티로 등록.
- `addEventListener` 메서드 사용.

## HTML 요소의 속성에 이벤트 처리기 등록하기

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Print current time on the console</title>
  <script>
  function displayTime() {
    const d = new Date();
    console.log("Current Time is " + d.toLocaleString());
  }
  </script>
</head>
<body>
  <input type="button" value="click" onclick="displayTime()">
</body>
</html>
```
- HTML 코드와 자바스크립트 코드가 뒤섞이는 단점.

주요 이벤트 처리기 이름
- |이벤트 처리기 이름|이벤트 종류|
  |:-|:-|
  |`onclick`|마우스로 클릭했을 때|
  |`ondblclick`|마우스로 더블 클릭했을 때|
  |`onmousedown`|마우스 버튼을 눌렀을 때|
  |`onmouseup`|마우스 버튼에서 손가락을 떼었을 때|
  |`onmousemove`|마우스 포인터가 HTML 요소 위에서 움직임|
  |`onmouseout`|마우스 포인터가 HTML 요소를 이탈|
  |`onmouseover`|마우스 포인터가 HTML 요소 위에 놓여 있음|
  |`onkeydown`|키보드의 키를 눌렀을 때(Keycode 값)|
  |`onkeypress`|키보드의 키를 눌렀을 때(ASCII 값)|
  |`onkeyup`|키보드의 키에서 손가락을 떼었을 때(Keycode 값)|
  |`onchange`|`input` 요소의 값이 바뀌었을 때|
  |`onblur`|`input` 요소가 포커스를 잃었을 때|
  |`onfocus`|`input` 요소에 포커스를 맞추었을 때|
  |`onselect`|텍스트 필드 등의 텍스트를 선택했을 때|
  |`onsubmit`|폼 제출 버튼을 눌렀을 때|
  |`onload`|HTML을 모두 읽어 들였을 때|
  |`onunloadWeb`|웹 페이지가 메모리에서 내려갈 때(예: 다른 페이지로 전환)|
  |`onabort`|페이지나 이미지 읽어 들이기가 중단되었을 때|
  |`onerror`|페이지나 이미지를 읽어 들이는 동안 오류가 발생했을 때|
  |`onresize`|HTML 요소의 크기가 바뀌었을 때|

## DOM에서 가져온 HTML 요소에 이벤트 처리기 지정하기

> **DOM(Document Object Model)**: 자바스크립트 등의 프로그램이 HTML 요소를 조작할 수 있게 하는 인터페이스.

### DOM 객체

DOM에서는 HTML 문서나 HTML 요소를 가리키는 객체로 HTML 문서를 조작.
- `window`: Window 객체. 웹 브라우저 윈도우 하나 또는 탭 하나를 가리킴.
- `document`: Document 객체. HTML 문서 전체를 가리킴.
- 요소 객체: HTML 문서의 요소를 가리킴.

### DOM을 사용해서 이벤트 처리기 등록하기

1. `window.onload`를 사용하여 HTML 문서를 읽어들임.
2. `document.getElementById` 메서드를 사용하여 특정 `id` 속성 값을 가진 요소 객체를 가져옴.
3. 요소 객체의 이벤트 처리기 프로퍼티에 이벤트 처리기로 동작할 함수를 등록.

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Print current time on the console</title>
  <script>
  function displayTime() {
    const d = new Date();
    console.log("Current Time is " + d.toLocaleString());
  }

  window.onload = function() {
    var button = document.getElementById("button");
    button.onclick = displayTime;
  }
  </script>
</head>
<body>
  <input id="button" type="button" value="click">
</body>
</html>
```
1. `window.onload`에 초기 설정 함수를 이벤트 처리기로 등록.
- `window.onload = function() { ... };`
- HTML 코드와 자바스크립트 코드를 분리.
- 이를 위해 `<script>` 요소를 `<head>` 요소의 자식 요소로 배치.
  - `<head>` 요소 안의 `<script>` 요소의 코드가 실행되는 시점에 `<body>` 요소를 아직 읽어 들이지 못함(`<script>` 요소는 **동기 실행** 또는 **블로킹 실행**됨).
  - 이때 `window.onload`에 이벤트 처리기를 등록하면 HTML 문서 전체를 읽어 들인 이후에 이벤트 처리기를 등록함.
- 이 또한 `window` 객체의 `onload` 이벤트에 반응하는 이벤트 처리기를 등록하는 작업.

2. `getElementById` 메서드로 요소 객체를 가져옴.
- `var button = document.getElementById("button");`
- `document.getElementById`는 전달받은 인수를 `id` 속성의 값으로 가지고 있는 HTML 요소의 요소 객체를 반환.
- HTML 요소를 찾지 못하면 `null`을 반환.

3. 이벤트 처리기 프로퍼티에 이벤트 처리기로 동작할 함수를 등록.
- `button.onclick = displayTime;`
- 요소 객체의 `onclick` 프로퍼티 값으로 함수 `displayTime`의 참조를 대입.

### 이벤트 처리기 제거

이벤트 처리기가 등록되어 있지 않은 이벤트 처리기 프로퍼티에는 기본적으로 `null`이 저장.
- 이벤트 처리기를 제거할 때는 `null`을 대입.
- `button.onclick = null;`

### 자바스크립트 프로그램의 작성법

- 자바스크립트 코드는 `<head>` 요소의 `<script>` 요소에 작성.
- `window.onload`에 프로그램의 초기 설정 작업 수행.
- 이벤트 처리기 함수와 그 안에서 사용하는 다양한 함수를 정의. 또는 전역 변수를 선언.

## 타이머

### 지정된 시간이 흐른 후에 함수 실행하기: `setTimeout`

일정 시간이 흐른 후에 한 번만 호출되는 함수 등록.
- `setTimeout(function() { ... }, 2000);`
- 첫 번째 인수는 실행하고자 하는 함수의 참조, 두 번째 인수는 지연 시간(ms).
- `setTimeout()`이 반환한 값을 `clearTimeout()`의 인수로 넘겨서 실행하면 함수 실행이 취소.

### 지정된 시간마다 반복해서 실행하기: `setInterval`

일정한 시간 간격에 따라 반복해서 실행할 함수 등록.
- `setInterval(function() { ... }, 2000);`
- 첫 번째 인수는 실행하고자 하는 함수의 참조, 두 번째 인수는 시간 간격(ms).
- `setInterval()`이 반환한 값을 `clearTimeout()`의 인수로 넘겨서 실행하면 함수 실행이 취소.

# HTML 요소를 동적으로 읽고 쓰기

# Canvas를 활용한 컴퓨터 그래픽스
