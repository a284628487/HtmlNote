# node.js http

#### HTTP服务器

要开发`HTTP`服务器程序，从头处理`TCP`连接，解析`HTTP`是不现实的。这些工作实际上已经由`Node.js`自带的`http`模块完成了。应用程序并不直接和`HTTP`协议打交道，而是操作`http`模块提供的`request`和`response`对象。

- request: 封装了`HTTP`请求，我们调用`request`对象的属性和方法就可以拿到所有`HTTP`请求的信息；
- response: 封装了`HTTP`响应，我们操作`response`对象的方法，就可以把`HTTP`响应返回给浏览器。

下面是一个简单的服务器`web.js`:
```
'use strict';
// 导入http模块:
var http = require('http');
// 创建http server，并传入回调函数:
var server = http.createServer(function (request, response) {
    // 回调函数接收request和response对象,
    // 获得HTTP请求的method和url:
    console.log(request.method + ': ' + request.url);
    // 将HTTP响应200写入response, 同时设置Content-Type: text/html:
    response.writeHead(200, {'Content-Type': 'text/html'});
    // 将HTTP响应的HTML内容写入response:
    response.end('<h1>Hello world!</h1>');
});

// 让服务器监听8080端口:
server.listen(8080);
console.log('Server is running at http://127.0.0.1:8080/');
```
在命令提示符下运行该程序，可以看到以下输出：
```
$ node web.js 
Server is running at http://127.0.0.1:8080/
```

#### 文件服务器

让我们继续扩展上面的`Web`程序。我们可以设定一个目录，然后让`Web`程序变成一个文件服务器。要实现这一点，我们只需要解析`request.url`中的路径，然后在本地找到对应的文件，把文件内容发送出去就可以了。

解析`URL`需要用到`Node.js`提供的`url`模块，它使用起来非常简单，通过`parse()`将一个字符串解析为一个`Url`对象：

```
'use strict';
var url = require('url');
console.log(url.parse('http://user:pass@host.com:8080/path/to/file?query=string#hash'));
```
结果如下：
```
Url {
  protocol: 'http:',
  slashes: true,
  auth: 'user:pass',
  host: 'host.com:8080',
  port: '8080',
  hostname: 'host.com',
  hash: '#hash',
  search: '?query=string',
  query: 'query=string',
  pathname: '/path/to/file',
  path: '/path/to/file?query=string',
  href: 'http://user:pass@host.com:8080/path/to/file?query=string#hash' 
}
```

处理本地文件目录需要使用`Node.js`提供的`path`模块，它可以方便地构造目录：
```
'use strict';
var path = require('path');
// 解析当前目录:
var workDir = path.resolve('.'); // '/Users/a284628487'
// 组合完整的文件路径:当前目录 + 'pub' + 'index.html':
var filePath = path.join(workDir, 'pub', 'index.html');
// '/Users/a284628487/pub/index.html'
```
使用`path`模块可以正确处理操作系统相关的文件路径。在`Windows`系统下，返回的路径类似于`C:\Users\a284628487\static\index.html`，这样，我们就不关心怎么拼接路径了。

最后，我们实现一个文件服务器file_server.js：
```
'use strict';

var
    fs = require('fs'),
    url = require('url'),
    path = require('path'),
    http = require('http');

// 从命令行参数获取root目录，默认是当前目录:
var root = path.resolve(process.argv[2] || '.');

console.log('Static root dir: ' + root);

// 创建服务器:
var server = http.createServer(function (request, response) {
    // 获得URL的path，类似 '/css/bootstrap.css':
    var pathname = url.parse(request.url).pathname;
    // 获得对应的本地文件路径，类似 '/srv/www/css/bootstrap.css':
    var filepath = path.join(root, pathname);
    // 获取文件状态:
    fs.stat(filepath, function (err, stats) {
        if (!err && stats.isFile()) {
            // 没有出错并且文件存在:
            console.log('200 ' + request.url);
            // 发送200响应:
            response.writeHead(200);
            // 将文件流导向response:
            fs.createReadStream(filepath).pipe(response);
        } else {
            // 出错了或者文件不存在:
            console.log('404 ' + request.url);
            // 发送404响应:
            response.writeHead(404);
            response.end('404 Not Found');
        }
    });
});

server.listen(8080);
console.log('Server is running at http://127.0.0.1:8080/');
```
注：没有必要手动读取文件内容。由于response对象本身是一个`Writable Stream`，直接用`pipe()`方法就实现了自动读取文件内容并输出到`HTTP`响应。
在命令行运行`node file_server.js /path/to/dir`，把`/path/to/dir`改成本地的一个有效的目录，然后在浏览器中输入`http://localhost:8080/index.html`
只要当前目录下存在文件`index.html`，服务器就可以把文件内容发送给浏览器。观察控制台输出：
```
200 /index.html
200 /css/uikit.min.css
200 /js/jquery.min.js
200 /fonts/fontawesome-webfont.woff2
```
第一个请求是浏览器请求`index.html`页面，后续请求是浏览器解析`HTML`后发送的其它资源请求。

#### GET/POST请求

- 获取`GET`请求内容

由于`GET`请求直接被嵌入在路径中，`URL`是完整的请求路径，包括了`?`后面的部分，因此你可以手动解析后面的内容作为`GET`请求的参数。
`node.js`中`url`模块中的`parse`函数提供了这个功能。
```
var http = require('http');
var url = require('url');
var util = require('util');

http.createServer(function(req, res){
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.end(util.inspect(url.parse(req.url, true)));
}).listen(3000);
```
在浏览器中访问`http://localhost:3000/user?name=w3c&email=w3c@w3cschool.cc`然后查看返回结果

- 获取`POST`请求内容

`POST`请求的内容全部的都在请求体中，`http.ServerRequest`并没有一个属性内容为请求体，原因是等待请求体传输可能是一件耗时的工作。比如上传文件，而很多时候我们可能并不需要理会请求体的内容，恶意的`POST`请求会大大消耗服务器的资源，所有`node.js`默认是不会解析请求体的，当你需要的时候，需要手动来做。

```
var http = require('http');
var querystring = require('querystring');
var util = require('util');

http.createServer(function(req, res){
    var post = ''; //定义了一个post变量，用于暂存请求体的信息

    req.on('data', function(chunk){ //通过req的data事件监听函数，每当接受到请求体的数据，就累加到post变量中
        post += chunk;
    });

    req.on('end', function(){ // 在end事件触发后，通过querystring.parse将post解析为真正的POST请求格式，然后向客户端返回。
        post = querystring.parse(post);
        res.end(util.inspect(post));
    });
}).listen(3000);
```

#### 补充内容

- 端口

端口的作用：通过端口来区分出同一电脑内不同应用或者进程，从而实现一条物理网线(通过分组交换技术-比如internet)同时链接多个程序。端口号是一个 16位的`uint`, 所以其范围为 1 to 65535 (对TCP来说, port 0 被保留，不能被使用. 对于UDP来说, source端的端口号是可选的，为0时表示无端口).
`app.listen(3000)`，进程就被打标，电脑接收到的3000端口的网络消息就会被发送给我们启动的这个进程；



