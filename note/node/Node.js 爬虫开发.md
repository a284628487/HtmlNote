# Node.js 爬虫开发

需要使用到的3个依赖
- express
- superagent http://visionmedia.github.io/superagent/ http 方面的库，可以发起 get 或 post 请求。
- cheerio  https://github.com/cheeriojs/cheerio 可以理解成一个`Node.js`版的`jquery`，用来从网页中以`css selector`取数据，使用方式跟`jquery`一样。

#### 新建项目

- mkdir spider && cd spider
- npm init
- npm install PACKAGE_NAME --save
- coding

##### demo.js: 爬取`https://cnodejs.org/`
```
'use strict';

var express = require('express');
var superagent = require('superagent');
var cheerio = require('cheerio');

var app = express();
app.get('/', function (req, resp, next) {
	superagent.get('https://cnodejs.org/')
	.end(function(err, sres){
		if(err){
			next(err);
		}
		// sres.text 里面存储着网页的 html 内容，将它传给 cheerio.load 之后
	    // 就可以得到一个实现了 jquery 接口的变量，我们习惯性地将它命名为 `$`
        // 剩下就都是 jquery 的内容了
        var $ = cheerio.load(sres.text);
        var items = [];
        $('#topic_list .topic_title').each(function (idx, element) {
	        var element = $(element);
	        items.push({
		        title: element.attr('title'),
		        href: element.attr('href')
		    });
        });
	    resp.send(items);
	});
});
var server = app.listen(8081, function() {
	var host = server.address().address;
	var port = server.address().port;
	console.log(host + ":" + port);
});
```

#### eventProxy 控制并发

[eventproxy](https://github.com/JacksonTian/eventproxy)

`Node.js`的并发模型跟多线程不同，抛却那些观念。更具体一点的话，比如异步到底为何异步，Node.js 为何单线程却能并发这类走近科学的问题，参考《九浅一深Node.js》： http://book.douban.com/subject/25768396/ 。

用`js`写过异步的应该都知道，如果你要并发异步获取两三个地址的数据，并且要在获取到数据之后，对这些数据一起进行利用的话，常规的写法是自己维护一个计数器。

先定义一个`var count = 0`，然后每次抓取成功以后，就`count++`。如果是要抓取三个源的数据，由于根本不知道这些异步操作到底谁先完成，那么每次当抓取成功的时候，就判断一下`count === 3`。当值为真时，使用另一个函数继续完成操作。

而`eventproxy`就起到了这个计数器的作用，它来帮你管理到底这些异步操作是否完成，完成之后，它会自动调用你提供的处理函数，并将抓取到的数据当参数传过来。

如果利用`eventproxy`，则是这样的
```
var ep = new eventproxy();
ep.all('data1_event', 'data2_event', 'data3_event', function (data1, data2, data3) {
	var html = func(data1, data2, data3);
	render(html);
});

$.get('http://data1_source', function (data) {
	ep.emit('data1_event', data);
});

$.get('http://data2_source', function (data) {
	ep.emit('data2_event', data);
});

$.get('http://data3_source', function (data) {
	ep.emit('data3_event', data);
});
```
其实就是相当于一个高等计数器
```
ep.all('data1_event', 'data2_event', 'data3_event', function (data1, data2, data3) {});
```
此行代码监听了三个事件，分别是`data1_event`, `data2_event`, `data3_event`，每次当一个源的数据抓取完成时，就通过`ep.emit()`来告诉`ep`，某某事件已经完成了。
当三个事件未同时完成时，`ep.emit()`调用之后不会做任何事；当三个事件都完成的时候，就会调用末尾的那个回调函数，来对它们进行统一处理。

`eventproxy`提供了不少其他场景所需的`API`，但最最常用的用法就是以上的这种，即：
- 先 var ep = new eventproxy(); 得到一个 eventproxy 实例。
- 告诉它你要监听哪些事件，并给它一个回调函数。ep.all('event1', 'event2', function (result1, result2) {})。
- 在适当的时候 ep.emit('event_name', eventData)。

[更多](https://github.com/JacksonTian/eventproxy#%E9%87%8D%E5%A4%8D%E5%BC%82%E6%AD%A5%E5%8D%8F%E4%BD%9C)

```
'use strict';

var express = require('express');
var superagent = require('superagent');
var cheerio = require('cheerio');
var eventproxy = require('eventproxy');
var url = require('url');

var app = express();
superagent.get('https://cnodejs.org/')
.end(function(err, sres){
	if(err){
		next(err);
	}
	// sres.text 里面存储着网页的 html 内容，将它传给 cheerio.load 之后
    // 就可以得到一个实现了 jquery 接口的变量，我们习惯性地将它命名为 `$`
    // 剩下就都是 jquery 的内容了
    var $ = cheerio.load(sres.text);
    var items = [];
    $('#topic_list .topic_title').each(function (idx, element) {
        var element = $(element);
        var href = url.resolve('https://cnodejs.org/', element.attr('href'));
        items.push(href);
    });
    // 获取topic
    getTopic(items);
});

function getTopic(items) {
	var ep = new eventproxy();
	// topic_getted 被触发了 items.length 次之后，执行回调方法
	ep.after('topic_getted', items.length, function(topics) {
		//
		console.log('topic_getted:' + items.length);
		topics = topics.map(function(topicPair){
			var topicUrl = topicPair[0];
			var topicHtml = topicPair[1];
			var $ = cheerio.load(topicHtml);
			return ({
		    	title: $('.topic_full_title').text().trim(),
		    	href: topicUrl,
		    	comment1: $('.reply_content').eq(0).text().trim(),
			});
		});
		console.log(topics);
	});

    items.forEach(function (topicUrl) {
    	// 发起请求
    	superagent.get(topicUrl)
        .end(function (err, res) {
        	if (err) {
        		console.log('error:_');
        	}
        	// 触发事件并传入参数 [url, 请求获取到的html]
        	ep.emit('topic_getted', [topicUrl, res.text]);
        });
    });
}
```
要点:
```
var ep = new eventproxy();
ep.all('event1', 'event2', function(e1, e1){}); // 全部事件被触发之后执行回调
ep.after('event_', 20, function(data){}); // 事件被触发多少次之后执行回调
```

