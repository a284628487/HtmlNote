# props

### props.children

组件的`this.props`对象的属性与组件的属性一一对应，但是有一个例外，就是`this.props.children`属性。它表示组件的所有子节点；

```
<script type="text/babel">
  var NotesList = React.createClass({
    render: function() {
      return (
        <ol>
        {
          React.Children.map(this.props.children, function (child) {
            return <li>{child}</li>;
          })
        }
        </ol>
      );
    }
  });

  ReactDOM.render(
    <NotesList>
      <span>hello</span>
      <span>world</span>
    </NotesList>,
    document.getElementById('container')
  );
</script>
```

上面的代码将两个`span`标签的外部分别包装上`li`标签，然后在最外层包装上`ol`标签；

这里需要注意，`this.props.children`的值有三种可能：

  - 如果当前组件没有子节点，它就是`undefined`；
  - 如果有一个子节点，数据类型是`object`；
  - 如果有多个子节点，数据类型就是`array`；

`React`提供了工具方法`React.Children`来处理`this.props.children`。用`React.Children.map`来遍历子节点，而不用担心`this.props.children`的数据类型是`undefined`还是`object`，[更多](https://facebook.github.io/react/docs/top-level-api.html#react.children)


### PropTypes

组件的属性可以接受任意值，字符串、对象、函数等等都可以。有时，我们需要一种机制，验证别人使用组件时，提供的参数是否符合要求。组件类的`PropTypes`属性，就是用来验证组件实例的属性是否符合要求；
```
<script type="text/javascript">
  var MyTitle = React.createClass({
    propTypes: {
      title: React.PropTypes.string.isRequired,
    },

    render: function() {
       return <h1> {this.props.title} </h1>;
     }
  });
</script>
```
`MyTitle`组件有一个`title`属性。`PropTypes`规定这个`title`属性是必须的，而且它的值必须是字符串。

[更多](http://facebook.github.io/react/docs/reusable-components.html)

此外，`getDefaultProps` 方法可以用来设置组件属性的默认值。
```
<script type="text/javascript">
  var MyTitle = React.createClass({
    getDefaultProps : function () {
      return {
        title : 'Hello World'
      };
    },

    render: function() {
       return <h1> {this.props.title} </h1>;
     }
  });

  ReactDOM.render(
    <MyTitle />,
    document.getElementById('container')
  );
</script>
```
