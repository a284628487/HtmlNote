
### 使用 `React.createFactory()` 

```
    <script>
      function ExampleApplication(props) {
        var elapsed = Math.round(props.elapsed  / 100);
        var seconds = elapsed / 10 + (elapsed % 10 ? '' : '.0' );
        var message =
          'React has been successfully running for ' + seconds + ' seconds.';

        return React.DOM.p(null, message);
      }

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
```

