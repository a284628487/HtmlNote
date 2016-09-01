
### Component

`React`允许将代码封装成组件（component），然后像插入普通`HTML`标签一样，在网页中插入这个组件。

```
<script type="text/babel">
	var HelloMessage = React.createClass({
	  render: function() {
	    return <h1>Hello { this.props.name }</h1>;
	  }
	});

	ReactDOM.render(
	  <HelloMessage name="John" />,
	  document.getElementById('container')
	);
</script>
```

变量`HelloMessage`就是一个组件类。模板插入 <HelloMessage /> 时，会自动生成`HelloMessage`的一个实例（下文的"组件"都指组件类的实例）。所有组件类都必须有自己的`render`方法，用于输出组件。

**组件类的第一个字母必须大写，否则会报错，另外，组件类只能包含一个顶层标签，否则也会报错。**

下面的写法就是错误的!!!

```
var HelloMessage = React.createClass({
  render: function() {
    return <h1>
      Hello {this.props.name}
    </h1><p>
      some text
    </p>;
  }
});
```

组件的用法与原生的`HTML`标签完全一致，可以任意加入属性，比如`<HelloMessage name="John">`，就是`HelloMessage`组件加入一个`name`属性。组件的属性可以在组件类的`this.props`对象上获取。

**添加组件属性，有一个地方需要注意，就是`class`属性需要写成`className`，`for`属性需要写成`htmlFor`，这是因为`class`和`for`是`JavaScript`的保留字。**

example:

```
<script type="text/babel">
  var Hm = React.createClass({
    render: function () {
      return <div className={ this.props.className } >
      <h1> Hello {this.props.name} </h1>
      <p> ppp </p>
      </div>;
    }
  });
  ReactDOM.render(
    <Hm name='ccf' className='class1'/>,
    document.getElementById('container')
  );
</script>
```

渲染内容：

```
<div data-reactroot="" class="class1">
    <h1>Hello ccf</h1>
    <p>st</p>
</div>
```
