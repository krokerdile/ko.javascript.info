
# 전역 객체

전역 객체는 어디에서나 사용 가능한 변수나 함수로, 대게 언어 자체나 실행 환경에 기본적으로 내장되어 있습니다.

전역 객체를 브라우저 환경에선 `window`, Node.js 환경에선 `global`라고 부릅니다. 이 외의 환경에선 다른 이름을 가질 수 있습니다.

최근엔 전역 객체의 표준화 된 이름인 `globalThis`가 자바스크립에 추가되었습니다. 자바스크립트가 돌아가는 모든 환경에서 이를 지원해야 하죠. Edge나 IE, Opera같은 브라우저에선 아직 `globalThis`를 지원하진 않지만, 이에 대한 폴리필(polyfill)을 쉽게 찾을 수 있습니다.

We'll use `window` here, assuming that our environment is a browser. If your script may run in other environments, it's better to use `globalThis` instead.

All properties of the global object can be accessed directly:

```js run
alert("Hello");
// 위와 동일하게 동작합니다.
window.alert("Hello");
```

브라우저에서 (`let`이나 `const`가 아닌) `var`로 선언한 전역 함수나 전역 변수는 전역 객체의 프로퍼티가 됩니다.

```js run untrusted refresh
var gVar = 5;

alert(window.gVar); // 5 (전역 객체 window의 프로퍼티)
```

하지만 이런 방식으로 전역 객체를 사용하는 것은 좋지 않습니다. 하위 호환성 때문에 아직까지 동작은 하지만요. 모듈을 사용하는 모던 스크립트에선 이런 일이 일어나지 않습니다. 이에 대해선 [자바스크립트 모듈](info:modules)에서 더 이야기해 보도록 하겠습니다.

If we used `let` instead, such thing wouldn't happen:

```js run untrusted refresh
let gLet = 5;

alert(window.gLet); // undefined (전역 객체의 프로퍼티가 되지 않습니다.)
```

중요한 변수라서 전역 공간에서 모두 참조할 수 있게 하려면, 아래와 같이 프로퍼티를 직접 추가해 주시기 바랍니다.

```js run
*!*
// 현재 사용자(current User)에 대한 정보를 전역 객체에 추가했기 때문에, 모든 스크립트에서 접근할 수 있게 되었습니다.
window.currentUser = {
  name: "John"
};
*/!*

// 코드 어딘가에서 참조할 수 있습니다.
alert(currentUser.name);  // John

// 또는 "currentUser"라는 이름을 가진 지역 변수가 있다면
// 전역 객체 window에서 이를 명시적으로 가져올 수 있습니다. 안전한 방법으로 말이죠!
alert(window.currentUser.name); // John
```

That said, using global variables is generally discouraged. There should be as few global variables as possible. The code design where a function gets "input" variables and produces certain "outcome" is clearer, less prone to errors and easier to test than if it uses outer or global variables.

## 폴리필 사용하기

전역 객체를 이용하면 현재 사용중인 브라우저가 최신 자바스크립트 기능을 지원하는지 여부를 확인할 수 있습니다.

예를 들어, 내장 객체인 `Promise`를 지원하는지 여부를 아래와 같이 테스트할 수 있습니다. 오래된 브라우저를 사용 중이라면 `Promise`객체를 지원하지 않기 때문에 `alert` 창이 뜰 겁니다.
```js run
if (!window.Promise) {
  alert("오래된 브라우저를 사용 중이시군요!");
}
```

최신 명세에는 있지만 오래된 브라우저를 사용하고 있어서 해당 기능을 지원하지 않는다면, 직접 "폴리필"을 만들 수 있습니다. 함수를 만들고 전역 객체에 추가하면 되죠.

```js run
if (!window.Promise) {
  window.Promise = ... // 모던 언어에서 지원하는 기능을 직접 구현
}
```

## 요약

- 전역 객체는 어디서든 접근 가능한 변수를 담고 있습니다.

    전역 객체를 통하면 `Array`, 브라우저 환경에서 현재 창의 높이를 가져올 수 있게 해주는 `window.innerHeight`, 등의 자바스크립트 내장 기능을 사용할 수 있습니다.
- 전역 객체는 `globalThis`라는 보편적인 이름으로 참조할 수 있습니다.

    하지만 관습에 따라 브라우저 환경에선 `window`, Node.js에선 `global`이라는 이름으로 전역 객체를 참조할 때가 많습니다. `globalThis`는 제안된지 얼마 안된 스펙이기 때문에, 모든 브라우저에서 지원하지 않습니다. 따라서 폴리필을 구현해 이를 사용해야 합니다.
- 프로젝트 전역에서 쓰이는 변수일 때만 전역 객체에 변수를 저장하도록 합시다. 전역 변수는 가능한 한 최소한으로 사용합시다.
- [모듈](info:modules)을 사용하고 있지 않다면, 브라우저 환경에서 `var`로 선언한 전역 변수는 전역 객체의 프로퍼티가 됩니다.
- 해석이 용이하고 요구사항 변경에 쉽게 대응할 수 있는 코드를 구현하려면, 전역 객체의 프로퍼티는 직접 접근해야 합니다. `window.x`처럼 말이죠.
