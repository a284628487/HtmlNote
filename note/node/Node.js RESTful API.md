# Node.js RESTful API

`REST`即表述性状态传递（英文：`Representational State Transfer`，简称REST）是`Roy Fielding`博士在2000年他的博士论文中提出来的一种软件架构风格。

表述性状态转移是一组架构约束条件和原则。满足这些约束条件和原则的应用程序或设计就是`RESTful`。需要注意的是，`REST`是设计风格而不是标准。`REST`通常基于使用`HTTP`，`URI`，和`XML`以及`HTML`（标准通用标记语言下的一个应用）这些现有的广泛流行的协议和标准。`REST`通常使用`JSON`数据格式。

#### RESTful Web Services

`Web service`是一个平台独立的，低耦合的，自包含的、基于可编程的`web`的应用程序，可使用开放的`XML`（标准通用标记语言下的一个子集）标准来描述、发布、发现、协调和配置这些应用程序，用于开发分布式的互操作的应用程序。

基于`REST`架构的`Web Services`即是`RESTful`。

由于轻量级以及通过`HTTP`直接传输数据的特性，`Web`服务的`RESTful`方法已经成为最常见的替代方法。可以使用各种语言（比如 Java 程序、Perl、Ruby、Python、PHP 和 Javascript[包括 Ajax]）实现客户端。

`RESTful Web` 服务通常可以通过自动客户端或代表用户的应用程序访问。但是，这种服务的简便性让用户能够与之直接交互，使用它们的`Web` 浏览器构建一个`GET URL`并读取返回的内容。

#### 创建 RESTful

创建`users.json`

```
{
   "user1" : {
      "name" : "mahesh",
	  "password" : "password1",
	  "profession" : "teacher",
	  "id": 1
   },
   "user2" : {
      "name" : "suresh",
	  "password" : "password2",
	  "profession" : "librarian",
	  "id": 2
   },
   "user3" : {
      "name" : "ramesh",
	  "password" : "password3",
	  "profession" : "clerk",
	  "id": 3
   }
}
```
基于以上数据，我们创建以下`RESTful API`：</br>
1	listUsers	GET	空	显示所有用户列表</br>
2	addUser	POST	JSON 字符串	添加新用户</br>
3	deleteUser	DELETE	JSON 字符串	删除用户</br>
4	id	GET	空	显示用户详细信息

#### listUsers
```
var express = require('express');
var app = express();
var fs = require("fs");

app.get('/listUsers', function (req, res) {
   fs.readFile( __dirname + "/" + "users.json", 'utf8', function (err, data) {
       console.log( data );
       res.end( data );
   });
})

var server = app.listen(8081, function () {
  var host = server.address().address
  var port = server.address().port
  console.log("应用实例，访问地址为 http://%s:%s", host, port)
})
```

#### addUser
```
var express = require('express');
var app = express();
var fs = require("fs");

//添加的新用户数据
var user = {
   "user4" : {
      "name" : "mohit",
      "password" : "password4",
      "profession" : "teacher",
      "id": 4
   }
}

app.get('/addUser', function (req, res) {
   // 读取已存在的数据
   fs.readFile( __dirname + "/" + "users.json", 'utf8', function (err, data) {
       data = JSON.parse( data );
       data["user4"] = user["user4"];
       console.log(data);
       res.end(JSON.stringify(data));
   });
})
```

#### id
```
var express = require('express');
var app = express();
var fs = require("fs");

app.get('/:id', function (req, res) {
   // 首先我们读取已存在的用户
   fs.readFile(__dirname + "/" + "users.json", 'utf8', function (err, data) {
       data = JSON.parse(data);
       var user = data["user" + req.params.id];
       console.log(user);
       res.end(JSON.stringify(user));
   });
})
```

#### deleteUser

```
var express = require('express');
var app = express();
var fs = require("fs");

var id = 2;

app.get('/deleteUser', function (req, res) {
   // First read existing users.
   fs.readFile( __dirname + "/" + "users.json", 'utf8', function (err, data) {
       data = JSON.parse( data );
       delete data["user" + 2];
       
       console.log( data );
       res.end(JSON.stringify(data));
   });
})
```
