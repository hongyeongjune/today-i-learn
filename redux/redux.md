#### 참조
> https://ko.redux.js.org/introduction/getting-started/

### Redux
* 상태 관리 라이브러리
* 컴포넌트의 상태 업데이트 관련 로직을 다른 파일로 분리시켜 효율적으로 관리할 수 있다.

### createStore(reducer, [preloadedState], [enhancer]))
* 앱의 상태 트리 전체를 보관하는 리덕스 저장소를 생성하는 메서드
* 앱 내에는 단 하나의 저장소만이 있어야 한다.
* reducer (function)
  * 주어진 현재 상태 트리와 액션에서 다음 상태 트리를 반화하는 리듀싱 함수
  * ```type Reducer<S, A> = (state: S, action: A) => S```
  * 리듀서는 누적값과 값을 받아서 새로운 누적값을 반환하는 함수
  * 리덕스만의 개념은 아니다.
```javascript
import { createStore } from 'redux';

const reducer = (state = 0, actions) => {
    switch (actions.type) {
        case ADD:
            return state + 1;
        case MINUS:
            return state - 1;
        default:
            return state;
    }
};

const store = createStore(reducer);

store.dispatch({
    type: 'ADD'
});
store.dispatch({
    type: 'MINUS'
});
```
### Store
* 앱의 전체 상태 트리를 가지고 있는 저장소
* getState()
  * 애플리케이션의 현재 상태 트리를 반환한다.
  * 저장소의 리듀서가 마지막으로 반환한 값과 동일
* dispatch(action)
  * 상태를 변경하기 위한 유일한 방법
* subscribe(listener)
  * 상태 트리의 일부가 변경될 때마다 호출된다.