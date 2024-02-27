# useEffect와 useRef를 함께 이용하기

useEffect의 작동원리를 알기 위해서는 자바스크립트의 실행 컨텍스트에 대한 이해가 선행되어야 한다.

useEffect는 두 번째 인자로 들어온 의존성 배열에 있는 state나 props가 바뀌면 첫 번재 인자인 함수가 실행될 수 있도록 한다.
이 때 의존성 배열이 비어있을 경우에는 컴포넌트가 마운트 되었을 때 한 번만 실행된다.

그렇다면 실행 컨텍스트란 무엇일까?

실행 컨텍스트는 함수가 '호출'될 때 전달된다.

따라서 useEffect에서 실행되는 함수 내의 상태나 props는 실시간으로 (동기적으로) 반영되지 않을 수 있다는 의미이다.

반면, useRef를 사용한다면 실시간으로 적용될 수 있다.

## JavaScript 실행 컨텍스트

### 1. 개념

1. 실행할 코드에 제공할 환경정보를 모아놓은 객체
1. 동일한 환경에 있는 환경 정보들을 모은 실행컨텍스트를 콜스택에 쌓아올린 후 실행한다.

### 2. 역할 및 기능

1. 코드의 환경과 순서를 보장
1. FILO 구조이기 때문에 순서를 보장
1. 내부에 쌓인 정보를 통해 환경을 보장 (전역 공간 or 함수 내부의 환경 like closure)
1. (ex) 전역(Global) 컨텍스트: 
    1. JS가 실행되는 순간 콜스택에 담김
    1. browser에서 window.~, node의 경우 global.~를 사용할 수 있는 이유

### 3. 내용

1. **변수 환경(Variable Environment)**:
    1. 그대로 Lexical Environment(문법 환경)에 복사
    1. 내부의 식별자 정보(Environment Record)
    1. 외부 환경 정보(Outer Environment Reference)
1. **문법 환경(Lexical Environment)**:
    1. 변수 환경 복사 후 변경사항이 실시간으로 적용됨
    1. 변수 환경의 초기 상태를 기억
    1. 문법 환경의 최신 상태를 저장
1. **식별자 정보(Environment Record)**:
    1. 현재 컨텍스트와 관련된 식별자와 식별자에 바인딩된 값이 기록됨
    1. 실행컨텍스트 내부 전체를 처음부터 끝까지 확인하며 순서대로 수집
    1. **호이스팅의 원인!!!**
        1. 함수 실행시 실행 컨텍스트가 먼저 생성되므로
        1. Environment Record 즉 식별자 수집이 먼저 되므로
        1. 변수와 같은 식별자를 끌어올리는 것 같다. 는 호이스팅이라는 현상이 생겼습니다.
1. **외부 환경 정보(Outer Environment Reference)**:
    1. 스코프 체인, 클로저를 가능케하는 요소
    1. 현재 함수가 **선언될 당시**의 실시간 문법 환경(Lexical Environment)
1. **ThisBinding**:
    1. 실행 컨텍스트 생성 시, 즉 함수가 호출될 때 할당되는 this의 정보를 담고 있음

# 콜스택과 이벤트 루프

## 1. 콜 스택 (Call Stack)

1. 개념
    1. 함수가 호출되면 실행 컨텍스트가 생성되고 추가되는 공간
    1. 함수 내부에서 호출되는 내부함수들은 콜스택에 추가되어 해당 위치에서 실행됨
    1. 함수의 실행이 종료되면 실행컨텍스트가 제거됨
    1. 중단된 시점부터 재시작
1. 문제점
    ![image](https://github.com/sthgml/DevNote/assets/41767015/5ad5fa43-18c2-4da7-8292-1468e0286dac)
    1. 한 함수가 오래걸리면 브라우저가 멈추어 사용성 측면에서 불편한 서비스가 됨
    1. 하지만 JS가 한 번에 여러 작업을 수행하는 것 처럼 보임

## 2. 이벤트 루프

1. 기능 및 역할
    1. JS는 단일스레드임에도 불구하고 비동기로 작업을 수행할 수 있게 함
    1. Browser 환경임을 전제하에, 이벤트 루프에 기반한 동시성 모델을 가짐
1. 모던 자바스크립트 엔진
    ![image](https://github.com/sthgml/DevNote/assets/41767015/0cadc62e-fd43-44c4-b3b0-7a44c991dad7)
    1. Heap:
        1. 구조화 되지 않은 넓은 메모리 영역
        1. JS의 Reference Type(참조형==객체)는 모두 여기 할당됨
    1. Web API:
        1. **브라우저**에서 제공하는 별도의 API (JS아님)
        1. DOM, SVG, Fetch, Canvas, setTimeout 등
    1. Callback Queue:
        1. 비동기 함수가 실행된 후 콜백함수가 대기하는 공간
    1. MicroTask Queue:
        1. ES6에서 도입된 새로운 컨셉
        1. Callback Queue와 동일한 계층에 존재하며 Promise를 통한 비동기 요청시의 콜백 함수가 대기하는 공간
    1. Animation Frame:
        1. requestAnimationFrame의 콜백함수가 대기하는 공간
    1. Event Loop:
        1. Call Stack과 각 작업 Queue들을 감시
        1. Call Stack이 비었을 경우 우선순위에 따라 Queue에서 하나씩 꺼내 CallStack에 추가
        1. 비동기 함수의 콜백 함수 처리 순서: (크롬 브라우저 엔진(V8)기준)
            1. MicroTask (Promise)
            1. Animation Frame
            1. Callback
        1. (ex)

        ```js
        console.log('start'); // (0)
        setTimeout(() => {console.log('setTimeout')}, 0); // Callback (3)
        requestAnimationFrame(()=>{console.log('rAF')}) // Animation Frame (2)
        Promise.resolve().then(()=>{console.log('promise')}) // MicroTask (1)
        console.log('end'); // (0-1)
        ```

        실행 결과는 => 'start' - 'end' - 'promise' - 'rAF' - 'setTimeout'

    1. Zero Delay
        1. setTimeout(()=>{}, 0) 을 하더라도 0ms 이후에 실행되는게 아님
        1. setTimeout 내의 콜백함수는 가장 우선순위가 낮기 때문에 다른 비동기 콜백함수들이 모두 실행되고 난 뒤에 실행됨
        1. 따라서 지연시간은 실행이 보장되는 시간이 아닌 최소 시간임을 알 수 있습니다.
