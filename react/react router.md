### React Router
> https://reactrouter.com/en/main/start/overview

* 리액트 라우터는 클라이언트 사이드 라우팅이다.
* 기존 웹사이트에서 브라우저는 웹 서버에 문서를 요청하고 CSS 및 자바스크립트를 다운로드 받은 후 서버에서 보낸 HTML을 렌더링한다.
* 사용자가 해당 페이지를 열면 새 페이지에 대한 프로세스를 다시 시작한다.

* 클라이언트 사이드 라우팅을 사용하면, 서버에서 다른 문서를 요청하지 않고도 URL을 업데이트할 수 있다.
* 즉, 브라우저가 완전히 새로운 문서를 요청하거나 다음 페이지에 대한 CSS 및 자바스크립트를 다운로드 할 필요가 없기 때문에 속도가 훨씬 빨라진다.

#### Browser Router
> https://reactrouter.com/en/main/router-components/browser-router
> https://devdocs.io/react_router/index#browserrouter

* 브라우저 라우터는 라우터 역할을 담당한다.
* URL과 UI를 동기화해주는 라이브러리, 웹 애플리케이션이 History API를 사용하여 페이지를 새로고침 없이 주소 변경이 가능하게 한다.

```javascript
import * as React from "react";
import * as ReactDOM from "react-dom";
import { BrowserRouter } from "react-router-dom";

ReactDOM.render(
  <BrowserRouter>
    {/* The rest of your app goes here */}
  </BrowserRouter>,
  root
);
```

#### Switch
> https://v5.reactrouter.com/web/api/Switch

* Route로 생성된 자식 컴포넌트 중 매칭되는 첫 번째 Route를 렌더링한다.
* 만약 Switch를 사용하지 않고 "/", "/home", "/hello"를 사용한다면, "/"가 포함된 "/home", "hello" 컴포넌트들이 다 렌더링된다.

#### Route
> https://reactrouter.com/en/main/route/route
> https://devdocs.io/react_router/index#routes-and-route

* 컴포넌트에 path 속성을 이용하여 원하는 url을 지정한다.
* 해당 url에 접속하면 해당 url에 일치하는 컴포넌트만 렌더링된다.

```javascript
<Routes>
  <Route path="/" element={<Dashboard />}>
    <Route
      path="messages"
      element={<DashboardMessages />}
    />
    <Route path="tasks" element={<DashboardTasks />} />
  </Route>
  <Route path="about" element={<AboutPage />} />
</Routes>
```

#### useParams
> https://devdocs.io/react_router/index#useparams

* Route path에 의해 매치된 현재 URL의 동적 파라미터 값을 꺼내올 때 사용한다.
```javascript
import * as React from 'react';
import { Routes, Route, useParams } from 'react-router-dom';

function ProfilePage() {
  // Get the userId param from the URL.
  let { userId } = useParams();
  // ...
}

function App() {
  return (
    <Routes>
      <Route path="users">
        <Route path=":userId" element={<ProfilePage />} />
        <Route path="me" element={...} />
      </Route>
    </Routes>
  );
}
```

### nestedRouter
> https://reactrouter.com/en/v6.3.0/getting-started/overview#nested-routes

* 부모 Router 안에 자식 Router를 중첩해서 사용할 수 있다.
* 전체가 렌더링되는게 아니라 path에 따라 필요한 부분만 렌더링이 되는 아주 강력한 기능이다.

* 아래 예시와 같이 Router를 중첩으로 사용한 다음에,
```javascript
function App() {
  return (
    <Routes>
      <Route path="invoices" element={<Invoices />}>
        <Route path=":invoiceId" element={<Invoice />} />
        <Route path="sent" element={<SentInvoices />} />
      </Route>
    </Routes>
  );
}
```

* Outlet 이라는 키워드를 사용해서 어느 위치에 렌더링할 지 결정할 수 있다.
```javascript
function Invoices() {
  return (
    <div>
      <h1>Invoices</h1>
      <Outlet />
    </div>
  );
}

function Invoice() {
  let { invoiceId } = useParams();
  return <h1>Invoice {invoiceId}</h1>;
}

function SentInvoices() {
  return <h1>Sent Invoices</h1>;
}
```

### useMatch
> https://reactrouter.com/en/v6.3.0/api#usematch

* 현재 경로를 반환하는 메서드

* declaration
```javascript
declare function useMatch<ParamKey extends string = string>(
  pattern: PathPattern | string
): PathMatch<ParamKey> | null;
```