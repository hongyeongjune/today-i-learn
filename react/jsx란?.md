#### 참조 (공식문서)
* 자세한 내용은 문서를 참고하자
* https://ko.reactjs.org/docs/introducing-jsx.html

### JSX란?
* Javascript를 확장한 문법
* JSX로 작성된 코드는 브라우저에서 실행되기 전에 코드가 번들링되는 과정에서 바벨을 사용하여 일반 자바스크립트 형태의 코드로 변환한다.
* Javscript 코드안에서 UI 관련 작업 (ex. html)을 할 때 시각적으로 도움이되는 것이 장점

### 비교

비교는 아래 링크를 통해 직접해볼 수 있다.  
https://babeljs.io/repl#?browsers=defaults%2C%20not%20ie%2011%2C%20not%20ie_mob%2011&build=&builtIns=false&corejs=3.21&spec=false&loose=false&code_lz=Q&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=env%2Creact%2Cstage-2&prettier=false&targets=&version=7.18.13&externalPlugins=&assumptions=%7B%7D

#### AS-IS
```javascript
const h3 = React.createElement("h3", {
    onMouseEnter: () => console.log('enter')
  }, "Hello, I am a H3");
  const button = React.createElement("button", {
    onClick: () => console.log('click')
  }, "Click me");
```
#### TO-BE
```javascript
"use strict";

const h3 = React.createElement("h3", {
  onMouseEnter: () => console.log('enter')
}, "Hello, I am a H3");
const button = React.createElement("button", {
  onClick: () => console.log('click')
}, "Click me");
```