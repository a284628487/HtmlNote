# Node.js 使用 async 控制并发

[async](https://github.com/caolan/async), [asyncdemo](https://github.com/alsotang/async_demo)

`async`的`mapLimit(arr, limit, iterator, callback)`接口就是用来控制并发数量的。另外，还有个常用的控制并发连接数的接口是`queue(worker, concurrency)`，可以去`https://github.com/caolan/async#queueworker-concurrency`看看说明。

##### 什么时候用`eventproxy`，什么时候使用`async`？

当需要去多个数据源获取数据然后进行(一般是小于10个)汇总的时候，用`eventproxy` 方便；当需要用到队列，需要控制并发数，或者需要方便的使用函数式编程时，使用`async`，大部分场景是前者。

demo:
```
var concurrencyCount = 0;
function fetchUrl(url, callback){
	var delay = parseInt((Math.random() * 10000000) % 1000);
	concurrencyCount++;
	console.log(concurrencyCount, url, delay);
	setTimeout(function(){
		concurrencyCount--;
		callback(null, url, 'content');
	}, delay);
}

var urls = [];
for (var i = 0; i < 30; i++){
	urls.push('http://xlxl_');
}

var async = require('async');
async.mapLimit(urls, 5, function(url, callback){
	fetchUrl(url, callback);
}, function(err, urls_){
	console.log('over');
	// console.log(err, urls_);
});
```

- 构造一个`fetchUrl`方法获取网页内容
- 生成需要爬取的地址列表
- 使用`async.mapLimit`并发获取爬取网页
