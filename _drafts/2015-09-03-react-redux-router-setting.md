---
layout: post
category: 카테고리
title:  "React.js - Redux - Router 튜토리얼"
date:   2015-09-03 10:36
tags: none 
comments: true
---

1. React-Router
	1.  설치
		- `npm install --save react-router`
	2. components폴더 생성
		- App.js 작성 ( route root )

				'use strinct';
				
				import React from 'react';
				import {RouteHandler} from 'react-router';
				
				class App extends React.Component {
				    render() {
				        return (
				            <div> <RouteHandler/> </div>
				        )
				    }
				}
				
				export default App;
		- HelloWorld.js작성 ( default route )
				
				'use strict';
				
				import React from 'react';
				
				class HelloWorld extends React.Component {
				    render() {
				    	return (
				       	<div> Hello World! </div>
				       )
				    }
				}
				
				export default HelloWorld;
		- Route.js 작성 ( route 내용 작성 )

				'use strict';
				
				import {App, HelloWorld} from './index';
				import {DefaultRoute, Route} from 'react-router';
				import React from 'react';
				
				export default (
				    <Route handler = {App} path = '/'>
				        <DefaultRoute handler={HelloWorld}/>
				    </Route>
				);
		- index.js 작성 ( components폴더를 require 했을 때, export할 모듈리스트 )

		
				'use strict';
				
				export {default as App} from './App';
				export {default as HelloWorld} from './HelloWorld';
				export {default as Route} from './Route';

	3. entry에 router 설정
	
			'use strict';
			
			import React from 'react';
			import {Route} from './components';
			import Router from 'react-router';
			
			Router.run(Route, (Handler) => {
			    React.render(<Handler/>, document.getElementById('app'));
			})
	4. Hello World 출력확인

2. Redux
	1. 설치
		- `npm install --save react-redux`
	2. 중요 개념이해
		1. Provider 컴포넌트
			- 항상 Root Component를 wrapping 하고 있어야함.
			- 이 컴포넌트로 Wrapping 하면, 자식~리프 자식들까지 context로써 전달된 redux-store에 접근가능함.
			- 현재는 react 0.13의 컨텍스트 이슈때문에 child를 함수로 가져야한다. 0.14부터는 이러지 않아도 될 것이다.
			- 컴포넌트에 contextType를 세팅하면, store접근가능 한 것 확인함.
			- context자체는 undocumented feature임.
			- contextType을 이용해 store에 직접접근할 수 있지만, connect 함수/데코레이터를 이용해 컴포넌트에 노출시키고 싶은 변수만 노출 시키거나, dispatch를 하거나 하는 것이 가능하다.
			
		2. connect
			- component와, redux store를 연결함. ( 내부적으로 Provider에서 넘겨준 store를 context를 이용하여 사용한다는 것임 )
			- store의 dispatch function을 컴포넌트의 props에 add시켜줌.
			- dumb컴포넌트를 smart컴포넌트로 바꾸는 중요키워드
			- decorator, 함수 형태로 제공된다.
			- decorator는 experimental이다.
			- ex)
	
					// dispatch만 props에 add
					export default connect()(컴포넌트이름)
					// state에있는 변수 expose ( subscribe 됨 )
					export default connect(state=>{ return { value:state.value }; })(컴포넌트이름)
