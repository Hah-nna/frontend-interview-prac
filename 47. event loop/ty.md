# event loop 란?

자바스크립트는 싱글 스레드 언어 이다. 한번에 하나의 작업만 수행이 가능한 것이다.
웹 애플리케이션에서는 네트워크 요청이나 이벤트 처리, 타이머와 같은 작업을 멀티로 처리해야 하는 경우가 많다.
오래 걸리고 반복적인 작업들은 자바스크립트 엔진이 아닌 브라우저 내부의 멀티 스레드인 Web APIs에서 비동기 + 논블로킹으로 처리된다.

> 즉, 비동기로 동작하는 핵심요소는 자바스크립트 언어가 아니라 브라우저라는 소프트웨어가 가지고 있다고 보면 된다. Node.js 에서는 libuv 내장 라이브러리가 처리한다.

**싱글 스레드인 자바스크립트의 작업을 멀티 스레드로 돌려 작업을 동시에 처리시키게 하던가, 또는 여러 작업 중 어떤 작업을 우선으로 동작시킬 것인지 결정하는 세심한 컨트롤을 하기 위해 존재하는 것이 바로 이벤트 루프(Event Loop) 이다.**

**이벤트 루프는 브라우저 내부의 Call Stack, Callback Queue, Web APIs 등의 요소들을 모니터링하면서 비동기적으로 실행되는 작업들을 관리하고, 이를 순서대로 처리하여 프로그램의 실행 흐름을 제어한다.(브라우저의 동작 타이밍을 제어하는 관리자)**

<img src="data:image/png;base64,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">

### 1. js Engine

자바스크립트 엔진은 코드를 이해하고 실행을 도와주는 역할을 한다. (대표적인 엔진으로 google V8 이다.)
각 브라우저 별로 여러 가지 엔진들이 존재하지만 js엔진은 크게 memory heap 과 call stack으로 이루어져 있다,

- Memory Heap : 메모리 할당이 일어나는 장소
- Call Stack : 코드가 실행될 경우 하나씩 stack의 형태로 쌓이는 장소

### 2. Memory Heap & Call stack

먼저 Memory Heap에 있는 사용자가 작성한 코드들은 Call Stack에서 Stack 방식으로 쌓이며 코드를 실행하게 되는데 이때 동기 함수들은 그대로 실행하게 되고 비동기 함수들은 Web API로 처리하게 되며 일을 분배 한다.
`Stack : 후입선출(LIFO)로 마지막에 들어간 것이 먼저 나가는 방식`

### 3. Web API

Javscript를 사용하면서 우리가 많이 사용하는 API 들은 사실 JavaScript에서 지원하는 것이 아닌 웹 브라우저에서 제공하는 API로 DOM ,AJAX, Timeout 등이 있다.

Call Stack에서 실행된 비동기 함수는 Web API에서 처리를 하게 되고 그동안에 Call Stack은 나머지 동기 함수들을 처리하게 된다

Web API는 비동기 함수들을 처리하며 작업이 완료된 비동기 함수들을 Callback Queue로 넘겨주게 된다.

### callback Queue

Callback Queue는 비동기 함수들을 보관하는 장소로 Event Loop에서 비동기 함수를 꺼내기 전까지는 계속 Queue방식으로 보관하게 된다.
`Queue : 선입선출(FIFO)로 먼저 들어간 것이 먼저 나가는 방식`

### event Loop

Event Loop는 Call Stack과 Callback Queue를 상태를 계속 감시하며 Call Stack에 함수들이 존재하지 않는다면 Callback Queue에 있는 비동기 함수들을 Call Stack에 밀어 넣게 됩니다. 그 후 Call Stack에서 비동기 함수를 실행시키게 된다.

https://blog.toycrane.xyz/%EC%A7%84%EC%A7%9C-%EC%89%BD%EA%B2%8C-%EC%95%8C%EC%95%84%EB%B3%B4%EB%8A%94-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-c7fbdc44cc97