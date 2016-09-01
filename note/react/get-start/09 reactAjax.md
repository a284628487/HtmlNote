# Ajax

组件的数据来源，通常是通过`Ajax`请求从服务器获取，可以在`componentDidMount`方法里面发起`Ajax`请求，等到请求成功，再用`this.setState`方法重新渲染UI。

```
<script type="text/javascript">
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
</script>
```

`React`本身没有任何依赖，完全可以不用`jQuery`，而使用其他库。我们甚至可以把一个`Promise`对象传入组件；
```
<script type="text/javascript">
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
</script>
```

上面代码从`Github`的API抓取数据，然后将`Promise`对象作为属性，传给`RepoList`组件。

- 如果`Promise`对象正在抓取数据（pending状态），组件显示 正在加载 ；
- 如果`Promise`对象报错（rejected状态），组件显示报错信息；
- 如果`Promise`对象抓取数据成功（fulfilled状态），组件显示获取的数据。
