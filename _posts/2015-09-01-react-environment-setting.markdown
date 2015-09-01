---
layout: post
category: web
title:  "React.js + gulp,webpack 환경설정"
date:   2015-09-01 08:36
tags: javascript, react, gulp, webpack
comments: true
---

#React.js + gulp, webpack 환경설정
React.js , gulp, webpack 환경설정을 ES6 문법으로 세팅하는 법입니다.

1. 설치
	- 글로벌
		- npm : https://github.com/npm/npm
		- gulp : `npm install -g gulp`
		
	- 프로젝트
		- npm초기화 : `npm init`
		- gulp 프로젝트에 세팅 : `npm install --save-dev gulp`
		- gitignore : https://github.com/github/gitignore/blob/master/Node.gitignore
		
2. gulpfile 작성
	- gulpfile.js 가 필요하다.
	- ES6문법지원을 위하여, gulpfile.babel.js 로 파일을 만든다.
	- babel 설치 : `npm install --save-dev babel` 
				    `npm install --save-dev babel-core`
	- gulpfile.babel.js 내용작성

		~~~javascript
			'use strict';
			
			import gulp from 'gulp';
			
			//default task 작성
			gulp.task('default', []);
		~~~
	

3. webpack 설정 : 한 단계마다 `gulp` 명령어를 치면서, 어디가 잘못 되었는지 확인하면서 진행가능.
	1. 파일/폴더구조 세팅
		- src/ : 빌드 전 파일들이 위치하는 곳
		- dist/ : 빌드 후 결과물
	2. webpack 설치
		- gulp-webpack 이름이 webpack-stream으로 변경되었다.
		- `npm install --save-dev webpack-stream`
		- webpack에서의 babel loader 적용을 위하여 설치
		- `npm install --save-dev babel-loader`
	3. webpack.config.js 작성

		~~~javascript
		'use strict';
		
		export default {
			//시작될 js모듈이름
		    entry: './src/entry',
		    output: {
		    	 // 빌드 후 output js 파일이름
		        filename: 'bundle.js'
		    },
		    module: {
		        loaders: [
		            {
		            	   // load할 파일정규식
		                test: /\.js$/,
		                // load제외할 폴더/파일
		                exclude: /(node_modules)/,
		                loader: 'babel'
		            }
		        ]
		    }
		};
		~~~

	4. gulpfile.babel.js 수정

		~~~javascript
		'use strict';
		
		import gulp from 'gulp';
		import webpack from 'webpack-stream';
		import webpackConfig from './webpack.config';
		
		// webpack 빌드, output destination 설정
		gulp.task('webpack', () => {
		    return webpack(webpackConfig).pipe(gulp.dest('./dist'));
		});
		
		// default task 작성
		gulp.task('default', ['webpack']);
		~~~
		
	5. `gulp`
	
4. React.js
	1. 설치 ( --save-dev 가 아니다! )
		`npm install --save react`
		- Refusing to install react as a dependency of itself
			- 이와 같은 에러가 났을 떄는, package.json안에 어떤 필드가 'react' 인지 확인하고 수정하면 된다. 필자는 name이 react 라서 문제를 겪었다.
	2. 테스트를 위한 src/index.html 파일 생성 ( 빌드된 output파일을 load 하도록 작성, 추후 React Component 가 붙을 div 도 생성)

		~~~html
		<!DOCTYPE html>
		<html lang="en">
		<head>
		    <meta charset="UTF-8">
		    <title>React.js</title>
		</head>
		<body>
			<div id="app"></div>
		    <script type="text/javascript" src="bundle.js"></script>
		</body>
		</html>
		~~~
		
	3. gulpfile.babel.js에 index.html 을 dist 로 복사하는 자동화명령 추가.
	
		~~~javascript
		'use strict';
		
		import gulp from 'gulp';
		import webpack from 'webpack-stream';
		import webpackConfig from './webpack.config';
		
		gulp.task('copy', () =>  {
		    return gulp.src('./src/index.html').pipe(gulp.dest('./dist'));
		})
		
		gulp.task('webpack', () => {
		    return webpack(webpackConfig).pipe(gulp.dest('./dist'));
		});
		
		// default task 작성
		gulp.task('default', ['copy', 'webpack']);
	~~~
	
	4. entry.js에 React.js 테스트코드 작성

		~~~javascript
		'use strict';
		
		import React from 'react';
			
		class HelloWorld extends React.Component {
		    render() {
		        return (
		            <div> Hello World </div>
		        )
		    }
		}
			
		React.render(<HelloWorld/>, document.getElementById('app'));
		~~~	
	5. `gulp` 후 확인! Hello World!
