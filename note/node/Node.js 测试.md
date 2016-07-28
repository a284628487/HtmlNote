# Node.js 测试 mocha, should, istanbul

### Point
- 学习使用测试框架 [mocha](http://mochajs.org/)
- 学习使用断言库 [should](https://github.com/tj/should.js)
- 学习使用测试率覆盖工具 [istanbul](https://github.com/gotwarlost/istanbul)
- 简单 Makefile 的编写 http://blog.csdn.net/haoel/article/details/2886

#### begin

定义fibonacci函数，此函数的定义为 int fibonacci(int n)
```
	当 n === 0 时，返回 0；n === 1时，返回 1;
	n > 1 时，返回 fibonacci(n) === fibonacci(n-1) + fibonacci(n-2)，如 fibonacci(10) === 55;
	n 不可大于10，否则抛错，因为 Node.js 的计算性能没那么强。
	n 也不可小于 0，否则抛错，因为没意义。
	n 不为数字时，抛错。
```
新建fibonacci.js
```
var fibonacci = function (n) {
	if(n === 0){
		return 0;
	}
	if(n === 1){
		return 1;
	}
	return fibonacci(n-1) + fibonacci(n-2);
}

if(require.main === module) {
	// 如果是直接执行 test.js，则进入此处
	// 如果 test.js 被其他文件 require，则此处不会执行。
	var v = Number(process.argv[2]);
	console.log(fibonacci(v));
}

exports.fibonacci = fibonacci;
```
执行命令
```
node fibonacci.js 10 // 结果为55
```
#### 测试驱动开发

在`fibonacci.js`同级目录下添加/test/fibonacci.test.js
```
var main = require('../main');
var should = require('should');

describe('test/main.test.js', function () {
	it('should equal 55 when n === 10', function () {
		main.fibonacci(10).should.equal(55);
	});
});
```
安装全局的 `mocha`
```
$ npm install mocha -g
```
`-g`与 非`-g`的区别，就是安装位置的区别，`g`是`global`的意思。如果不加的话，则`mocha`安装 在项目目录下面；如果加了，则这个`mocha`是安装在全局的，如果`mocha`有可执行命令的话，那么这个命令也会自动加入到你系统 `$PATH`中的某个地方。

执行测试:
```
$ mocha
```

##### describe & it

`describe`中的字符串，用来描述你要测的主体是什么；`it`当中，描述具体的`case`内容。

引入的`should`模块，是个断言库。在`Node.js`中，使用`mocha`和`should`在协作完成断言测试。
`should`在`js`的Object “基类”上注入了一个`#should`属性，这个属性中，又有着许许多多的属性可以被访问。
比如测试一个数是不是大于3，则是`(5).should.above(3)；`测试一个字符串是否有着特定前缀：`'foobar'.should.startWith('foo');`。`should.js API `在：[https://github.com/tj/should.js](https://github.com/tj/should.js)
还有另外一个断言库 [expect](https://github.com/LearnBoost/expect.js/)

- 新增一个`case`内容
```
it('should throw when n isn\'t Number', function () {
	(function () {
		fibonacci.fibonacci('呵呵');
	}).should.throw('n should be a Number');
});
```
再次运行的时候，该`case`未跑通；

- 更新 `fibonacci` 实现

添加对参数的判断
```
if (typeof n !== 'number') {
	throw new Error('n should be a Number');
}
```
再跑一次`$ mocha`就过了。这就是测试驱动开发：先把要达到的目的都描述清楚，然后让现有的程序跑不过 case，再修补程序，让 case 通过。

#### 测试覆盖率

- 安装`istanbul : $ npm i istanbul -g`

- 执行`$ istanbul cover _mocha`

这会比直接使用`mocha`多了覆盖率的输出!
```
=============================== Coverage summary ===============================
Statements   : 83.33% ( 10/12 )
Branches     : 87.5% ( 7/8 )
Functions    : 100% ( 1/1 )
Lines        : 83.33% ( 10/12 )
```
- 查看测试详情

打开 `/coverage/lcov-report/index.html`文件查看；

#### over

`mocha`和`istanbul`的结合是相当无缝的，只要`mocha`跑得动，那么`istanbul`就接得进来。

- mocha 和 istanbul 版本依赖问题

假设你有一个项目A，用到了 mocha 的 version 3，其他人有个项目B，用到了 mocha 的 version 10，那么如果你 npm i mocha -g 装的是 version 3 的话，你用 $ mocha 是不兼容B项目的。因为 mocha 版本改变之后，很可能语法也变了。

这时，跑测试用例的正确方法，应该是
```
$ npm i mocha --save-dev，装个 mocha 到项目目录中去
$ ./node_modules/.bin/mocha，用刚才安装的这个特定版本的 mocha，来跑项目的测试代码。
./node_modules/.bin 这个目录下放着我们所有依赖自带的那些可执行文件。
```
每次输入这个很麻烦，所以我们要引入`Makefile`，让`Makefile`帮我们记住复杂的配置。

```
test:
  ./node_modules/.bin/mocha

cov test-cov:
  ./node_modules/.bin/istanbul cover _mocha

.PHONY: test cov test-cov
```
这时，我们只需要调用`make test`或者`make cov`，就可以跑我们相应的测试了。
关于[Makefile](http://blog.csdn.net/haoel/article/details/2886)
