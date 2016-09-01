### 一个简单的使用`react`的`HTML`模板
```
<!DOCTYPE html>
<html>
  <head>
    <script src="../build/react.js"></script>
    <script src="../build/react-dom.js"></script>
    <script src="../build/browser.min.js"></script>
  </head>
  <body>
    <div id="container"></div>
    <script type="text/babel">
      // ** Our code goes here! **
        ReactDOM.render(
          <h2>HelloWorld</h2>,
          document.getElementById('container')
        );
    </script>
  </body>
</html>
```

- 一共用了三个库：`react.js`、`react-dom.js`和`Browser.js`，它们必须首先加载。其中，`react.js`是`React`的核心库，`react-dom.js`是提供与`DOM`相关的功能，`Browser.js`的作用是将`JSX`语法转为`JavaScript`语法，这一步很消耗时间，实际上线的时候，应该将它放到服务器端完成。

- 最后一个`<script>`标签的`type`属性为`text/babel`。这是`React`独有的[JSX](https://facebook.github.io/react/docs/jsx-in-depth.html)语法，跟`JavaScript`不兼容。凡是使用`JSX`语法的地方，都要加上`type="text/babel"`。

- `ReactDOM.render`将`<h2>HelloWorld</h2>`渲染到`div#container`中；

渲染之后的`container`：

```
<div id="container"><h2 data-reactroot="">HelloWorld</h2></div>
```

### JSX转换

将`jsx`转换为`js`，需要使用`babel`工具；

```
npm install -g babel-cli
cd ———— // 进入到js文件所在目录

npm install babel-preset-react
babel example.js --presets react --out-dir=build
```
上面的命令将`example.js`进行语法转换，转换后的文件存放在同级目录下的`build`文件夹中；
```
$ babel src --out-dir build
```
上面命令可以将`src`子目录的`js`文件进行语法转换，转码后的文件全部放在`build`子目录。

