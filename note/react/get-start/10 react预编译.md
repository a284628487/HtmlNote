# Precompiled

### 转换命令

```
npm install -g babel-cli
cd ———— // 进入到js文件所在目录
npm install babel-preset-react
babel example.js --presets react --out-dir=build
```
预编译出来的js文件存放于build文件夹中;

### 转换前
```
<script type="text/babel">
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
    ReactDOM.render(<ExampleApplication elapsed={new Date().getTime() - start} />,
      document.getElementById('container')
    );
  }, 50);
</script>
```

### 转换后

```
<script type="text/javascript">
  var ExampleApplication = React.createClass({
    displayName: 'ExampleApplication',

    render: function () {
      var elapsed = Math.round(this.props.elapsed / 100);
      var seconds = elapsed / 10 + (elapsed % 10 ? '' : '.0');
      var message = 'React has been successfully running for ' + seconds + ' seconds!';

      return React.createElement(
        'p', // tagName
        null, // attr?
        message // text
      );
    }
  });

  var start = new Date().getTime();

  setInterval(function () {
    ReactDOM.render(React.createElement(ExampleApplication, { elapsed: new Date().getTime() - start }), document.getElementById('container'));
  }, 50);
</script>
```
对比两个文件，实际上就是对`React.createClass`中的`render`方法返回的内容和`ReactDOM.render`方法进行转换了，将 `jsx` 语法转换成了标准的 `js` 语法；


### index.html

```
    <script src="../../build/react.js"></script>
    <script src="../../build/react-dom.js"></script>
    <script src="build/example.js"></script>
```


上面的 `render` 方法还可以这样写

```
// 还可以这样写
return React.DOM.div({ onClick: this.handleClick, className: 'commentBox', style: { width: '200px', color: '#FF4400', backgroundColor: '#1a9fe0', fontSize: '20px' }, }, 'message');
```

然后 `ReactDOM.render()` 这样来实现：

```
var CommentBoxFactory = React.createFactory(ExampleApplication);
ReactDOM.render(
  CommentBoxFactory({elapsed: new Date().getTime()}),
  document.getElementById('content')
);
```

