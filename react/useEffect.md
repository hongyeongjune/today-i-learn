#### 참조 (공식문서)
* https://ko.reactjs.org/docs/hooks-effect.html

### useEffect

#### useEffect 역할
* 컴포넌트가 렌더링 될 때 특정 작업을 실행할 수 있도록 하는 Hook
* React는 우리가 넘긴 함수를 기억했다가 DOM 업데이트를 수행한 이후에 불러낸다.

#### useEffect를 컴포넌트 안에서 불러내는 이유
* useEffect를 컴포넌트 내부에 둠으로써 effect를 통해 state 변수에 접근할 수 있게 된다.
* 함수 범위 안에 존재하기 때문에 특별한 API 없이도 값을 얻을 수 있다.

#### useEffect 렌더링 이후 매번 수행?
첫 번째 렌더링과 이후의 모든 업데이트에서 수행된다.

#### 예시
```javascript
function App() {
    const [keyword, setKeyword] = useState("");

    useEffect(() => {
        console.log("I run only once.");
    }, []);
    useEffect(() => {
        if (keyword !== "") {
            console.log("I run when 'keyword' changes.");
        }
    }, [keyword]);

    return (
        <div>
            <input
                value={keyword}
                onChange={e => setKeyword(e.target.value)}
                placeholder={"search here..."}
                type="text"
            />
        </div>
    );
}
```

위의 코드에서 "I run only once."는 첫 번째 렌더링될 때 한번만 실행된다.  
deps 부분에 아무것도 들어있지 않고 더이상 리렌더링될 일이 없기 때문에 처음 실행 이후로 더이상 실행하지 않습니다.  
두번째 useEffect에서는 deps 부분에 keyword가 들어간 것을 확인할 수 있는데, 이 것은 keyword의 값에 변화가 일어날 때마다 해당 부분을 리렌더링하라는 의미이다.  

#### clean up
* useEffect() 함수에서 return 함수
* 컴포넌트의 unmount 이전, update 직전에 어떤 작업을 수행하고 싶을 때 사용

#### 예시
```javascript
function Hello() {
    useEffect(() => {
        console.log("created");
        return () => console.log("destoryed");
    }, []);

    return <h3>Hello</h3>;
}

function App() {
    const [showing, setShowing] = useState(false);

    return (
        <div>
            {
                showing && <Hello />
            }
            <button onClick={() => setShowing(prev => !prev)}>{showing ? "Hide" : "Show"}</button>
        </div>
    );
}
```

위의 코드에서 첫 렌더링때 showing이 false이기 때문에 콘솔에 아무것도 찍히지 않는다.  
버튼을 한번 클릭하면 "created"라는 콘솔이 출력될 것이다.  
마운트과정에서 useEffect 함수가 실행되기 때문이다.  
버튼을 다시 한번 클릭하면 언마운트가 진행되고 클린업 함수에 의해 "destroyed" 메세지가 콘솔에 출력될 것 이다.