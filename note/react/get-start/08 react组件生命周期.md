# 组件的生命周期

组件有3个生命周期状态[rel](https://facebook.github.io/react/docs/working-with-the-browser.html#component-lifecycle)

  - Mounting：已插入真实 DOM
  - Updating：正在被重新渲染
  - Unmounting：已移出真实 DOM

`React`为每个状态都提供了两种处理函数，`will`函数在进入状态之前调用，`did`函数在进入状态之后调用，三种状态共计五种处理函数。

  - componentWillMount()
  - componentDidMount()
  - componentWillUpdate(object nextProps, object nextState)
  - componentDidUpdate(object prevProps, object prevState)
  - componentWillUnmount()

此外，`React`还提供两种特殊状态的处理函数。

  - componentWillReceiveProps(object nextProps)：已加载组件收到新的参数时调用
  - shouldComponentUpdate(object nextProps, object nextState)：组件判断是否重新渲染时调用

[详情查看](http://facebook.github.io/react/docs/component-specs.html#lifecycle-methods)

```
<script type="text/javascript">
var Hello = React.createClass({
    getInitialState: function () {
      return {
        opacity: 1.0
      };
    },

    componentDidMount: function () {
      this.timer = setInterval(function () {
        var opacity = this.state.opacity;
        opacity -= .05;
        if (opacity < 0.1) {
          opacity = 1.0;
        }
        this.setState({
          opacity: opacity
        });
      }.bind(this), 100);
    },

    render: function () {
      return (
        <div style={{opacity: this.state.opacity}}>
          Hello {this.props.name}
        </div>
      );
    }
  });

  ReactDOM.render(
    <Hello name="world"/>,
    document.getElementById('container')
  );
</script>
```

`Hello`组件加载以后，通过`componentDidMount`方法设置一个定时器，每隔100毫秒，就重新设置组件的透明度，从而引发重新渲染。

组件的`style`属性的设置方式也值得注意，不能写成
```
style="opacity:{this.state.opacity};"
```
而要写成
```
style={{opacity: this.state.opacity}}
```

因为[React 组件样式](https://facebook.github.io/react/tips/inline-styles.html)是一个对象，所以第一层大括号表示这是`JavaScript`语法，第二层大括号才是表示`样式对象`。
