---
layout: post
category: web
title:  "React.js - Redux - Router 튜토리얼"
date:   2015-09-04 14:36
tags: javascript, react, redux, react-router, router 
comments: true
---

React에 Redux, React에 Router 를 각각 붙이는 것은 어렵지 않습니다. 하지만 세 개를 동시에 붙여보면 잘 안되기도하고, 여기저기 예제들도 다 달라서 어려움이 많습니다.

가장 큰 문제는 React-Router가 현재 0.13버전이 npm 에 등록되어있고, React 1.0 대응버전이 베타로 나와있기 때문입니다. 대부분의 예제는 1.0.0-beta3으로 되어있어서, 자료를 찾는데 어려움이 많았는데, 중간중간 버전에 따라 정리해보았습니다.

(본 예제는 redux-react-router 라이브러리와 상관없는 내용입니다. React, Redux, React-Router를 동시에 사용하는 방법에대한 내용입니다.)

1. React-Router
	1.  설치
		- 0.13 버전
			- `npm install --save react-router`
		- 1.0.0-beta3
			- `npm intsall --save react-router@1.0.0-beta3`
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

			- 0.13
	
					'use strict';
					
					import {App, HelloWorld} from './index';
					import {DefaultRoute, Route} from 'react-router';
					import React from 'react';
					
					export default (
					    <Route handler = {App} path = '/'>
					        <DefaultRoute handler={HelloWorld}/>
					    </Route>
					);
			- 1.0.0 beta ( context 이슈 때문에, 반드시 함수로 리턴해야한다. )

					'use strict';
					
					import {App, HelloWorld} from './index';
					import React from 'react';
					import {Router, Route} from 'react-router';
					
					function RouteWrapper(history) {
					    return (
					        <Router history={history}>
					            <Route component={App}>
					                <Route path="/" component={HelloWorld} />
					            </Route>
					        </Router>
					    );
					};
					
					export default RouteWrapper;
		- index.js 작성 ( components폴더를 require 했을 때, export할 모듈리스트 )

		
				'use strict';
				
				export {default as App} from './App';
				export {default as HelloWorld} from './HelloWorld';
				// (0.13)
				export {default as Route} from './Route';
				// (1.0.0)
				export {default as RouteWrapper} from './Route';
				
	3. entry에 router 설정
		- 0.13 은 Router.run을 사용하고, 1.0.0 은 React.render를 직접호출하되, Router 컴포넌트를 사용하는 것이 다르다.
		- Router 컴포넌트에 history를 넣어주지않으면 `Uncaught Error: Invariant Violation: Server-side <Router>s need location, branch, params, and components props. Try using Router.run to get all the props you need` 과 같은 에러가 난다.

				'use strict';
				
				import React from 'react';
				// (0.13)
					import {Route} from './components';
					import Router from 'react-router';
				// (1.0.0)beta
					import BrowserHistory from 'react-router/lib/BrowserHistory'
					import {RouteWrapper} from './components';
							
				// (0.13)
					Router.run(Route, (Handler) => {
					    React.render(<Handler/>
					    , document.getElementById('app'));
					})
				
				// (1.0.0) beta
					const history = new BrowserHistory();
					React.render(
						RouteWrapper(history)
			      		, document.getElementById('app')
			      	);
	4. Hello World 출력확인

2. Redux with React.js
	1. 설치
		- `npm install --save react-redux`
	2. React-Redux 주요 키워드
		1. Provider 컴포넌트

				<Provider store={store}>
					{() => Root 컴포넌트 }
				</Provider>
			- 항상 Root 컴포넌트를 wrapping 하고 있어야함.
			- 이 컴포넌트로 Wrapping 하면, 자식~리프 자식들까지 context로써 전달된 redux-store에 접근가능함.
			- 현재는 react 0.13의 컨텍스트 이슈때문에 child를 함수로 가져야한다. 0.14부터는 이러지 않아도 될 것이다.
			- 컴포넌트에 contextType를 세팅하면, store접근가능 한 것 확인함.
			- context자체는 undocumented feature임.
			- contextType을 이용해 store에 직접접근할 수 있지만, connect 함수/데코레이터를 이용해 컴포넌트에 노출시키고 싶은 변수만 노출 시키거나, dispatch를 하거나 하는 것이 가능하다.
			
		2. connect

				// dispatch만 props에 add
				export default connect()(컴포넌트이름)
				// state에있는 변수를 현 컴포넌트.props에 expose ( subscribe 됨 )
				export default connect(state=>{ return { value:state.value }; })(컴포넌트이름)
				
			- component와, redux store를 연결함. ( 내부적으로 Provider에서 넘겨준 store를 context를 이용하여 사용한다는 것임 )
			- store의 dispatch function을 컴포넌트의 props에 add시켜줌.
			- dumb컴포넌트를 smart컴포넌트로 바꾸는 중요키워드
				- 참고 : https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0
			- decorator, 함수 형태로 제공된다.
			- decorator는 experimental이다.
			
	3. Redux 붙이기 ( redux 는 react-redux 에서 dependency로 설치된다 )
		1. Reducer, Store 생성
			- 기본적으로 이전 포스트를 참고하면 됨.
			- 튜토리얼 따라하시는 분을 위해 test용 reducer, store 첨부
				- entry.js
			
						import {createStore} from 'redux';
						function testReducer(state = {}, action) {
						    switch (action.type) {
						        default:
						            return state;
						    }
						}
						
						const store = createStore(testReducer);
		2. Provider 컴포넌트 추가
			- RootComponent 를 Wrapping 하는 Provider 컴포넌트를 만들고, store를 던져주면 완성이다.
			- RootComponent란 별게 아니고, 자신의 App에 최상단 컴포넌트를 말하는데, 사실 위치는 상관이없고, context가 필요한 컴포넌트 상단에 있기만 하면 된다.
			- (중요) Provider 컴포넌트의 child인 Root Component는 현재(React 0.13) function형태로 들어가야한다! 그래야 컴포넌트에서 store를 사용할 수 있다. 0.14부터는 이렇게 사용안해도 될 것이라고 예상한다고 한다.

				- 0.13
				- 만약, Routing이 잘 안된다면 https://github.com/rackt/react-redux -> readme파일에서 routerState로 검색해서, routerState를 Handler 컴포넌트의 prop으로 넣어주면 된다.

						import {Provider} from 'react-redux';
						Router.run(Route, (Handler) => {
						    React.render(
						    <Provider store={store}>
						    	{() => <Handler/> }
						    </Provider>
						    , document.getElementById('app'));
						})				
				- 1.0.0 beta3

						import {Provider} from 'react-redux';
						React.render(
						    <Provider store={store}>
						        {() => RouteWrapper(history) }
						    </Provider>
						    , document.getElementById('app')
						);
		3. Hello World 컴포넌트를 Smart하게 만들자.
			- components/HelloWorld.js

			
					'use strict';
					
					import React from 'react';
					import {connect} from 'react-redux';
					
					class HelloWorld extends React.Component {
					    render() {
					        const {dispatch, state} = this.props;
					        console.log(dispatch);
					        console.log({state});
					        return (
					            <div>Hello World!</div>
					        )
					    }
					}
					export default connect(state => { return ( { state: state } ); })(HelloWorld);
					
			- console.log 로, dispatch, state를 사용할 수 있음을 볼 수 있다.
			- 이제 필요한 기능을 Redux를 사용하여 구현할 수 있을 것이다.