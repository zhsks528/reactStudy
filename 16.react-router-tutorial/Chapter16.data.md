## 16.1 SPA란?

**SPA**는 **Single Page Application**, 즉 **싱글 페이지 애플리케이셔의 약어** 이다.

말 그대로 페이지가 한 개인 애플리케이션이라는 의미

> 전통적인 페이지는 여러 페이지로 구성



**SPA 단점**

앱 규모가 커지면 자바스크립트 파일 크기도 너무 커진다.

**이유 =>** 페이지를 로딩 할 때, 유저가 실제로 방문하지 않을 수도 있는 페이지와 관련된 컴포넌트 코드도 함께 불러오기 때문이다.

**해결 =>** **코드 스플리팅(code splitting)** 기술을 사용하면 트래픽과 로딩 속도를 개선할 수 있다.



## 16.2 프로젝트 구성

### 16.2.1 프로젝트 생성 및 라이브러리 설치

1. 프로젝트를 생성한다

```
$ create-react-app react-router-tutorial
```

2. 해당 디렉토리로 이동한다.

```
$ cd react-router-tutorial
```

3. 해당 디렉토리로 이동 후, `react-router-dom` 을 설치한다.

```
$ yarn add react-router-dom
```



### 16.2.2 프로젝트 초기화 및 구조 설정

다음 파일을 제거

- src/App.css
- src/App.test.js
- src/logo.svg



다음 디렉토리 생성

- src/components : 컴포넌트들이 위치하는 디렉토리
- src/pages : 각 **라우터**들이 위치하는 디렉토리



### 16.2.3 NODE_PATH 설정

디렉토리 구조가 깊어질수록 ../../../../../components/MyComponent 이런 식으로 길어지고 헷갈릴 수 있다.

이러한 문제는 프로젝트의 루트 경로를 지정하여 파일을 **절대 경로**로 불러오면 쉽게 해결할 수 있다.



**package.json** 파일을 열어 scripts의 start와 build를 다음과 같이 수정한다.

```
...
"scripts" : {
    "start" : "NODE_PATH=src react-scripts start",
    "build" : "NODE_PATH=src react-scripts build",
    ...
}
...
```



**-------Windows 사용자 필독--------**



**cross-env**를 설치한다.

```
$ yarn add cross-env
```



**package.json** 파일을 열어 scripts의 start와 build를 다음과 같이 수정한다.

```
...
"scripts" : {
    "start" : "cross-env NODE_PATH=src react-scripts start",
    "build" : "cross-env NODE_PATH=src react-scripts build",
    ...
}
...
```



### 16.2.4 컴포넌트 준비

**BrowserRouter**는 HTML5의 history API를 사용하여 새로고침하지 않고도 페이지 주소를 교체할 수 있다.

예)

```
import React from 'react';
import { BrowserRouter } from 'react-router-dom';
import App from './App';

const Root = () => {
    return(
        <BrowserRouter>
            <App />
        </BrowserRouter>
    )
}

export default Root;
```



index.js에서 App이 아닌 Root를 렌더링하도록 수정

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import Root from './Root';
import * as serviceWorker from './serviceWorker';

ReactDOM.render(<Root />, document.getElementById('root'));

serviceWorker.unregister();
```



## 16.3 Route와 파라미터

### 16.3.1 기본 라우트 준비



### 16.3.2 라우트 설정

페이지 주소를 설정할 떄는 **Route 컴포넌트**를 사용한다,

`import { Route } from 'react-router-dom'`

**Route 컴포넌트**에서 경로는 `path` 값으로 설정하고, 보여 줄 컴포넌트는 `component` 값으로 설정한다.

`exact` 값은 주소가 여기에서 **설정한 path와 정확히 일치할 때만** 보이도록 설정한다.

예.

```
import React from 'react';
import { Route } from 'react-router-dom';
import { Home, About } from 'pages';

const App = () => {
  return (
    <div>
      <Menu/>
      <Route exact path="/" component= {Home}/>
      <Route path="/about" component={About}/>
    </div>
  );
};

export default App;

```



### 16.3.3 라우트 파라미터와 쿼리 읽기

라우트의 경로에 특정 값을 넣는 방법은 **2가지**가 있다.



#### 16.3.3.1 params

예 1-1) /: <> 을 추가

```
import React from 'react';
import { Route } from 'react-router-dom';
import { Home, About } from 'pages';

const App = () => {
	return(
		<Route exact path="/" component={Home}/>
    	<Route path="/about" component={About}/>
    	<Route path="/about/:name" component={About}/>
	);
};

export default App;
```



예 1-2) /: <> ?  **?를 추가**

```
import React from 'react';
import { Route } from 'react-router-dom';
import { Home, About } from 'pages';

const App = () => {
	return(
		<Route exact path="/" component={Home}/>
    	<Route path="/about/:name?>" component={About}/>
	);
};

export default App;
```





#### 16.3.3.2 Query String

**Query String**은 URL 뒤에 /about/something?**key=value&anotherKey=value** 형식으로 들어가는 정보

이 문자열로 된 쿼리를 객체 형태로 파싱하려면 query-string 라이브러리를 설치해야 한다.

**Query String은 라우트 내부에서 정의한다.**



src/pages/About.js 코드부분 수정

```
import React from 'react';
import queryString from 'query-string';

const About = ({location, match}) => {
	const query = queryString.parse(location.search);
	console.log(query);
	
	(...)
}
```



+심화내용 (queryString을 받아서 적용하기)

	import React from 'react';
	import queryString from 'query-string';
	
	const About = ({location, match}) => {
		const query = queryString.parse(location.search);
		const { color } = query;
		
		return(
		<div>
			<h2 style={ {color} }>소개</h2>
			<p>
				안녕하세요. 저는 {match.params.name} 입니다.
			</p>
		</div>
		);
	};
	
	export default About;



## 16.4 라우트 이동

### 16.4.1 Link 컴포넌트

애플리케이션 안에서 다른 라우트로 이동할 때는, `<a href="">Link</a>`형식으로 하면 안된다.

**이유 =>** 새로고침하면서 로딩을 하기 때문이다.

**해결 =>** 리액트 라우터에 있는 **Link 컴포넌트**를 사용해야 한다.



src/components/Menu.js

```
import React from 'react';
import { Link } from 'react-router-dom';

const Menu = () =>{
    return(
        <div>
            <ul>
                <li><Link to="/">홈</Link></li>
                <li><Link to="/about">소개</Link></li>
                <li><Link to="/about/react">React 소개</Link></li>
            </ul>
        </div>
    )
}

export default Menu;
```

- Link 컴포넌트는 react-router-dom에서 불러온다.
- 이동할 주소를 컴포넌트의 to 값으로 지정한다.



App에서 컴포넌트 불러와 렌더링

```
import React from 'react';
import { Route } from 'react-router-dom';
import { Home, About } from 'pages';
import Menu from 'components/Menu';

const App = () => {
    return(
    	<Menu />
        <Route exact path="/" component={Home}/>
        <Route path="/about/:name?>" component={About}/>
    );
};

export default App;
```



### 16.4.2 NavLink 컴포넌트

**NavLink 컴포넌트**는 Link 컴포넌트와 비슷하지만, 추가 기능이 있다.

**현재 주소와 해당컴포넌트의 목적지 주소가 일치한다면 특정 스타일로 또는 클래스를 지정할 수 있다.**

```
import React from 'react';
import { NavLink, withRouter } from 'react-router-dom';

const Menu = () =>{
    const activeStyle = {
        color : 'green',
        fontSize : '2rem'
    }
    return(
        <div>
            <ul>
                <li>
                	<NavLink exact to="/" activeStyle={activeStyle}>
                		홈
                	</NavLink>
                </li>
                <li>
                	<NavLink exact to="/about" activeStyle={activeStyle}>
                		소개
                	</NavLink>
                </li>
                <li>
                	<NavLink to="/about/react" activeStyle={activeStyle}>
                		React 소개
                	</NavLink>
                </li>
                <li>
                	<NavLink to="/posts" activeStyle={activeStyle}>
                		포스트 목록
                	</NavLink>
                </li>
            </ul>
        </div>
    )
}

export default withRouter(Menu);
```



### 16.4.3  자바스크립트에서 라우팅

자바스크립트로 라우팅을 하려면 라우트로 사용된 컴포넌트가 받아오는 **props** 중 하나인 **history** 객체의 **push** 함수를 활용해야한다.



src/pages/Home.js

```
import React from 'react';

const Home = ({history}) => {
    return(
        <div>
            <h2>홈</h2>
            <button onClick={()=>{
                history.push('/about/javascript')
            }}>자바스크립트를 사용하여 이동</button>
        </div>
    );
};


export default Home;
```



### 16.5 라우트 안의 라우트

라우트안에 또 다른 라우트를 정의하는 방법



1. src/pages/Post.js 생성 및 params.id 값을 렌더링

   ```
   import React from 'react';
   
   const Post = ({match}) => {
       return(
           <p>
               포스트 #{match.params.id}
           </p>
       );
   };
   
   export default Post;
   ```



2. Post 페이지를 만든 후 이 컴포넌트를 pages 디렉터리의 index에 등록

   src/pages/index.js

   ```
   export { default as Home } from './Home';
   export { default as About } from './About';
   export { default as Post } from './Post';
   ```



3. 포스트 목록을 보여 줄 Posts 페이지 컴포넌트 생성

   src/pages/Posts.js

   ```
   import React from 'react';
   import { Post } from 'pages';
   import { Link, Route } from 'react-router-dom';
   
   const Posts = ({match})=>{
       console.log('Posts:', match);
       return(
           <div>
               <h3>포스트 목록</h3>
               <ul>
                   <li><Link to="{`${match.url}/1`}">포스트 #1</Link></li>
                   <li><Link to="{`${match.url}/2`}">포스트 #2</Link></li>
                   <li><Link to="{`${match.url}/3`}">포스트 #3</Link></li>
               </ul>
               <Route exact path={match.url} render={()=> (<p>포스트를 선택하세요</p>)}/>
               <Route exact path={`${match.url}/:id`} component={ Post }/>
           </div>
       );
   };
   
   export default Posts;
   ```

4. Posts 페이지를 만든 후 이 컴포넌트를 pages 디렉터리의 index에 등록

   src/pages/index.js

   ```
   export { default as Home } from './Home';
   export { default as About } from './About';
   export { default as Post } from './Post';
   export { default as Posts } from './Posts';
   ```

5. src/App.js 라우트를 등록하고, Menu에서 링크 추카

   ```
   import React from 'react';
   import { Route } from 'react-router-dom';
   import { Home, About, Posts } from 'pages';
   import Menu from 'components/Menu';
   
   const App = () => {
   	return(
   		<div>
   			<Menu />
   			<Route exact path="/" component={Home} />
   			<Route path="/about/:name?" component={About} />
   			<Route path="/posts" component={Posts} />
   		</div>
   	)
   }
   
   export default App;
   ```

6.  src/components/Menu.js

   ```
   import React from 'react';
   import { NavLink, withRouter } from 'react-router-dom';
   
   const Menu = () =>{
       const activeStyle = {
           color : 'green',
           fontSize : '2rem'
       }
       return(
           <div>
               <ul>
                   <li><NavLink exact to="/" activeStyle={activeStyle}>홈</NavLink></li>
                   <li><NavLink exact to="/about" activeStyle={activeStyle}>소개</NavLink></li>
                   <li><NavLink to="/about/react" activeStyle={activeStyle}>React 소개</NavLink></li>
                   <li><NavLink to="/posts" activeStyle={activeStyle}>포스트 목록</NavLink></li>
               </ul>
           </div>
       )
   }
   
   export default Menu;
   ```




### 16.6 라우트로 사용된 컴포넌트가 전달받는 props

location, match, history 객체가 어떤 역할을 하는가?



#### 16.6.1 location

**location**은 현재 페이지의 주소 상태를 알려준다.



#### 16.6.2 match

**match**는  `<Route>` 컴포넌트에서 설정한 path와 관련된 데이터들을 조회할 때 사용한다.

현재 URL이 같을지라도 다른 라우트에서 사용된 match는 다른 정보를 알려준다.



#### 16.6.3 history

**history**는 현재 라우터를 조작할 때 사용한다.

이 객체에서 헷갈릴 수 있는 함수 **push**와 **replace**가 있다.

**replace는 페이지 방문 기록을 남기지 않아서 페이지 이동 후 뒤로가기 버튼을 눌렀을 때 방금 전의 페이지가 아니라 방금 전의 전 페이지가 나타난다.**



### 16.7 withRouter로 기타 컴포넌트에서 라우터 접근

**라우트 내부 또는 외부 컴포넌트에서는 location, match, history 값을 사용할 수 없다.**

이 때는 **withRouter**를 사용하여 해당 props에 접근할 수 있다.



src/components/Menu.js

```
import { NavLink , withRouter } from 'react-router-dom';
(...)

export default withRouter(Menu);
```



**withRouter는 주로 history에 접근하여 컴포넌트에서 라우터를 조작하는데 사용**

