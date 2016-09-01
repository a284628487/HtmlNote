### basic

```
<!DOCTYPE html>
<html>
  <body>
    <div id="container">  
    </div>
    <script src="../../build/react.js"></script>
    <script src="../../build/react-dom.js"></script>
    <script>
      var ExampleApplication = React.createClass({
        render: function() {
          var elapsed = Math.round(this.props.elapsed  / 100);
          var seconds = elapsed / 10 + (elapsed % 10 ? '' : '.0' );
          var message =
            'React has been successfully running for ' + seconds + ' seconds.';

          return React.DOM.p(null, message);
        }
      });

      // Call React.createFactory instead of directly call ExampleApplication({...}) in React.render
      var ExampleApplicationFactory = React.createFactory(ExampleApplication);

      var start = new Date().getTime();
      setInterval(function() {
        ReactDOM.render(
          ExampleApplicationFactory({elapsed: new Date().getTime() - start}),
          document.getElementById('container')
        );
      }, 50);
    </script>
  </body>
</html> 
```

使用最原始的`js`实现的网页；`<script>` 标签不需要指定 `type=text/babel`，在执行 `js` 的时候也不需要 `browser` 来转换；

