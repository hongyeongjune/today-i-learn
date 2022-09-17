### useEffect 첫 렌더링 시 두번 실행되는 이유

#### 사건 발생
useEffect 사용 시 처음 렌더링 때 두번 실행이 되었다.
```javascript
function Example() {
    useEffect(() => {
        console.log("example");
    }, []);
    return (
        <div></div>
    );
}
```

#### 왜 두번 실행될까?
원인은 App.js에서 찾을 수 있는데, 기본적으로 create-react-app을 통해서 리액트 프로젝트를 생성하게 되면 처음 구조는 다음과 같다.
```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
```

> https://ko.reactjs.org/docs/strict-mode.html

위의 공식문서를 살펴보면, StrictMode를 사용할 시 개발모드에서 앱의 문제가 있나 확인하기 위해 처음 시작할 때 렌더링이 두번 발생하게 된다.
따라서 해당 부분을 제거하면 정상적으로 첫 렌더링 시 한번만 실행된다.
