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

#### 상세 설명
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
deps 부분에 아무것도 들어있지 않기 때문에 처음 실행 이후로 더이상 실행하지 않습니다.  
두번째 useEffect에서는 deps 부분에 keyword가 들어간 것을 확인할 수 있는데, 이 것은 keyword의 값에 변화가 일어날 때마다 해당 부분을 리렌더링하라는 의미이다.  

