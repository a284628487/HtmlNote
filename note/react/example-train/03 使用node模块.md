# 使用node模块

#### browserify

`Browserify` 可以让你使用类似于 `node` 的 `require()` 的方式来组织浏览器端的 `Javascript` 代码，通过预编译让前端 `Javascript` 可以直接使用 `Node NPM` 安装的一些库。

### index.js

```
'use strict';

var React = require('react');
var ReactDOM = require('react-dom');

var ExampleApplication = React.createClass({
  render: function() {
    var elapsed = Math.round(this.props.elapsed  / 100);
    var seconds = elapsed / 10 + (elapsed % 10 ? '' : '.0' );
    var message =
      'React has been successfully running for ' + seconds + ' seconds.';

    return <p>{message}</p>;
  }
});

var start = new Date().getTime();

setInterval(function() {
  ReactDOM.render(
    <ExampleApplication elapsed={new Date().getTime() - start} />,
    document.getElementById('container')
  );
}, 50);
```

### package.json

```
{
  "name": "react-basic-commonjs-example",
  "description": "Basic example of using React with CommonJS",
  "private": true,
  "main": "index.js",
  "dependencies": {
    "babel-preset-es2015": "^6.6.0",
    "babel-preset-react": "^6.5.0",
    "babelify": "^7.3.0",
    "browserify": "^13.0.0",
    "react": "^15.0.2",
    "react-dom": "^15.0.2",
    "watchify": "^3.7.0"
  },
  "scripts": {
    "build": "browserify ./index.js -t babelify -o bundle.js",
    "start": "watchify ./index.js -v -t babelify -o bundle.js"
  }
}
```

- dependencies : 结合 `index.js` 里面的 `require(react)` ，`require(react-dom)`，在 `package.json` 里面，`dependencies` 依赖了这两个库，并且还有 `jsx` 语法转换的库；

- scripts 

	```
    "build": "browserify ./index.js -t babelify -o bundle.js",
    "start": "watchify ./index.js -v -t babelify -o bundle.js"
	```
两句执行脚本，用于将 `index.js` 脚本打包成 `bundle.js`；

- 执行脚本

```
npm install
npm start
```

### index.html

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Basic CommonJS Example with Browserify</title>
  <body>
    <div id="container">
    </div>
    <script src="bundle.js"></script>
  </body>
</html>
```
网页里面没有引用 `react.js`，也没有 `browser.min.js`，取而代之的是 `buidle.js`，也就是上一步中生成的 `js` 文件；

### bundle.js

在文件的开头可以看到这样的一段 `js`；

```
'use strict';

var React = require('react');
var ReactDOM = require('react-dom');

var ExampleApplication = React.createClass({
  displayName: 'ExampleApplication',

  render: function render() {
    var elapsed = Math.round(this.props.elapsed / 100);
    var seconds = elapsed / 10 + (elapsed % 10 ? '' : '.0');
    var message = 'React has been successfully running for ' + seconds + ' seconds.';

    return React.createElement(
      'p',
      null,
      message
    );
  }
});

var start = new Date().getTime();

setInterval(function () {
  ReactDOM.render(React.createElement(ExampleApplication, { elapsed: new Date().getTime() - start }), document.getElementById('container'));
}, 50);
```
实际上就是 `babel` 转换 `index.js` 得到的普通的 `js`；



