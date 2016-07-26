# Node.js 基本模块

`Node.js`是运行在服务器端的`JavaScript`环境，服务器程序和浏览器程序相比，最大的特点是没有浏览器的安全限制了，而且，服务器程序必须能接收网络请求，读写文件，处理二进制内容，所以，`Node.js`内置的常用模块就是为了实现基本的服务器功能。这些模块在浏览器环境中是无法被执行的，因为它们的底层代码是用`C/C++`在`Node.js`运行环境中实现的。

#### global

我们已经知道，`JavaScript`有且仅有一个全局对象，在浏览器中，叫`window`对象。而在`Node.js`环境中，也有唯一的全局对象，但不叫`window`，而叫`global`，这个对象的属性和方法也和浏览器环境的`window`不同。进入`Node.js`交互环境，可以直接输入：
```
> global.console
Console {
  log: [Function: bound ],
  info: [Function: bound ],
  warn: [Function: bound ],
  error: [Function: bound ],
  dir: [Function: bound ],
  time: [Function: bound ],
  timeEnd: [Function: bound ],
  trace: [Function: bound trace],
  assert: [Function: bound ],
  Console: [Function: Console] }
  ```
#### process

`process`也是`Node.js`提供的一个对象，它代表当前`Node.js`进程。通过`process`对象可以拿到许多有用信息：
```
> process === global.process;
true
> process.version;
'v5.2.0'
> process.platform;
'darwin'
> process.arch;
'x64'
> process.cwd(); // 返回当前工作目录
'/Users/a284628487'
> process.chdir('/private/tmp'); // 切换当前工作目录
undefined
> process.cwd();
'/private/tmp'
```

`JavaScript`程序是由事件驱动执行的单线程模型，`Node.js`也不例外。`Node.js`不断执行响应事件的`JavaScript`函数，直到没有任何响应事件的函数可以执行时，`Node.js`就退出了。

如果想要在下一次事件响应中执行代码，可以调用`process.nextTick()`：

test.js
```
// process.nextTick()将在下一轮事件循环中调用:
process.nextTick(function () {
    console.log('nextTick callback!');
});
console.log('nextTick was set!');
```
用`Node`执行上面的代码`node test.js`，打印输出是：
```
nextTick was set!
nextTick callback!
```
说明传入`process.nextTick()`的函数不是立刻执行，而是要等到下一次事件循环。

`Node.js`进程本身的事件是由`process`对象来处理。如果我们响应`exit`事件，就可以在程序即将退出时执行某个回调函数：
```
// 程序即将退出时的回调函数:
process.on('exit', function (code) {
    console.log('about to exit with code: ' + code);
});
```
#### 判断JavaScript执行环境

有很多`JavaScript`代码既能在浏览器中执行，也能在`Node`环境执行，但有些时候，程序本身需要判断自己到底是在什么环境下执行的，常用的方式就是根据浏览器和`Node`环境提供的全局变量名称来判断：
```
if (typeof(window) === 'undefined') {
    console.log('node.js');
} else {
    console.log('browser');
}
```
