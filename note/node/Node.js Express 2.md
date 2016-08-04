[Express-Guide](http://wiki.jikexueyuan.com/project/express-guide/)

## 路由

Express 定义了如下和 HTTP 请求对应的路由方法：
get、post、put、head、delete、options。有些路由方法名不是合规的 JavaScript 变量名，此时使用括号记法，比如 `app['m-search']('/', function ...`

### app.all()
app.all() 是一个特殊的路由方法，没有任何 HTTP 方法与其对应，它的作用是对于一个路径上的所有请求加载中间件。

```
app.all('/abc', function(req, resp, next) {
	console.log('all/');
	next(); // 交给下一个句柄处理
});
```
所有来自 `/abc` 的请求，不管使用 GET、POST、PUT、DELETE 或其他任何 http 模块支持的 HTTP 请求，句柄都会得到执行。

### 路由路径
路由路径和请求方法一起定义了请求的端点，它可以是
- 字符串
- 字符串模式
- 正则表达式

Express 使用 [path-to-regexp](https://www.npmjs.com/package/path-to-regexp)匹配路由路径。[Express Route Tester](http://forbeslindesay.github.io/express-route-tester/)是测试基本 Express 路径的好工具，但不支持模式匹配。

##### 字符串模式示例
```
// 匹配 acd 和 abcd
app.get('/ab?cd', function(req, res) {
  res.send('ab?cd');
});

// 匹配 abcd、abbcd、abbbcd等
app.get('/ab+cd', function(req, res) {
  res.send('ab+cd');
});

// 匹配 abcd、abxcd、abRABDOMcd、ab123cd等
app.get('/ab*cd', function(req, res) {
  res.send('ab*cd');
});

// 匹配 /abe 和 /abcde
app.get('/ab(cd)?e', function(req, res) {
 res.send('ab(cd)?e');
});
```
字符 ?、+、* 和 () 是正则表达式的子集，- 和 . 在基于字符串的路径中按照字面值解释。

##### 正则表达式示例
```
// 匹配任何路径中含有 a 的路径：
app.get(/a/, function(req, res) {
  res.send('/a/');
});

// 匹配 butterfly、dragonfly，不匹配 butterflyman、dragonfly man等
app.get(/.*fly$/, function(req, res) {
  res.send('/.*fly$/');
});
```

### 路由句柄
可以为请求处理提供多个回调函数，其行为类似`中间件`。唯一的区别是这些回调函数有可能调用`next('route')`方法而略过其他路由回调函数。可以利用该机制为路由定义前提条件，如果在现有路径上继续执行没有意义，则可将控制权交给剩下的路径。

路由句柄有多种形式，可以是一个函数、一个函数数组，或者是两者混合，

##### 多个回调函数处理路由（记得指定 next 对象）：
```
app.get('/example/b', function (req, res, next) {
  console.log('response will be sent by the next function ...');
  next();
}, function (req, res) {
  res.send('Hello from B!');
});
```
##### 使用回调函数数组处理路由：
```
var cb0 = function (req, res, next) {
  console.log('CB0');
  next();
}

var cb1 = function (req, res, next) {
  console.log('CB1');
  next();
}

app.get('/example/c', [cb0, cb1]);
```

##### 混合使用函数和函数数组处理路由：
```
var cb0 = function (req, res, next) {
  console.log('CB0');
  next();
}

var cb1 = function (req, res, next) {
  console.log('CB1');
  next();
}

app.get('/example/d', [cb0, cb1], function (req, res, next) {
  console.log('response will be sent by the next function ...');
  next();
}, function (req, res) {
  res.send('Hello from D!');
});
```

### 响应方法
响应对象（res）的方法向客户端返回响应，终结请求响应的循环。如果在路由句柄中一个方法也不调用，来自客户端的请求会一直挂起。
```
res.download()	提示下载文件。
res.end()	终结响应。
res.json()	发送一个 JSON 响应。
res.jsonp()	发送一个支持 JSONP 的 JSON 响应。
res.redirect()	重定向请求。
res.render()	渲染视图模板。
res.send()	发送各种类型的响应。
res.sendFile	以八位字节流的形式发送文件。
res.sendStatus()	设置响应状态代码，并将其以字符串形式作为响应体的一部分发送。
```

### app.route()
可使用`app.route()`创建路由路径的链式路由句柄。由于路径在一个地方指定，这样做有助于创建模块化的路由，而且减少了代码冗余和拼写错误。[Router](http://expressjs.com/4x/api.html#router)

##### 示例使用 app.route() 定义了链式路由句柄。
```
app.route('/book')
  .get(function(req, res) {
    res.send('Get a random book');
  })
  .post(function(req, res) {
    res.send('Add a book');
  })
  .put(function(req, res) {
    res.send('Update the book');
  });
```

### express.Router
可使用`express.Router`类创建模块化、可挂载的路由句柄。`Router`实例是一个完整的中间件和路由系统，因此常称其为一个 "mini-app"。

下面的实例程序创建了一个路由模块，并加载了一个中间件，定义了一些路由，并且将它们挂载至应用的路径上。

1.在 app 目录下创建名为 routeExample.js 的文件
```
var express = require('express');
var router = express.Router();

// 该路由使用的中间件
router.use(function timeLog(req, res, next) {
  console.log('Time: ', Date.now());
  next();
});
// 定义主页路由
router.get('/', function(req, res) {
  res.send('Birds home page');
});
// 定义 about 路由
router.get('/about', function(req, res) {
  res.send('About birds');
});

module.exports = router;
```
2.然后在应用中加载路由模块：
```
var routeExample = require('./routeExample');
...
app.use('/routeExample', routeExample);
```
应用即可处理发自`/routeExample`和`/routeExample/about`的请求，并且调用为该路由指定的 timeLog 中间件。

## 中间件
中间件是一个可访问请求对象（req）和响应对象（res）的函数，在 Express 应用的请求-响应循环里，下一个内联的中间件通常用变量 next 表示。中间件的功能包括：
- 执行任何代码。
- 修改请求和响应对象。
- 终结请求-响应循环。
- 调用堆栈中的下一个中间件。

在`Express`应用的请求-响应循环里，下一个内联的中间件通常用变量 `next` 表示。
如果当前中间件没有终结请求-响应循环，则必须调用`next()`方法将控制权交给下一个中间件，否则请求就会挂起。

#### 应用级中间件
应用级中间件绑定到 express 实例，使用 app.use() 和 app.VERB()。
```
var app = express();

// 没有挂载路径的中间件，应用的每个请求都会执行该中间件
app.use(function (req, res, next) {
  console.log('Time:', Date.now());
  next();
});

// 挂载至 /user/:id 的中间件，任何指向 /user/:id 的请求都会执行它
app.use('/user/:id', function (req, res, next) {
  console.log('Request Type:', req.method);
  next();
});

// 路由和句柄函数(中间件系统)，处理指向 /user/:id 的 GET 请求
app.get('/user/:id', function (req, res, next) {
  res.send('USER');
});
```
在一个挂载点装载一组中间件。
```
// 一个中间件栈，对任何指向 /user/:id 的 HTTP 请求打印出相关信息
app.use('/user/:id', function(req, res, next) {
  console.log('Request URL:', req.originalUrl);
  next();
}, function (req, res, next) {
  console.log('Request Type:', req.method);
  next();
});
```
作为中间件系统的路由句柄，使得为路径定义多个路由成为可能。在下面的例子中，为指向 /user/:id 的 GET 请求定义了两个路由。第二个路由虽然不会带来任何问题，但却永远不会被调用，因为第一个路由已经终止了请求-响应循环。
```
// 一个中间件栈，处理指向 /user/:id 的 GET 请求
app.get('/user/:id', function (req, res, next) {
  console.log('ID:', req.params.id);
  next();
}, function (req, res, next) {
  res.send('User Info');
});

// 处理 /user/:id， 打印出用户 id
app.get('/user/:id', function (req, res, next) {
  res.end(req.params.id);
});
```
如果需要在中间件栈中跳过剩余中间件，调用 next('route') 方法将控制权交给下一个路由。需要注意的是 next('route') 只对使用 app.VERB() 或 router.VERB() 加载的中间件有效。
```
// 一个中间件栈，处理指向 /user/:id 的 GET 请求
app.get('/user/:id', function (req, res, next) {
  // 如果 user id 为 0, 跳到下一个路由
  if (req.params.id == 0) next('route');
  // 负责将控制权交给栈中下一个中间件
  else next(); //
}, function (req, res, next) {
  // 渲染常规页面
  res.render('regular');
});

// 处理 /user/:id， 渲染一个特殊页面
app.get('/user/:id', function (req, res, next) {
  res.render('special');
});
```
#### 路由级中间件
路由级中间件和应用级中间件一样，只是它绑定的对象为 express.Router()。
```
var router = express.Router();
```
路由级使用 router.use() 或 router.VERB() 加载。
上述在应用级创建的中间件系统，可通过如下代码改写为路由级：

```
var app = express();
var router = express.Router();

// 没有挂载路径的中间件，通过该路由的每个请求都会执行该中间件
router.use(function (req, res, next) {
  console.log('Time:', Date.now());
  next();
});

// 一个中间件栈，显示任何指向 /user/:id 的 HTTP 请求的信息
router.use('/user/:id', function(req, res, next) {
  console.log('Request URL:', req.originalUrl);
  next();
}, function (req, res, next) {
  console.log('Request Type:', req.method);
  next();
});

// 一个中间件栈，处理指向 /user/:id 的 GET 请求
router.get('/user/:id', function (req, res, next) {
  // 如果 user id 为 0, 跳到下一个路由
  if (req.params.id == 0) next('route');
  // 负责将控制权交给栈中下一个中间件
  else next(); //
}, function (req, res, next) {
  // 渲染常规页面
  res.render('regular');
});

// 处理 /user/:id， 渲染一个特殊页面
router.get('/user/:id', function (req, res, next) {
  console.log(req.params.id);
  res.render('special');
});

// 将路由挂载至应用
app.use('/', router);
```
#### 错误处理中间件
错误处理中间件有 4 个参数，定义错误处理中间件时必须使用这 4 个参数。即使不需要 next 对象，也必须在签名中声明它，否则中间件会被识别为一个常规中间件，不能处理错误。
错误处理中间件和其他中间件定义类似，只是要使用 4 个参数
```
app.use(function(err, req, res, next) {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
```
在其他 app.use() 和路由调用后，最后定义错误处理中间件，比如：
```
var bodyParser = require('body-parser');
var methodOverride = require('method-override');

app.use(bodyParser());
app.use(methodOverride());
app.use(function(err, req, res, next) {
  // 业务逻辑
});
```
中间件返回的响应是随意的，可以响应一个 HTML 错误页面、一句简单的话、一个 JSON 字符串，或者其他任何您想要的东西。
为了便于组织（更高级的框架），可能会像定义常规中间件一样，定义多个错误处理中间件。比如为使用 XHR 的请求定义一个，还想为没有使用的定义一个，那么：
```
var bodyParser = require('body-parser');
var methodOverride = require('method-override');

app.use(bodyParser());
app.use(methodOverride());
app.use(logErrors);
app.use(clientErrorHandler);
app.use(errorHandler);
```
`logErrors` 将请求和错误信息写入标准错误输出、日志或类似服务：
```
function logErrors(err, req, res, next) {
  console.error(err.stack);
  next(err);
}
```
`clientErrorHandler` 的定义如下（注意这里将错误直接传给了 next）：
```
function clientErrorHandler(err, req, res, next) {
  if (req.xhr) {
    res.status(500).send({ error: 'Something blew up!' });
  } else {
    next(err);
  }
}
```
`errorHandler` 能捕获所有错误，其定义如下：
```
function errorHandler(err, req, res, next) {
  res.status(500);
  res.render('error', { error: err });
}
```
如果向 next() 传入参数（除了 'route' 字符串），Express 会认为当前请求有错误的输出，因此跳过后续其他非错误处理和路由/中间件函数。如果需做特殊处理，需要创建新的错误处理路由，如下所示。

如果路由句柄有多个回调函数，可使用 'route' 参数跳到下一个路由句柄。比如：
```
app.get('/a_route_behind_paywall',
  function checkIfPaidSubscriber(req, res, next) {
    if(!req.user.hasPaid) {
      // 继续处理该请求
      next('route');
    }
  }, function getPaidContent(req, res, next) {
    PaidContent.find(function(err, doc) {
      if(err) return next(err);
      res.json(doc);
    });
  });
```
在这个例子中，句柄 getPaidContent 会被跳过，但 app 中为 /a_route_behind_paywall 定义的其他句柄则会继续执行。
next() 和 next(err) 类似于 Promise.resolve() 和 Promise.reject()。它们让您可以向 Express 发信号，告诉它当前句柄执行结束并且处于什么状态。next(err) 会跳过后续句柄，除了那些用来处理错误的句柄。
#### 第三方中间件
Express 是一款提供路由和中间件的 Web 框架，但其本身的功能却异常精简。Express 应用的功能通过第三方中间件来添加。
安装所需功能的 node 模块，并在应用中加载，可以在应用级加载，也可以在路由级加载。

下面的例子安装并加载了一个解析 cookie 的中间件： cookie-parser
```
$ npm install cookie-parser
var express = require('express');
var app = express();
var cookieParser = require('cookie-parser');

// 加载 cookie 解析中间件
app.use(cookieParser());
```
参考[Third-party middleware](http://expressjs.com/resources/middleware.html) 获取 Express 中经常用到的第三方中间件列表。

## 调试 Express
Express 内部使用 debug 模块记录路由匹配、使用到的中间件、应用模式以及请求-响应循环。

debug 有点像改装过的 console.log，不同的是，不需要在生产代码中注释掉 debug。它会默认关闭，而且使用一个名为 DEBUG 的环境变量还可以打开。
在启动应用时，设置 DEBUG 环境变量为 express:*，可以查看 Express 中用到的所有内部日志。
```
$ DEBUG=express:* node index.js
```
在 Windows 系统里，使用相应的命令。
```
> set DEBUG=express:* & node index.js
```

## 数据库集成
[Link](http://wiki.jikexueyuan.com/project/express-guide/database-integration.html)
