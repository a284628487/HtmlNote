# Node.js 使用 async 控制并发

[async](https://github.com/caolan/async), [asyncdemo](https://github.com/alsotang/async_demo)

`async`的`mapLimit(arr, limit, iterator, callback)`接口就是用来控制并发数量的。另外，还有个常用的控制并发连接数的接口是`queue(worker, concurrency)`，可以去`https://github.com/caolan/async#queueworker-concurrency`看看说明。

##### 什么时候用`eventproxy`，什么时候使用`async`？

当需要去多个数据源获取数据然后进行(一般是小于10个)汇总的时候，用`eventproxy` 方便；当需要用到队列，需要控制并发数，或者需要方便的使用函数式编程时，使用`async`，大部分场景是前者。除了可以在`Node.js` 上运行外，其还可以在浏览器端运行。

#### mapLimit
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

#### series 串行

`async.series()`来依次执行一组函数，第一个参数是一个函数数组，每个函 数接受一个参数作为其回调函数，在该函数执行完成时调用回调函数即可。第二个参数作为该组函数执行完成后的回调函数。

```
var async = require('async');

async.series([
	function(done){
		console.log(1);
		done();
	},
	function(done){
		console.log(2);
		done();
	}
	], function(err){
		console.log('over');
});
```
执行结果
```
1
2
over
```
如果在执行某一个函数的过程中出错了，可以在执行回调函数`done()`的时候，将错误信 息作为回调函数的第一个参数传入并执行`done(err)`，程序将会跳过函数数组中剩余的部分而直接执行最终的回调函数。

#### parallel 并行

如果要并行的执行一组函数，我们只需要使用`async.parallel()`即可，使用方法同`async.series()`一样。

#### each

原始遍历
```
var arr = [1, 2, 3, 4, 5];
arr.forEach(function (item) {
  console.log(item);
});
```
假如`forEach()`的回调函数内部要执行的是一些异步操作，而我们又需要等待遍历操作完成后再执行其他的操作，可以使用`async.each()`来实现：
```
var arr = [1, 2, 3, 4, 5];
async.each(arr, function (item, done) {

  // 通过 setTimeout 来模拟一个异步任务
  setTimeout(function () {
    console.log(item);
    done();
  }, Math.random() * 1000);

}, function (err) {
  if (err) throw err;
  console.log('完成');
});
```

#### eachSeries 串行遍历

如果要让这些异步任务串行执行，可以使用`async.eachSeries()`来实现。
```
async.eachSeries(articleList, function (article, next) {
    // 读取文章内容
    readArticleDetail(article.url, function (err, detail) {
      if (err) console.error(err.stack);
      // 直接显示
      console.log(detail);
      // 调用 next() 来返回
      next();
    });
  }, function (err) {
    // 当遍历完 articleList 后，执行此回调函数
    if (err) return console.error(err.stack);
    console.log('完成');
  });
});

```

#### 小结

对应于数组的`map`、`filter`、`reduce`等方法，`async`模块也提供了相应工具函数。关于`async`模块的详细使用方法可以访问该模块的主页来获取：https://npmjs.org/package/async

