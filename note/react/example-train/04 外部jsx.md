
example.js

```
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

index.html

```
    <script src="../../build/react.js"></script>
    <script src="../../build/react-dom.js"></script>
    <script src="../../build/browser.min.js"></script>
    <script type="text/babel" src="example.js"></script>
```

对于外部的 `jsx` 语法的 `js`，需要将 `html` 和 `js` 放到服务器上，从服务器上去浏览该网页，才能正常渲染显示；
