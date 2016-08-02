# react 入门

### HTML模板
```
<!DOCTYPE html>
<html>
  <head>
    <script src="../build/react.js"></script>
    <script src="../build/react-dom.js"></script>
    <script src="../build/browser.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
      // ** Our code goes here! **
    </script>
  </body>
</html>
```
上面代码有两个地方需要注意。首先，最后一个`<script>`标签的`type`属性为`text/babel`。这是`React`独有的[JSX](https://facebook.github.io/react/docs/jsx-in-depth.html)语法，跟`JavaScript`不兼容。凡是使用`JSX`的地方，都要加上`type="text/babel"`。

其次，上面代码一共用了三个库：`react.js`、`react-dom.js`和`Browser.js`，它们必须首先加载。其中，`react.js`是`React`的核心库，`react-dom.js`是提供与`DOM`相关的功能，`Browser.js`的作用是将`JSX`语法转为`JavaScript`语法，这一步很消耗时间，实际上线的时候，应该将它放到服务器完成。
```
npm install -g babel-cli
cd examples/basic-jsx-precompile/
npm install babel-preset-react
babel example.js --presets react --out-dir=build
```
上面的命令将`example.js`进行语法转换；
```
$ babel src --out-dir build
```
上面命令可以将`src`子目录的`js`文件进行语法转换，转码后的文件全部放在`build`子目录。

### ReactDOM.render()

```
<script type="text/babel">
	ReactDOM.render(
		<h2>HelloWorld</h2>,
		document.getElementById('container')
	);
</script>
```
上面代码将一个`h2`标题，插入`container`节点

### JSX 语法

`HTML`语言直接写在`JavaScript`语言之中，不加任何引号，这就是[`JSX`的语法](http://facebook.github.io/react/docs/displaying-data.html#jsx-syntax)，它允许`HTML`与`JavaScript`的混写。
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
`JSX`的基本语法规则：遇到`HTML`标签（以 < 开头），就用`HTML`规则解析；遇到代码块（以 { 开头），就用`JavaScript`规则解析。

`JSX`还允许直接在模板插入`JavaScript`变量。如果这个变量是一个数组，则会展开这个数组的所有成员。
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
组件类的第一个字母必须大写，否则会报错，另外，组件类只能包含一个顶层标签，否则也会报错。

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

添加组件属性，有一个地方需要注意，就是`class`属性需要写成`className`，`for`属性需要写成`htmlFor`，这是因为`class`和`for`是`JavaScript`的保留字。

### props.children

`this.props`对象的属性与组件的属性一一对应，但是有一个例外，就是`this.props.children`属性。它表示组件的所有子节点

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
```

这里需要注意，`this.props.children`的值有三种可能：如果当前组件没有子节点，它就是`undefined，`如果有一个子节点，数据类型是`object`；如果有多个子节点，数据类型就是`array`。
`React`提供了工具方法`React.Children`来处理`this.props.children`。用`React.Children.map`来遍历子节点，而不用担心`this.props.children`的数据类型是`undefined`还是`object`，[更多](https://facebook.github.io/react/docs/top-level-api.html#react.children)

### PropTypes

组件的属性可以接受任意值，字符串、对象、函数等等都可以。有时，我们需要一种机制，验证别人使用组件时，提供的参数是否符合要求。组件类的`PropTypes`属性，就是用来验证组件实例的属性是否符合要求；
```
var MyTitle = React.createClass({
  propTypes: {
    title: React.PropTypes.string.isRequired,
  },

  render: function() {
     return <h1> {this.props.title} </h1>;
   }
});
```
`MyTitle`组件有一个`title`属性。`PropTypes`规定这个`title`属性是必须的，而且它的值必须是字符串。
[更多](http://facebook.github.io/react/docs/reusable-components.html)

此外，getDefaultProps 方法可以用来设置组件属性的默认值。
```
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
  document.body
);
```

### 获取真实的DOM节点

组件并不是真实的 DOM 节点，而是存在于内存之中的一种数据结构，叫做虚拟`DOM（virtual DOM）`。只有当它插入文档以后才会变成真实的 DOM 。根据 React 的设计，所有的 DOM 变动，都先在虚拟 DOM 上发生，然后再将实际发生变动的部分，反映在真实`DOM`上，这种算法叫做`DOM diff`，它可以极大提高网页的性能表现。
但是，有时需要从组件获取真实`DOM`的节点，这时就要用到`ref`属性；
```
var MyComponent = React.createClass({
  handleClick: function() {
    this.refs.myTextInput.focus();
  },
  render: function() {
    return (
      <div>
        <input type="text" ref="myTextInput" />
        <input type="button" value="Focus the text input" onClick={this.handleClick} />
      </div>
    );
  }
});

ReactDOM.render(
  <MyComponent />,
  document.getElementById('container')
);
```
组件`MyComponent`的子节点有一个文本输入框，用于获取用户的输入。这时就必须获取真实的`DOM`节点，虚拟`DOM`是拿不到用户输入的。为了做到这一点，文本输入框必须有一个`ref`属性，然后通过`this.refs.[refName]`就可以获取到这个真实的`DOM`节点。

需要注意的是，由于`this.refs.[refName]`属性获取的是真实`DOM`，所以必须等到虚拟`DOM`，插入文档以后，才能使用这个属性，否则会报错。上面代码中，通过为组件指定`Click`事件的回调函数，确保了只有等到真实`DOM`发生`Click`事件之后，才会读取`this.refs.[refName]`属性。

除了 Click 事件以外，还有 KeyDown 、Copy、Scroll 等，完整的事件清单请查看[官方文档](http://facebook.github.io/react/docs/events.html#supported-events)。

### this.state

组件免不了要与用户互动，`React`的一大创新，就是将组件看成是一个状态机，一开始有一个初始状态，然后用户互动，导致状态变化，从而触发重新渲染UI

```
var LikeButton = React.createClass({
  getInitialState: function() {
    return {liked: false};
  },
  handleClick: function(event) {
    this.setState({liked: !this.state.liked});
  },
  render: function() {
    var text = this.state.liked ? 'like' : 'haven\'t liked';
    return (
      <p onClick={this.handleClick}>
        You {text} this. Click to toggle.
      </p>
    );
  }
});

ReactDOM.render(
  <LikeButton />,
  document.getElementById('container')
);
```

上面代码定义了一个`LikeButton`组件，它的`getInitialState`方法用于定义初始状态，也就是一个对象，这个对象可以通过`this.state`属性读取。当用户点击组件，导致状态变化，`this.setState`方法可以修改状态值，每次修改以后，会自动调用`this.render`方法，再次渲染组件。
由于`this.props`和`this.state`都用于描述组件的特性，可能会产生混淆。一个简单的区分方法是，`this.props`表示那些一旦定义，就不再改变的特性，而`this.state`是会随着用户互动而产生变化的特性。

### Form表单

用户在表单填入的内容，属于用户跟组件的互动，所以不能用`this.props`读取

```
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
```
文本输入框的值，不能用`this.props.value`读取，而要定义一个`onChange`事件的回调函数，通过`event.target.value`读取用户输入的值。`textarea`、`select`、`radio`都属于这种情况，[更多](http://facebook.github.io/react/docs/forms.html)。

### 组件的生命周期

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
因为[React 组件样式](https://facebook.github.io/react/tips/inline-styles.html)是一个对象，所以第一重大括号表示这是`JavaScript`语法，第二个大括号表示`样式对象`。

### Ajax

组件的数据来源，通常是通过`Ajax`请求从服务器获取，可以在`componentDidMount`方法里面发起`Ajax`请求，等到请求成功，再用`this.setState`方法重新渲染UI 

```
var UserGist = React.createClass({
  getInitialState: function() {
    return {
      username: '',
      lastGistUrl: ''
    };
  },

  componentDidMount: function() {
    $.get(this.props.source, function(result) {
      var lastGist = result[0];
      if (this.isMounted()) {
        this.setState({
        	username: lastGist.owner.login,
        	lastGistUrl: lastGist.html_url
        });
      }
    }.bind(this));
  },

  render: function() {
    return (
      <div>
        {this.state.username} -> last gist is
        <a href={this.state.lastGistUrl}>here</a>.
      </div>
    );
  }
});

ReactDOM.render(
  <UserGist source="https://api.github.com/users/octocat/gists" />,
  document.getElementById('container')
);
```

`React`本身没有任何依赖，完全可以不用`jQuery`，而使用其他库。我们甚至可以把一个`Promise`对象传入组件；
```
var RepoList = React.createClass({
  getInitialState: function() {
    return { loading: true, error: null, data: null};
  },

  componentDidMount() {
    this.props.promise.then(
      value => this.setState({loading: false, data: value}),
      error => this.setState({loading: false, error: error}));
  },

  render: function() {
    if (this.state.loading) {
      return <span>Loading...</span>;
    }
    else if (this.state.error !== null) {
      return <span>Error: {this.state.error.message}</span>;
    }
    else {
      var repos = this.state.data.items;
      var repoList = repos.map(function (repo) {
        return (
          <li>
            <a href={repo.html_url}>{repo.name}</a> ({repo.stargazers_count} stars) <br/> {repo.description}
          </li>
        );
      });
      return (
        <main>
          <h1>Most Popular JavaScript Projects in Github</h1>
          <ol>{repoList}</ol>
        </main>
      );
    }
  }
});

ReactDOM.render(
  <RepoList
    promise={$.getJSON('https://api.github.com/search/repositories?q=javascript&sort=stars')} />,
  document.getElementById('container')
);
```

上面代码从`Github`的API抓取数据，然后将`Promise`对象作为属性，传给`RepoList`组件。
如果`Promise`对象正在抓取数据（pending状态），组件显示 正在加载 ；如果`Promise`对象报错（rejected状态），组件显示报错信息；如果`Promise`对象抓取数据成功（fulfilled状态），组件显示获取的数据。


### Precompiled
```
npm install -g babel-cli
cd examples/basic-jsx-precompile/
npm install babel-preset-react
babel example.js --presets react --out-dir=build
```
转换前
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
  ReactDOM.render(<ExampleApplication elapsed={new Date().getTime() - start} />,
    document.getElementById('container')
  );
}, 50);
```
转换后
```
var ExampleApplication = React.createClass({
  displayName: 'ExampleApplication',

  render: function () {
    var elapsed = Math.round(this.props.elapsed / 100);
    var seconds = elapsed / 10 + (elapsed % 10 ? '' : '.0');
    var message = 'React has been successfully running for ' + seconds + ' seconds.';

    return React.createElement(
      'p', // tagName
      null, // attr?
      message // text
    );
    // 还可以这样写
    // return React.DOM.div({ onClick: this.handleClick, className: 'commentBox', style: { width: '200px', color: '#FF4400', backgroundColor: '#1a9fe0', fontSize: '20px' }, }, 'message');
    // 然后ReactDOM.render这样来实现：
      // var CommentBoxFactory = React.createFactory(ExampleApplication);
      // ReactDOM.render(
      //   CommentBoxFactory({elapsed: new Date().getTime()}),
      //   document.getElementById('content')
      // );
  }
});

var start = new Date().getTime();

setInterval(function () {
  ReactDOM.render(React.createElement(ExampleApplication, { elapsed: new Date().getTime() - start }), document.getElementById('container'));
}, 50);
```
对比两个文件，实际上就是对`React.createClass`的`render`方法返回的内容和`ReactDOM.render`方法进行转换了；

### ES6 fetures
```
//
var seconds = 12.0;
var message = `React has been successfully running for ${seconds} seconds.`;
//
setInterval(() => {
  ReactDOM.render(
    <ExampleApplication elapsed={new Date().getTime() - start} />,
    document.getElementById('container')
  );
}, 50);
```

[Link](http://www.ruanyifeng.com/blog/2015/03/react.html)

