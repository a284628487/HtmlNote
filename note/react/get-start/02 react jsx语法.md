### JSX 语法

`HTML`标签直接写在`JavaScript`语言之中，不加任何引号，这就是[`JSX`的语法](http://facebook.github.io/react/docs/displaying-data.html#jsx-syntax)，它允许`HTML`与`JavaScript`的混写。

code:

```
<script type="text/babel">
  var names = ['Facebook','Tencent'];
  ReactDOM.render(
    <div>
    {
      names.map(function(name) {
        return <div>{name}!</div>
      })
    }
    </div>,
    document.getElementById('container')
  );
</script>
```

渲染之后的内容：
```
<div id="container">
  <div data-reactroot="">
    <div>Facebook!</div>
    <div>Tencent!</div>
  </div>
</div>
```

### 基本语法规则

- 遇到`HTML`标签（以 < 开头），就用`HTML`规则解析；遇到代码块（以 { 开头），就用`JavaScript`规则解析。

- `JSX`还允许直接在模板插入`JavaScript`变量。如果这个变量是一个数组，则会展开这个数组的所有成员。

```
<script type="text/babel">
	var arr = [
	  <h1>Hello world!</h1>,
	  <h2>React is awesome</h2>,
	];
	ReactDOM.render(
	  <div>{arr}</div>,
	  document.getElementById('container')
	);
</script>
```

渲染效果基本同上;

