---
title: "[자바스크립트] 웹 브라우저의 객체"
---

# 클라이언트 측 자바스크립트

## 웹 브라우저에서 자바스크립트가 하는 일

## 자바스크립트 코드를 삽입하는 방법

## 웹 브라우저에서의 자바스크립트 실행 순서

> **렌더링 엔진**: 웹 브라우저에서 HTML 문서를 분석하고 표시하는 프로그램.

1. `window`객체 생성.
2. `document`객체 생성.
- `window`객체의 프로퍼티(`window.document`)로 생성됨.
- `document.readyState`의 초기값은 "loading".
3. HTML 구문 분석.
- `document`객체 요소와 텍스트 노드를 추가.
4. HTML 구문을 해석하는 도중 `script`요소를 만나면 `script` 안의 코드 구문 분석.
- `script` 코드 구문에 오류가 없으면 이 시점에서 동기적으로 실행.
- 즉, HTML 구문 분석 일시 중지, 자바스크립트 코드 실행, HTML 구문 분석 재개.
5. HTML 구문을 모두 읽고 DOM 트리 구축 완료.
- `document.readyState`의 값이 "interactive"로 바뀜.
6. 웹 브라우저는 `DOMContentLoaded` 이벤트를 발생시켜 `document` 객체에 DOM 트리 구축 완료를 알림.
7. `img`등의 요소가 외부 리소스를 읽어들임.
8. 모든 리소스를 읽어 들임.
- `document.readyState`의 값이 "complete"로 바뀜.
- 웹 브라우저는 `window`객체를 상대로 `laod` 이벤트를 발생시킴.
9. 이 시점부터 다양한 이벤트 수신.
- 이벤트가 발생하면 비동기로 호출.

### 자바스크립트로 HTML 요소를 조작
- `window.onload`의 이벤트 처리기에 초기화 스크립트 등록.
  - 문서를 다 읽어 들인 후 실행.
  - 외부 리소스를 읽어 들이는 시간 만큼 사용자가 기다려야 함.
- `DOMContentLoaded` 이벤트의 이벤트 처리기에 초기화 스크립트 등록.
  - DOM 트리 구축 후 실행.
  - `document.addEventListener("DOMContentLoaded", function(e) {...}, false);`


### `async`와 `defer` 속성

- 자바스크립트 코드 실행 시 HTML 구문 분석을 막지 않음.
- 인라인 스크립트에서는 사용 불가능.
  - `<script async src="../scripts/sample.js"></script>`
  - `<script defer src="../scripts/sample.js"></script>`
- `async`: HTML 구문 분석 처리를 막지 않으면서 `script` 코드가 비동기적으로 실행.
  - 실행 순서가 보장되지 않음.
- `defer`: DOM 트리 구축이 끝난 뒤 `script` 코드 실행.
  - 요소 객체에 초기화 작업 가능.
  - `DOMContentLoaded` 이벤트의 대안으로 활용 가능.
- 단, `document.write`은 `async`와 `defer` 속성을 무시하고 동기적으로 실행.

### CSS와 렌더링

CSS 렌더링은 DOM 트리 구축 과정과 함께 실행됨.
1. CSS 파서가 CSS 코드를 분석해서 스타일 규칙으로 만듦.
2. 스타일 규칙을 바탕으로 렌더 트리라는 트리를 DOM 트리와 함께 생성.
- 표시해야 하는 요소만 저장.
- 저장된 요소에느느 스타일 정보 추가.
3. 렌더 트리가 만들어지면 DOM의 각 노드의 위치와 크기를 결정.
4. DOM의 각 노드를 렌더 트리의 스타일 정보를 바탕으로 그림.
- HTML 문서를 읽어 들이는 과정에서 단계적으로 동시에 실행.

<p align="center">
  <img alt="렌더링의 흐름" src="https://i.imgur.com/G2FvCKo.png" />
</p>

렌더 트리를 구축해서 배치하고 그리는 과정이 실행되는 경우
- 처음 리소스를 읽어 들일 때.
- 자바스크립트로 동적인 처리를 할 때.
- 사용자의 조작으로 HTML/CSS 코드가 바뀔 때.


## 웹 브라우저의 호환성

|웹 브라우저|제작사|렌더링 엔진|자바스크립트 엔진|
|:-:|:-:|:-:|:-:|
|Chrome|Google|Blink|V8|
|Safari|Apple|Webkit|JavaScriptCore|
|Edge|Microsoft|Blink|V8|
|Firefox|Mozilla|Gecko|SpiderMonkey|
|Opera|Opera|Blink|V8|

- 최신 웹 브라우저들은 HTML5, CSS3, ECMAScript5를 대부분 지원.
- ECMAScript6 구현도 진행중.
- 오래된 웹 브라우저는 ECMAScript3은 지원하지만 ECMAScript5는 지원하지 않음.


## 크로스 브라우징 대책

> **크로스 브라우징 대책**: ECMAScript5를 지원하지 않는 브라우저에서도 문제없이 작동하게끔 대응하는 작업.

- 인터넷 익스플로러 8 이상.
- 크롬, 파이어폭스, 사파리, 오페라, 엣지 등은 최신 버전.
- 점유율이 낮은 웹 브라우저는 지원 중단을 고려.

### 기능성 테스트

해당 기능을 지원하는지 테스트.
- 기능을 지원하지 않는 환경에서는 다른 코드로 구현.
- 오래된 웹 브라우저가 업그레이드해도 문제없이 동작.
- 예를 들어, IE8은 `addEventListener` 메서드 대신 `attachEvent` 메서드 제공.

```js
if (element.addEventListener) { // DOM 메서드 테스트
  element.addEventListener("click", handler, false);
} else if (element.attachEvent) { // IE 메서드 테스트
  element.attachEvent("onclick", handler);
} else {
  element.onclick = handler;
}
```

### 브라우저 테스트

특정 웹 브라우저에만 국한된 버그에 대처.
- 웹 브라우저의 종류, 버전, OS 등의 정보 파악.
- `Navigator` 객체 사용.
- 꼭 필요한 경우에만 사용하되 기본적으로는 사용하지 않는게 바람직.

### 라이브러리를 사용해서 대응하기

호환성 문제를 해결해주는 라이브러리를 사용.
- jQuery가 대표적.

## Window 객체

클라이언트측 자바스크립트에서 가장 중요한 `window` 객체.
- `window`객체는 전역 객체이며, 전역 변수는 `window`객체의 프로퍼티.
- 웹 브라우저의 다양한 객체는 모두 `window`객체의 프로퍼티.
  - 예를 들어 `window.document`로 `document`객체 참조 가능.
  - `window.`은 생략 가능.

# Location 객체

> **Location 객체**: 창에 표시되는 문서의 URL을 관리.

- `window.location`, `location`, 또는 `document.location`으로 참조.
- 이번 절에는 다음의 URL이 주어져 있다고 가정.
  - http://www.example.com:80/test/index.html?q=value#anchor

## Location 객체의 프로퍼티

프로퍼티는 수정 가능.
- 필요한 경우, 웹 서버에 수정을 요청하고 응답에 따라 창을 갱신.

|프로퍼티|설명|예|
|-|-|-|
|hash|앵커 부분|#anchor|
|host|호스트 이름:포트 번호|www.example.com:80|
|hostname|호스트 이름|www.example.com|
|href|전체 URL|http://www.example.com:80/test/index.html?q=value#anchor|
|pathname|상대경로|/test|
|port|포트 번호|80|
|protocol|프로토콜|http:|
|search|질의 문자열|?q=value|

## Location 객체의 메서드

|메서드|설명|
|-|-|
|assign(url)|url이 가리키는 문서를 읽음. 웹 브라우저의 이력에 남음.|
|reload()|문서를 다시 읽어 들임.|
|replace(url)|url로 이동. 웹 브라우저의 이력에 남지 않음.|
|toString()|location.href 값 반환.|

# History 객체

> **History 객체**: 창의 웹 페이지 열람 이력을 관리.

- `window.history` 또는 `history`로 참조.

## History 객체의 프로퍼티

|프로퍼티|설명|
|-|-|
|length|현재 세션의 이력 개수(읽기 전용)|
|scrollRestoration|웹 브라우저의 자동 스크롤 기능을 설정 또는 해제. "auto" 또는 "manual"로 설정 가능.|
|state|pushState와 replaceState 메서드로 설정한 state 값(읽기 전용)|

## History 객체의 메서드

|메서드|설명|
|-|-|
|back()|뒤로 가기|
|forward()|앞으로 가기|
|go(number)|앞으로 number만큼 가기. number가 음수면 뒤로 가기|
|pushState(state, title, url)|페이지 이동 없이 열람 이력만 추가|
|replaceState(state, title, url)|현재 창의 열람 이력 수정|

- 뒤로 가기/앞으로 가기 버튼을 누르거나 `back`, `forward`, `go` 메서드를 호출했을 때, 또는 같은 웹페이지 위에서 열람 이력을 수정해도 `popstate` 이벤트 발생.
- `pushState` 메서드는 주로 Ajax 방식(웹 페이지 이동 없이 페이지 내용 수정)으로 웹 페이지를 그릴 때 사용.

# Navigator 객체

# Screen 객체

# Document 객체

# 창 제어하기
