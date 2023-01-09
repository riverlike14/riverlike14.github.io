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

## Window 객체

# Location 객체

# History 객체

# Navigator 객체

# Screen 객체

# Document 객체

# 창 제어하기
