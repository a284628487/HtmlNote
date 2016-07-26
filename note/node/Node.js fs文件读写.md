# fs 文件读写

`Node.js`内置的`fs`模块就是文件系统模块，负责读写文件。和所有其它`JavaScript`模块不同的是，`fs`模块同时提供了异步和同步的方法。
什么是异步方法？因为`JavaScript`的单线程模型，执行IO操作时，`JavaScript`代码无需等待，而是传入回调函数后，继续执行后续`JavaScript`代码。比如`jQuery`提供的`getJSON()`操作：
```
$.getJSON('http://example.com/ajax', function (data) {
    console.log('IO结果返回后执行...');
});
console.log('不等待IO结果直接执行后续代码...');
```
而同步的IO操作则需要等待函数返回：
```
// 根据网络耗时，函数将执行几十毫秒~几秒不等:
var data = getJSONSync('http://example.com/ajax');
```
同步操作的好处是代码简单，缺点是程序将等待IO操作，在等待时间内，无法响应其它任何事件。而异步读取不用等待IO操作，但代码较麻烦。

#### 异步读文件文件

按照`JavaScript`的标准，异步读取一个文本文件的代码如下：
```
'use strict';
var fs = require('fs');
fs.readFile('sample.txt', 'utf-8', function (err, data) {
    if (err) {
        console.log(err);
    } else {
        console.log(data);
    }
});
```
当正常读取时，err参数为null，`data`参数为读取到的`String`。当读取发生错误时，err参数代表一个错误对象，`data`为`undefined`。这也是`Node.js`标准的回调函数：第一个参数代表错误信息，第二个参数代表结果。

#### 异步读二进制文件
```
'use strict';
var fs = require('fs');
fs.readFile('sample.png', function (err, data) {
    if (err) {
        console.log(err);
    } else {
        console.log(data);
        console.log(data.length + ' bytes');
    }
});
```
当读取二进制文件时，不传入文件编码时，回调函数的`data`参数将返回一个`Buffer`对象。在`Node.js`中，`Buffer`对象是一个包含零个或任意个字节的数组（注意和Array不同）。

#### `Buffer`对象和`String`转换：
```
// Buffer -> String
var text = data.toString('utf-8');
console.log(text);

// String -> Buffer
var buf = new Buffer(text, 'utf-8');
console.log(buf);
```

#### 同步读文件

除了标准的异步读取模式外，`fs`也提供相应的同步读取函数。同步读取的函数和异步函数相比，多了一个`Sync`后缀，并且不接收回调函数，函数直接返回结果。
```
'use strict';
var fs = require('fs');
var data = fs.readFileSync('sample.txt', 'utf-8');
console.log(data);
```
原异步调用的回调函数的`data`被函数直接返回，函数名需要改为`readFileSync`，其它参数不变。
如果同步读取文件发生错误，则需要用`try...catch`捕获该错误：
```
try {
    var data = fs.readFileSync('sample.txt', 'utf-8');
    console.log(data);
} catch (err) {
    // 出错了
}
```

#### 写文件

将数据写入文件是通过`fs.writeFile()`实现的：
```
'use strict';
var fs = require('fs');
var data = 'Hello, Node.js';
fs.writeFile('output.txt', data, function (err) {
    if (err) {
        console.log(err);
    } else {
        console.log('ok.');
    }
});
```
`writeFile()`的参数依次为文件名、数据和回调函数。如果传入的数据是`String`，默认按`UTF-8`编码写入文本文件，如果传入的参数是`Buffer`，则写入的是二进制文件。回调函数由于只关心成功与否，因此只有一个`err`参数。
和`readFile()`类似，`writeFile()`也有一个同步方法，叫`writeFileSync()`：
```
'use strict';
var fs = require('fs');
var data = 'Hello, Node.js';
fs.writeFileSync('output.txt', data);
```

#### Stat

如果我们要获取文件大小，创建时间等信息，可以使用`fs.stat()`，它返回一个`Stat`对象，能告诉我们文件或目录的详细信息：
```
'use strict';
var fs = require('fs');
fs.stat('sample.txt', function (err, stat) {
    if (err) {
        console.log(err);
    } else {
        // 是否是文件:
        console.log('isFile: ' + stat.isFile());
        // 是否是目录:
        console.log('isDirectory: ' + stat.isDirectory());
        if (stat.isFile()) {
            // 文件大小:
            console.log('size: ' + stat.size);
            // 创建时间, Date对象:
            console.log('birth time: ' + stat.birthtime);
            // 修改时间, Date对象:
            console.log('modified time: ' + stat.mtime);
        }
    }
});
```
stat()也有一个对应的同步函数statSync()

#### 异步还是同步

在`fs`模块中，提供同步方法是为了方便使用。那我们到底是应该用异步方法还是同步方法呢？
由于`Node`环境执行的`JavaScript`代码是服务器端代码，所以，绝大部分需要在服务器运行期反复执行业务逻辑的代码，必须使用异步代码，否则，同步代码在执行时期，服务器将停止响应，因为`JavaScript`只有一个执行线程。
服务器启动时如果需要读取配置文件，或者结束时需要写入到状态文件时，可以使用同步代码，因为这些代码只在启动和结束时执行一次，不影响服务器正常运行时的异步执行。
