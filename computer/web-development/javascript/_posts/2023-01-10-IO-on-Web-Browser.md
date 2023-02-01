---
title: "[자바스크립트] 웹 브라우저에서의 입출력"
---

# 대화상자 표시하기

입출력이 간단하지만 실제로 자주 사용되지는 않음.

## 대화상자

대화상자: 입력을 하거나 메시지를 확인하기 위해 별도로 여는 창.
- `window.alert`, `window.prompt`, `window.confirm` 세 가지.
- 대화상자는 모달(Modal).
  - 대화상자가 떠 있는 중에는 부모의 창을 조작할 수 없음.

## alert(경고 대화상자)

![Alert](https://imgur.com/bP2NlR9.png)

`alert`: 경고 대화상자를 표시.
- 인수: 경고 메시지를 표시하는 문자열.
- `alert("Hello");`
- 확인을 누르면 대화상자가 사라지고 코드 제어권이 호출한 부분으로 돌아감.

## prompt(입력 대화상자)

![Prompt](https://imgur.com/WVrJ5kI.png)

`prompt`: 입력 대화상자를 표시.
- 인수: 입력 요청 메시지를 표시하는 문자열.
  - 두 번째 인수는 초기 입력값.
- `var name = prompt("What is your name?");`
- 사용자의 입력을 반환.

## confirm(확인 대화상자)

![Confirm](https://imgur.com/HHJsvxc.png)

`confirm`: 확인 대화상자를 표시.
- 인수: 메시지를 표시하는 문자열.
- `var ans = confirm("Are you sure?");`
- 논리값을 반환.

# `console`

디버깅에 자주 사용.

## `Console` 객체의 메서드

- `alert`에 비해 간단.
- 부모 창의 동작을 간섭하지 않음.

|메서드|설명|
|:-|:-|
|`console.dir`|객체의 대화형 목록을 출력|
|`console.error`|오류 메시지를 출력|
|`console.info`|메시지 타입 로그를 출력|
|`console.log`|일반 로그를 출력|
|`console.time`|처리 시간 측정용 타이머를 시작|
|`console.timeEnd`|처리 시간 측정용 타이머를 정지, 측정된 시간을 ms단위로 출력|
|`console.trace`|스택 트레이스를 출력|
|`console.warn`|경고 메시지를 출력|

## 콘솔에 텍스트 출력하기

`console.log`, `console.info`, `console.warn`, `console.error` 메서드들이 받는 인수와 표시하는 문자열은 동일.
- 로그 시작 부분에 주의 표식을 추가하는 등의 스타일에만 차이.
- `Console` 객체의 메서드에 서식 문자열 사용 가능.
  - `console.log("His name is %s and is %d years old.", name, age);`
  - |서식 문자열|설명|주의사항|
    |:-:|:-|:-:|
    |%o|객체를 가리키는 하이퍼링크로 변환|Node.js는 지원 X|
    |%d|정수로 변환|Node.js는 숫자로 변환|
    |%i|정수로 변환|Node.js는 지원 X|
    |%s|문자열로 변환||
    |%f|부동소수점 값으로 변환|Node.js는 지원 X|

## 객체의 프로퍼티를 목록으로 표시하기

`console.dir`: 객체의 프로퍼티를 나열.
- 인수로 객체 하나를 받음.
- `console.dir(document.body);`

## 타이머

`console.time`, `console.timeEnd`: 타이머 실행 및 종료.
- 인수로 타이머의 이름을 뜻하는 문자열을 입력.
- 시간 측정을 시작할 때 `console.time`에 타이머 이름을 입력.
- 시간 측정 작업이 끝나면 `console.timeEnd`에 동일한 타이머 이름을 입력.
  - ```js
    console.time("userAnswerTime");
    alert("Press OK");
    console.timeEnd("userAnswerTime");
    ```

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

HTML 요소 안의 내용을 동적으로 읽고 쓰는 방법.

## HTML 요소의 `innerHTML` 프로퍼티로 읽고 쓰기

`innerHTML` 프로퍼티: HTML 요소의 내용을 가리킴.
- ![Stopwatch](https://imgur.com/LVdeI0V.png)
- ```html
  <html>
    <head>
      <script>
        window.onload = function() {
          const startButton = document.getElementById("start");
          const stopButton = document.getElementById("stop");
          const display = document.getElementById("display");

          var timer;

          const start = () => {
            startButton.onclick = null;
            stopButton.onclick = stop;
            const startTime = new Date();

            timer = setInterval(() => {
              const currentTime = new Date();
              const timeDiff = currentTime - startTime;
              display.innerHTML = (timeDiff / 1000).toFixed(2); // display.innerHTML
            }, 10);
          };

          const stop = () => {
            clearTimeout(timer);
            startButton.onclick = start;
            stopButton.onclick = null;
          }

          startButton.onclick = start;
          stopButton.onclick = null;
        };
      </script>
    </head>
    <body>
      <div id="display">0.00</div>
      <button id="start">Start</button>
      <button id="stop">Stop</button>
    </body>
  </html>
  ```

## 폼 컨트롤의 입력 값 읽기

![체질량지수 계산하기](https://imgur.com/v1lOUda.png)

`input` 요소 등 폼 컨트롤 요소를 사용.
- 사용자의 입력 값을 자바스크립트 프로그램에서 사용 가능.
- |요소|type 속성|프로퍼티|설명|
  |:-:|:-:|:-:|:-|
  |input|number, text 등|value|입력된 값을 문자열로 변환|
  |input|ckeckbox, radio 등|ckecked|선택 여부를 논리값으로 반환|
  |select||selectedIndex|선택된 option요소를 가리키는 0부터 시작하는 번호|
  |textarea||value|입력된 문자열|
- ```html
  <html>
    <head>
      <script>
        window.onload = function() {
          const calculateBMI = (height, weight) => {
            return weight / height / height;
          }

          const calculateButton = document.getElementById("calculate");
          calculateButton.onclick = () => {
            const heightInput = document.getElementById("height");
            const weightInput = document.getElementById("weight");
            const height = parseFloat(heightInput.value / 100); // heightInput.value
            const weight = parseFloat(weightInput.value); // weightInput.value

            const bmi = calculateBMI(height, weight);

            document.getElementById("bmi").innerHTML = bmi.toFixed(1);
          }
        };
      </script>
    </head>
    <body>
      <div>
        <label for="height" >키:</label>
        <input id="height" ></input>cm
      </div>
      <div>
        <label for="weight" >몸무게:</label>
        <input id="weight" ></input>kg
      </div>
      <div>
        당신의 체질량 지수는 <span id="bmi">?</span>입니다.
      </div>
      <button id="calculate" >계산</button>
    </body>
  </html>
  ```

## `document.write`

`document.write`: 인수로 받은 문자열을 HTML 문서의 body 요소 안에 출력.
- 웹 브라우저는 수정된 HTML 문서를 표시.
- `document.write` 사용은 권장되지 않음.
- ```html
  <html>
    <head>
      <script>
        window.onload = () => {
          const button = document.getElementById("button");
          button.onclick = () => {
            const now = new Date();
            const month = now.getMonth() + 1;
            const day = now.getDate();

            document.write("<p>오늘은 " + month + "월 " + day + "일 입니다.</p>");
          };
        };
      </script>
    </head>
    <body>
      <button id="button">오늘은 몇월 며칠인가요?</button>
    </body>
  </html>
  ```

# Canvas를 활용한 컴퓨터 그래픽스

(추가 예정)
