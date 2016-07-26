# Stream

`stream`是`Node.js`提供的一个仅在服务区端可用的模块，目的是支持“流”这种数据结构。`Stream`是一个抽象接口，`Node` 中有很多对象实现了这个接口。例如，对`http`服务器发起请求的`request`对象就是一个`Stream`，还有stdout（标准输出）。

什么是流？流是一种抽象的数据结构。我们可以把数据看成是数据流，比如你敲键盘的时候，就可以把每个字符依次连起来，看成字符流。这个流是从键盘输入到应用程序，实际上它还对应着一个名字：标准输入流（stdin）。如果应用程序把字符一个一个输出到显示器上，这也可以看成是一个流，这个流也有名字：标准输出流（stdout）。

流的特点是数据是有序的，而且必须依次读取，或者依次写入，不能像Array那样随机定位。

`Stream`有四种流类型：
- Readable : 可读操作。
- Writable : 可写操作。
- Duplex : 可读可写操作。
- Transform : 操作被写入数据，然后读出结果。

所有的`Stream`对象都是`EventEmitter`的实例。常用的事件有：
- data : 当有数据可读时触发。
- end : 没有更多的数据可读时触发。
- error : 在接收和写入过程中发生错误时触发。
- finish : 所有数据已被写入到底层系统时触发。

#### 读文件
```
'use strict';
var fs = require('fs');
var data = '';

// 打开一个流:
var rs = fs.createReadStream('sample.txt', 'utf-8');

rs.on('data', function (chunk) {
    console.log('DATA:')
    console.log(chunk);
    data += chunk;
});

rs.on('end', function () {
    console.log('END');
    console.log(data);
});

rs.on('error', function (err) {
    console.log('ERROR: ' + err);
});
```
需要注意的是`data`事件可能会有多次，每次传递的`chunk`是流的一部分数据。

#### 写文件
要以流的形式写入文件，只需要不断调用`write()`方法，最后以`end()`结束：
```
'use strict';
var fs = require('fs');
// 创建写入流，写入到文件output1.txt
var ws1 = fs.createWriteStream('output1.txt', 'utf-8');
ws1.write('使用Stream写入文本数据...\n');
ws1.write('END.');
ws1.end(); // 标记文件末尾

var ws2 = fs.createWriteStream('output2.txt');
ws2.write(new Buffer('使用Stream写入二进制数据...\n', 'utf-8'));
ws2.write(new Buffer('END.', 'utf-8'));
ws2.end();

// 绑定事件
ws1.on('finish', function(){
})
ws1.on('error', function(){
})
```

##### 所有可以读取数据的流都继承自`stream.Readable`，所有可以写入的流都继承自`stream.Writable`。

#### pipe 管道流

通常用于从一个流中获取数据并将数据传递到另外一个流中。一个`Readable`流和一个`Writable`流串起来后，所有的数据自动从`Readable`流进入`Writable`流，这种操作叫`pipe`。在`Node.js`中，`Readable`流有一个`pipe()`方法，就是用来干这件事的。

让我们用`pipe()`把一个文件流和另一个文件流串起来，这样源文件的所有数据就自动写入到目标文件里了，所以，这实际上是一个复制文件的程序：
```
'use strict';

var fs = require('fs');

var rs = fs.createReadStream('sample.txt');
var ws = fs.createWriteStream('copied.txt');

rs.pipe(ws);
```
默认情况下，当`Readable`流的数据读取完毕，`end`事件触发后，将自动关闭`Writable`流。如果我们不希望自动关闭`Writable`流，需要传入参数：
```
readable.pipe(writable, { end: false });
```

#### 链式流

链式是通过连接输出流到另外一个流并创建多个对个流操作链的机制。链式流一般用于管道操作。接下来就用管道和链式来压缩和解压文件。

创建`compress.js`文件：
```
var fs = require("fs");
var zlib = require('zlib');

// 压缩 input.txt 文件为 input.txt.gz
fs.createReadStream('input.txt')
  .pipe(zlib.createGzip())
  .pipe(fs.createWriteStream('input.txt.gz'));
  
console.log("文件压缩完成。");
```
执行完以上操作后，我们可以看到当前目录下生成了`input.txt`的压缩文件`input.txt.gz`。

解压该文件，创建`decompress.js`文件，代码如下：
```
var fs = require("fs");
var zlib = require('zlib');

// 解压 input.txt.gz 文件为 input.txt
fs.createReadStream('input.txt.gz')
  .pipe(zlib.createGunzip())
  .pipe(fs.createWriteStream('input.txt'));
  
console.log("文件解压完成。");
```
