### Redux Toolkit 이란?
> https://redux-toolkit.js.org/introduction/getting-started#purpose

* 리덕스 툴킷 패키지는 리덕스 로직을 작성하는 표준 방식으로 만들어졌다.
* 리덕스 툴킷은 세가지 문제점을 해결하기 위해 만들어졌다.
  * 리덕스 스토어를 구성하는 것이 너무 복잡하다.
  * 리덕스를 사용하기에 너무 많은 패키지를 설치해야한다.
  * 리덕스는 지나치게 많은 보일러 플레이트 코드가 필요하다.

### 구성 요소
> https://redux-toolkit.js.org/introduction/getting-started#whats-included

#### configureStore()
> https://redux-toolkit.js.org/api/configureStore
* 단순화된 구성 옵션과 편하게 기본값을 제공하기 위해 createStore를 감싼다.
* 이는 자동으로 slice reducers와 결합할 수 있고, 제공하는 리덕스 미들웨어가 무엇이든 간에, redux-thunk를 디폴트로 제공한다.
* redux devtools extension을 사용할 수 있다.
* Parameters
  * 다음 옵션과 함께 하나의 단일 객체 파라미터를 받는다.
  ```javascript
    type ConfigureEnhancersCallback = (
      defaultEnhancers: StoreEnhancer[]
    ) => StoreEnhancer[]

    interface ConfigureStoreOptions<
      S = any,
      A extends Action = AnyAction,
      M extends Middlewares<S> = Middlewares<S>
    > {
      reducer: Reducer<S, A> | ReducersMapObject<S, A>

      middleware?: ((getDefaultMiddleware: CurriedGetDefaultMiddleware<S>) => M) | M

      devTools?: boolean | DevToolsOptions
  
      preloadedState?: DeepPartial<S extends any ? S : S>

      enhancers?: StoreEnhancer[] | ConfigureEnhancersCallback
    }

    function configureStore<S = any, A extends Action = AnyAction>(
      options: ConfigureStoreOptions<S, A>
    ): EnhancedStore<S, A>
  ```
  
#### createReducer()
> https://redux-toolkit.js.org/api/createReducer
* switch문으로 액션 타입의 목록을 작성하는 대신, case reducer functions들로 액션타입의 목록을 제공한다.
```javascript
const counterReducer = createReducer(0, {
  increment: (state, action) => state + action.payload,
  decrement: (state, action) => state - action.payload,
})
```
```javascript
import { createAction, createReducer } from '@reduxjs/toolkit'

const increment = createAction('counter/increment')
const decrement = createAction('counter/decrement')
const incrementByAmount = createAction('counter/incrementByAmount')

const initialState = { value: 0 }

const counterReducer = createReducer(initialState, (builder) => {
  builder
    .addCase(increment, (state, action) => {
      state.value++
    })
    .addCase(decrement, (state, action) => {
      state.value--
    })
    .addCase(incrementByAmount, (state, action) => {
      state.value += action.payload
    })
```

#### createAction()
> https://redux-toolkit.js.org/api/createAction
* 주어진 액션 타입 문자열에 대한 action creator 함수를 생성한다.
```javascript
function createAction(type, prepareAction?)
```
```javascript
import { createAction } from '@reduxjs/toolkit'

const increment = createAction('counter/increment')

let action = increment()
// { type: 'counter/increment' }

action = increment(3)
// returns { type: 'counter/increment', payload: 3 }

console.log(increment.toString())
// 'counter/increment'

console.log(`The action type is: ${increment}`)
// 'The action type is: counter/increment'
```

#### createSlice()
> https://redux-toolkit.js.org/api/createSlice
* reducer 함수의 객체, slice name, 초기값을 받아들여서 action creators, action types를 가진 slice reducer를 자동으로 생성한다.
```javascript
import { createSlice } from '@reduxjs/toolkit'

const initialState = { value: 0 }

const counterSlice = createSlice({
  name: 'counter',
  initialState,
  reducers: {
    increment(state) {
      state.value++
    },
    decrement(state) {
      state.value--
    },
    incrementByAmount(state, action) {
      state.value += action.payload
    },
  },
})

export const { increment, decrement, incrementByAmount } = counterSlice.actions
export default counterSlice.reducer
```
* Parameter
  * name : 이름
  * initialState : 초기값
  * reducers : 리듀서들
  ```javascript
    function createSlice({
      // A name, used in action types
      name: string,
      // The initial state for the reducer
      initialState: any,
      // An object of "case reducers". Key names will be used to generate actions.
      reducers: Object<string, ReducerFunction | ReducerAndPrepareObject>
      // A "builder callback" function used to add more reducers, or
      // an additional object of "case reducers", where the keys should be other
      // action types
      extraReducers?:
      | Object<string, ReducerFunction>
      | ((builder: ActionReducerMapBuilder<State>) => void)
    })
  ```