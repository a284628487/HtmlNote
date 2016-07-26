# Node.js 事件循环

`Node.js` 是单进程单线程应用程序，但是通过事件和回调支持并发，所以性能非常高。
`Node.js` 的每一个`API`都是异步的，并作为一个独立线程运行，使用异步函数调用，并处理并发。
`Node.js` 基本上所有的事件机制都是用设计模式中观察者模式实现。
`Node.js` 单线程类似进入一个while(true)的事件循环，直到没有事件观察者退出，每个异步事件都生成一个事件观察者，如果有事件发生就调用该回调函数

#### 事件驱动程序
`Node.js` 使用事件驱动模型，当`web server`接收到请求，然后进行处理，然后去服务下一个web请求。当这个请求完成，它被放回处理队列，当到达队列开头，这个结果被返回给用户。这个模型非常高效可扩展性非常强，因为`webserver`一直接受请求而不等待任何读写操作。（这也被称之为非阻塞式IO或者事件驱动IO）在事件驱动模型中，会生成一个主循环来监听事件，当检测到事件时触发回调函数。

EventEmitters -> Events (Event Loop) -> Event Handlers

整个事件驱动的流程就是这么实现的，非常简洁。有点类似于观察者模式，事件相当于一个主题(Subject)，而所有注册到这个事件上的处理函数相当于观察者(Observer)。

`Node.js`有多个内置的事件，我们可以通过引入`events`模块，并通过实例化`EventEmitter`类来绑定和监听事件，如下实例：
```
// 引入 events 模块
var events = require('events');
// 创建 eventEmitter 对象
var eventEmitter = new events.EventEmitter();
// 绑定事件及事件的处理程序
eventEmitter.on('eventName', eventHandler);
// 触发事件
eventEmitter.emit('eventName');
```
#### 实例

创建`main.js`文件
```
// 引入 events 模块
var events = require('events');
// 创建 eventEmitter 对象
var eventEmitter = new events.EventEmitter();

// 创建事件处理程序
var connectHandler = function connected() {
   console.log('连接成功。');
   // 触发 data_received 事件 
   eventEmitter.emit('data_received');
}

// 绑定 connection 事件处理程序
eventEmitter.on('connection', connectHandler);
 
// 使用匿名函数绑定 data_received 事件
eventEmitter.on('data_received', function(){
   console.log('数据接收成功。');
});

// 触发 connection 事件 
eventEmitter.emit('connection');

console.log("程序执行完毕。");
```
执行程序
```
$ node main.js
连接成功。
数据接收成功。
程序执行完毕。
```
#### Node 应用程序是如何工作的？
在`Node`应用程序中，执行异步操作的函数将回调函数作为最后一个参数， 回调函数接收错误对象作为第一个参数。

### EventEmitter

`Node.js`所有的异步`I/O`操作在完成时都会发送一个事件到事件队列。`Node.js`里面的许多对象都会分发事件：一个`net.Server`对象会在每次有新连接时分发一个事件， 一个`fs.readStream`对象会在文件被打开的时候发出一个事件。 所有这些产生事件的对象都是`events.EventEmitter`的实例。

`events`模块只提供了一个对象：`events.EventEmitter`。`EventEmitter`的核心就是事件触发与事件监听器功能的封装。
```
// 引入 events 模块
var events = require('events');
// 创建 eventEmitter 对象
var eventEmitter = new events.EventEmitter();
```

`EventEmitter`对象如果在实例化时发生错误，会触发 'error' 事件。当添加新的监听器时，'newListener' 事件会触发，当监听器被移除时，'removeListener' 事件被触发。
`EventEmitter`提供了多个属性，如`on`和`emit`。`on`函数用于绑定事件函数，`emit`属性用于触发一个事件。

event.js 文件:
```
var EventEmitter = require('events').EventEmitter; 
var event = new EventEmitter(); 
event.on('some_event', function() { 
    console.log('some_event_'); 
}); 
setTimeout(function() { 
    event.emit('some_event'); 
}, 1000); 
console.log('before...');
```
执行结果:
```
$ node event.js 
before...
some_event_
```

`EventEmitter`的每个事件由一个事件名和若干个参数组成，事件名是一个字符串，对于每个事件，`EventEmitter`支持若干个事件监听器，先注册的监听器先执行；

#### EventEmitter常用属性或方法

- addListener(event, listener)    为指定事件添加一个监听器到监听器数组的尾部。
- on(event, listener)     为指定事件注册一个监听器，接受一个字符串 event 和一个回调函数。
- once(event, listener)     为指定事件注册一个单次监听器。
- removeListener(event, listener)       移除指定事件的某个监听器，监听器 必须是该事件已经注册过的监听器。
- removeAllListeners([event])       移除所有事件的所有监听器，如果指定事件，则移除指定事件的所有监听器。
- setMaxListeners(n)    默认情况下，如果添加的监听器超过10个就会输出警告信息，setMaxListeners函数用于提高监听器的默认限制的数量。
- listeners(event)    返回指定事件的监听器数组。
- emit(event, [arg1], [arg2], [...])    按参数的顺序执行每个监听器，如果事件有注册监听返回true，否则返回false。

类方法
- listenerCount(emitter, event)     返回指定事件的监听器数量。

事件
- newListener
```
event - 字符串，事件名称
listener - 处理事件函数
```
该事件在添加新监听器时被触发。
- removeListener
```
event - 字符串，事件名称
listener - 处理事件函数
```
从指定监听器数组中删除一个监听器。需要注意的是，此操作将会改变处于被删监听器之后的那些监听器的索引。

#### error 事件
`EventEmitter`定义了一个特殊的事件`error`，它包含了错误的语义，我们在遇到 异常的时候通常会触发`error`事件。当`error`被触发时，`EventEmitter`规定如果没有响应的监听器，`Node.js`会把它当作异常，退出程序并输出错误信息。
我们一般会为会触发`error`事件的对象设置监听器，避免遇到错误后整个程序崩溃。例如：
```
var events = require('events'); 
var emitter = new events.EventEmitter(); 
emitter.emit('error');
```
运行时会显示以下错误：
```
node.js:201 
throw e; // process.nextTick error, or 'error' event on first tick 
^ 
Error: Uncaught, unspecified 'error' event. 
...
```

#### 继承EventEmitter

大多数时候我们不会直接使用`EventEmitter`，而是在对象中继承它。包括`fs、net、 http`在内的，只要是支持事件响应的核心模块都是`EventEmitter`的子类。
为什么要这样做呢？原因有两点：
首先，具有某个实体功能的对象实现事件符合语义，事件的监听和发射应该是一个对象的方法。
其次，`JavaScript`的对象机制是基于原型的，支持 部分多重继承，继承`EventEmitter`不会打乱对象原有的继承关系。

