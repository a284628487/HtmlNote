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

#### 全局对象与全局变量
`global`最根本的作用是作为全局变量的宿主。按照`ECMAScript`的定义，满足以下条件的变量是全局变量：
- 在最外层定义的变量；
- 全局对象的属性；
- 隐式定义的变量（未定义直接赋值的变量）。

当定义一个全局变量时，这个变量同时也会成为全局对象的属性，反之亦然。需要注意的是，在`Node.js`中不可能在最外层定义变量，因为所有用户代码都是属于当前模块的，而模块本身不是最外层上下文。
注意：永远使用`var`定义变量以避免引入全局变量，因为全局变量会污染命名空间，提高代码的耦合风险。

#### __filename
`__filename`表示当前正在执行的脚本的文件名。它将输出文件所在位置的绝对路径，且和命令行参数所指定的文件名不一定相同。如果在模块中，返回的值是模块文件的路径。

main.js：
```
// 输出全局变量 __filename 的值
console.log( __filename ); // 输出main.js的路径
```

#### __dirname
`__dirname`表示当前执行脚本所在的目录。

main.js
```
// 输出全局变量 __dirname 的值
console.log( __dirname );
```

#### clearTimeout(t)
`clearTimeout(t)`全局函数用于停止一个之前通过`setTimeout()`创建的定时器。 参数`t`是通过`setTimeout()`函数创建的计算器。
```
function printHello(){
   console.log( "Hello, World!");
}
// 两秒后执行以上函数
var t = setTimeout(printHello, 2000);
// 清除定时器
clearTimeout(t);
```
类似的还有`setInterval(cb, ms)`和`clearInterval(t)`;

#### console

1 console.log([data][, ...])
向标准输出流打印字符并以换行符结束。该方法接收若干 个参数，如果只有一个参数，则输出这个参数的字符串形式。如果有多个参数，则 以类似于C 语言 printf() 命令的格式输出。
2 console.info([data][, ...])
P该命令的作用是返回信息性消息，这个命令与console.log差别并不大，除了在chrome中只会输出文字外，其余的会显示一个蓝色的惊叹号。
3 console.error([data][, ...])
输出错误消息的。控制台在出现错误时会显示是红色的叉子。
4 console.warn([data][, ...])
输出警告消息。控制台出现有黄色的惊叹号。
5 console.dir(obj[, options])
用来对一个对象进行检查（inspect），并以易于阅读和打印的格式显示。
6 console.time(label)
输出时间，表示计时开始。
7 console.timeEnd(label)
结束时间，表示计时结束。
8 console.trace(message[, ...])
当前执行的代码在堆栈中的调用路径，这个测试函数运行很有帮助，只要给想测试的函数里面加入 console.trace 就行了。
9 console.assert(value[, message][, ...])
用于判断某个表达式或变量是否为真，接手两个参数，第一个参数是表达式，第二个参数是字符串。只有当第一个参数为false，才会输出第二个参数，否则不会有任何结果。

`console.log()`向标准输出流打印字符并以换行符结束。
`console.log`接受若干个参数，如果只有一个参数，则输出这个参数的字符串形式。如果有多个参数，则 以类似于C 语言 printf() 命令的格式输出。

#### process
`process`是一个全局变量，即`global`对象的属性。它用于描述当前`Node.js`进程状态的对象，提供了一个与操作系统的简单接口。

1 exit
当进程准备退出时触发。
2 beforeExit
当 node 清空事件循环，并且没有其他安排时触发这个事件。通常来说，当没有进程安排时 node 退出，但是 'beforeExit' 的监听器可以异步调用，这样 node 就会继续执行。
3 uncaughtException
当一个异常冒泡回到事件循环，触发这个事件。如果给异常添加了监视器，默认的操作（打印堆栈跟踪信息并退出）就不会发生。
4 Signal 事件
当进程接收到信号时就触发。信号列表详见标准的 POSIX 信号名，如 SIGINT、SIGUSR1 等。

test.js:
```
process.on('exit', function(code) {
  // 以下代码永远不会执行
  setTimeout(function() {
    console.log("该代码不会执行");
  }, 0);
  
  console.log('退出码为:', code);
});
console.log("程序执行结束");
```
[更多](http://www.runoob.com/nodejs/nodejs-global-object.html)

