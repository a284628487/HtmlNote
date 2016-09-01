### Form表单

用户在表单填入的内容，属于用户跟组件的互动，所以不能用`this.props`读取

```
<script type="text/javascript">
  var Input = React.createClass({
    getInitialState: function() {
      return {value: 'Hello!'};
    },
    handleChange: function(event) {
      this.setState({value: event.target.value});
    },
    render: function () {
      var value = this.state.value;
      return (
        <div>
          <input type="text" value={value} onChange={this.handleChange} />
          <p>{value}</p>
        </div>
      );
    }
  });

  ReactDOM.render(
  	<Input/>, 
  	document.getElementById('container')
  );
</script>
```

文本输入框的值，不能用`this.props.value`读取，而要定义一个`onChange`事件的回调函数，通过`event.target.value`读取用户输入的值。`textarea`、`select`、`radio`都属于这种情况，[更多](http://facebook.github.io/react/docs/forms.html)。

