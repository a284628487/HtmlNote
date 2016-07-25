# 解模块原理

当我们编写`JavaScript`代码时，我们可以申明全局变量：
```
var s = 'global';
```
在浏览器中，大量使用全局变量不好。如果你在`a.js`中使用了全局变量`s`，那么，在`b.js`中也使用全局变量s，将造成冲突，`b.js`中对`s`赋值会改变`a.js`的运行逻辑。
也就是说，`JavaScript`语言本身并没有一种模块机制来保证不同模块可以使用相同的变量名。

##### 那`Node.js`是如何实现这一点的？

其实要实现“模块”这个功能，并不需要语法层面的支持。`Node.js`也并不会增加任何`JavaScript`语法。实现“模块”功能的奥妙就在于`JavaScript`是一种函数式编程语言，它支持闭包。如果我们把一段`JavaScript`代码用一个函数包装起来，这段代码的所有“全局”变量就变成了函数内部的局部变量。

例如`hello.js`
```
var s = 'Hello';
var name = 'world';
console.log(s + ' ' + name + '!');
```
`Node.js`加载了`hello.js`后，它可以把代码包装一下，变成这样执行：
```
(function () {
    // 读取的hello.js代码:
    var s = 'Hello';
    var name = 'world';
    console.log(s + ' ' + name + '!');
    // hello.js代码结束
})();
```
这样一来，原来的全局变量`s`现在变成了匿名函数内部的局部变量。如果`Node.js`继续加载其他模块，这些模块中定义的“全局”变量s也互不干扰。所以，`Node`利用`JavaScript`的函数式编程的特性，实现了模块的隔离。

##### 模块的输出`module.exports`怎么实现？

这个也很容易实现，Node可以先准备一个对象module：
```
// 准备module对象:
var module = {
    id: 'greet', // 模块名
    exports: {}
};
var load = function (module) {
    // greet.js代码:
    function greet(name) {
        console.log('Hello, ' + name + '!');
    }

    module.exports = greet;
    // greet.js代码结束
    return module.exports;
};
var exported = load(module);
// 保存module:
save(module, exported);
```
可见，变量`module`是`Node`在加载`js`文件前准备的一个变量，并将其传入加载函数，我们在`greet.js`中可以直接使用变量`module`原因就在于它实际上是函数的一个参数：
```
module.exports = greet;
```
通过把参数`module`传递给`load()`函数，`greet.js`就顺利地把一个变量传递给了`Node`执行环境，`Node`会把`module`变量保存到某个地方。

由于`Node`保存了所有导入的`module`，当我们用`require()`获取`module`时，`Node`找到对应的`module`，把这个`module`的`exports`变量返回，这样，另一个模块就顺利拿到了模块的输出：
```
var greet = require('./greet');
```
以上是Node实现JavaScript模块的一个简单的原理介绍。

#### module.exports vs exports

很多时候，你会看到，在`Node`环境中，有两种方法可以在一个模块中输出变量：

方法一：对module.exports赋值：

hello.js
```
function hello() {
    console.log('Hello, world!');
}

function greet(name) {
    console.log('Hello, ' + name + '!');
}

function hello() {
    console.log('Hello, world!');
}

module.exports = {
    hello: hello,
    greet: greet
};
```
方法二：直接使用exports：

hello.js
```
function hello() {
    console.log('Hello, world!');
}

function greet(name) {
    console.log('Hello, ' + name + '!');
}

function hello() {
    console.log('Hello, world!');
}

exports.hello = hello;
exports.greet = greet;
```
但是你不可以直接对exports赋值：
```
// 代码可以执行，但是模块并没有输出任何变量:
exports = {
    hello: hello,
    greet: greet
};
```
如果你对上面的写法感到十分困惑，我们来分析`Node`的加载机制：
- 首先，`Node`会把整个待加载的`hello.js`文件放入一个包装函数`load`中执行。在执行这个`load()`函数前，`Node`准备好了`module`变量：
```
var module = {
    id: 'hello',
    exports: {}
};
```
- load()函数最终返回module.exports：
```
var load = function (exports, module) {
    // hello.js的文件内容
    ...
    // load函数返回:
    return module.exports;
};
```
- var exported = load(module.exports, module);
也就是说，默认情况下，`Node`准备的`exports`变量和`module.exports`变量实际上是同一个变量，并且初始化为空对象{}，于是，我们可以写：
```
exports.foo = function () { return 'foo'; };
exports.bar = function () { return 'bar'; };
```
也可以写：
```
module.exports.foo = function () { return 'foo'; };
module.exports.bar = function () { return 'bar'; };
```
也就是说，`Node`默认给你准备了一个空对象`{}`，这样你可以直接往里面加东西。

- 如果我们要输出的是一个函数或数组，那么，只能给`module.exports`赋值：
```
module.exports = function () { return 'foo'; };
```
给`exports`赋值是无效的，因为赋值后，`module.exports`仍然是空对象{}。

#### 结论
- 如果要输出一个键值对象`{}`，可以利用`exports`这个已存在的空对象`{}`，并继续在上面添加新的键值；
- 如果要输出一个函数或数组，必须直接对`module.exports`对象赋值。

所以我们可以得出结论：直接对module.exports赋值，可以应对任何情况：
```
module.exports = {
    foo: function () { return 'foo'; }
};
```
或者：
```
module.exports = function () { return 'foo'; };
```
最终，我们强烈建议使用`module.exports = xxx`的方式来输出模块变量，这样，你只需要记忆一种方法。




