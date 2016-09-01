### 定义一个 `x-search` 组件

```
    <script src="https://cdnjs.cloudflare.com/ajax/libs/webcomponentsjs/0.7.21/webcomponents.js"></script>
    <script src="../../build/react.js"></script>
    <script src="../../build/react-dom.js"></script>
    <script src="../../browser.min.js"></script>
    <script type="text/babel">

      // Define WebComponent
      var proto = Object.create(HTMLElement.prototype, {
        attachedCallback: {
          value: function() {
            var mountPoint = document.createElement('span');
            this.createShadowRoot().appendChild(mountPoint);

            var name = this.getAttribute('name');
            var url = 'https://www.google.com/search?q=' + encodeURIComponent(name);
            ReactDOM.render(<a href={url}>{name}</a>, mountPoint);
          }
        }
      });
      document.registerElement('x-search', {prototype: proto});

      // Define React Component
      class HelloMessage extends React.Component {
        render() {
          return <div>Hello <x-search name={this.props.name} />!</div>;
        }
      }

      // Mount React Component (which uses WebComponent which uses React)
      ReactDOM.render(<HelloMessage name="Jim Sproch" />, document.getElementById('container'));
    </script>

```
