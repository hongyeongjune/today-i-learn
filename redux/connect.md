#### 참조
> https://react-redux.js.org/api/connect
> https://react-redux.js.org/using-react-redux/connect-mapstate
> https://react-redux.js.org/using-react-redux/connect-mapdispatch

### connect()
* connect() 함수는 리액트 컴포넌트와 리덕스 스토어를 연결해준다.
```javascript
function connect(mapStateToProps?, mapDispatchToProps?, mergeProps?, options?)
```

### mapStateToProps
* mapStateToProps가 지정되면, 리덕스 저장소 업데이트를 구독한다.
* 즉, 스토어가 업데이트될 떄마다 호출된다.
* 즉, 스토어가 업데이트되면 해당 값이 변경되고 리액트 컴포넌트에서 변경된 부분이 리렌더링된다.
* mapStateToProps 함수는 두 개의 매개변수를 사용한다.
  * state: object
  * ownProps?: object
* state
  * 스토어 상태가 변경될 때마다 호출되고 스토어 상태가 유일한 매개변수로 제공
  ```javascript
  // TodoList.js
  function mapStateToProps(state) {
      const { todos } = state
      return { todoList: todos.allIds }
  }
  export default connect(mapStateToProps)(TodoList)
  ```
* ownProps
  * ownProps의 인자들은 커넥트에 의해 생성된 컴포넌트의 모든 props를 포함한다.
  ```javascript
  // Todo.js
  function mapStateToProps(state, ownProps) {
      const { visibilityFilter } = state
      // ownProps would look like { "id" : 123 }
      const { id } = ownProps
      const todo = getTodoById(state, id)

      // component receives additionally:
      return { todo, visibilityFilter }
  }
  ```

#### mapStateToProps의 반환값은 컴포넌트가 리렌더링할 지 결정한다.
* React-Redux는 ```shouldComponentUpdate``` 메서드를 내부적으로 구현하여, 컴포넌트가 필요한 데이터가 변경될 때 wrapper component가 리렌더링되도록 한다.
* 기본적으로, ```mapStateToProps```에서 리턴 받은 객체의 내용이 달라졌는지 판단하기 위해 각 필드에 ```===``` 비교(shallow equality)를 사용한다. 

#### 새 객체 반환
* React-Redux는 mapStateToProps의 결과값 변경 여부를 확인하기 위해 ```===```을 한다.
* 새로운 객체 혹은 새로우 배열 참조 값을 리턴하면 데이터 내용이 같더라도 리렌더링을 유발한다.
* new object or array reference를 생성하는 동작
  * .map(), .filter() 로 새로운 배열 생성
  * .concat 으로 배열 합치기
  * .slice 으로 부분 배열 선택
  * .assign 으로 값 복사
  * spread operator {...oldState, ...newDate}로 값 복사0
* memoized selector 함수를 사용하고 value가 변경되지 않으면 리렌더링이 일어나지 않아서 이걸 사용하도록 권장

### mapDispatchToProps
* connect 에 두번째 파라미터로 사용된다.
* store에 액션을 디스패치하는데 사용된다.
* action은 store의 상태를 변경하는 유일한 방법이다.
* React-Redux를 사용하면 컴포넌트는 store에 직접적으로 접근할 수 없다. -> React-Redux는 액션을 디스패치하는 방법을 2가지 제공한다.
  * 기본적으로 연결된 구성요소는 props.dispatch 작업 자체를 수신하고 전달할 수 있다.
  ```javascript
  function Counter({ count, dispatch }) {
      return (
          <div>
            <button onClick={() => dispatch({ type: 'DECREMENT' })}>-</button>
            <span>{count}</span>
            <button onClick={() => dispatch({ type: 'INCREMENT' })}>+</button>
            <button onClick={() => dispatch({ type: 'RESET' })}>reset</button>
          </div>
      )
  }
  ```
  * connect()를 호출할 때 mapDispatchToProps 함수를 만들어서 전달할 수 있다.
  ```javascript
  // button needs to be aware of "dispatch"
  <button onClick={() => dispatch({ type: "SOMETHING" })} />

  // button unaware of "dispatch",
  <button onClick={doSomething} />
  ```