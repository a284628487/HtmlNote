# Node.js入门
#### 环境搭建
`Node.js`下载地址:https://nodejs.org/
安装完成后，在命令行窗口输入`node -v`，即可看到版本信息，在命令行输入`node`，可进入`Node.js`交互环境，在交互环境下，你可以输入任意`JavaScript`语句，例如100+200，回车后将得到输出结果。要退出`Node.js`环境，连按两次`Ctrl+C`。

npm: `npm`其实是`Node.js`的包管理工具（package manager）。
我们在Node.js上开发时，会用到很多别人写的JavaScript代码。如果我们要使用别人写的某个包，每次都根据名称搜索一下官方网站，下载代码，解压，再使用，非常繁琐。于是一个集中管理的工具应运而生：大家都把自己开发的模块打包后放到npm官网上，如果要使用，直接通过npm安装就可以直接用，不用管代码存在哪，应该从哪下载。
更重要的是，如果我们要使用模块A，而模块A又依赖于模块B，模块B又依赖于模块X和模块Y，npm可以根据依赖关系，把所有依赖的包都下载下来并管理起来。
`npm`已经在`Node.js`安装的时候顺带装好了。我们在命令提示符或者终端输入`npm -v`，即可以看到`npm`的版本。

#### HelloWorld
新建一个文件`hw.js`，然后在命令行窗口下进入该目录，输入命令`node hw.js`，即可执行`hw.js`里面的代码。
```
console.log('HelloWorld');
```

#### 模块
为了编写可维护的代码，我们把很多函数分组，分别放到不同的文件里，这样，每个文件包含的代码就相对较少，很多编程语言都采用这种组织代码的方式。在`Node`环境中，一个`.js`文件就称之为一个模块（module）。使用模块还可以避免函数名和变量名冲突。相同名字的函数和变量完全可以分别存在不同的模块中，因此，我们自己在编写模块时，不必考虑名字会与其他模块冲突。
模块名其实就是`.js`的文件名，比如`hello.js`文件就是名为`hello`的模块。
- 定义模块
greet.js
```
'use strict';

function greet(name) {
    console.log('Hello, ' + name + '!');
}

module.exports = greet;
```
函数`greet()`是我们在`greet`模块中定义的，最后一行是一个赋值语句，它的意思是，把函数`greet`作为模块的输出暴露出去，这样其他模块就可以使用`greet`函数了。
- 使用模块
main.js
```
'use strict';

// 引入greet模块:
var greet = require('./greet');
var s = 'a284628487';
greet(s); // Hello, a284628487!
```
注意到引入`hello`模块用`Node`提供的`require`函数：
```
var greet = require('./greet');
```
引入的模块作为变量保存在`greet`变量中，`greet`变量到底是什么东西？其实变量`greet`就是在`greet.js`中我们用`module.exports = greet;`输出的`greet`函数。所以，`main.js`就成功地引用了`greet.js`模块中定义的`greet()`函数，接下来就可以直接使用它了。

在使用`require()`引入模块的时候，请注意模块的相对路径。因为`main.js`和`hello.js`位于同一个目录，所以我们用了当前目录.：
```
var greet = require('./greet'); // 不要忘了写相对目录!
```
如果只写模块名：
```
var greet = require('greet');
```
则`Node`会依次在内置模块、全局模块和当前模块下查找`hello.js`，很可能会得到一个错误：
```
module.js
    throw err;
          ^
Error: Cannot find module 'greet'
    at Function.Module._resolveFilename
    at Function.Module._load
    ...
    at Function.Module._load
    at Function.Module.runMain
```
遇到这个错误，你要检查：
	模块名是否写对了；
	模块文件是否存在；
	相对路径是否写对了；

#### `CommonJS`规范
这种模块加载机制被称为`CommonJS`规范。在这个规范下，每个`.js`文件都是一个模块，它们内部各自使用的变量名和函数名都互不冲突，例如，`hello.js`和`main.js`都申明了全局变量`var s = 'xxx'`，但互不影响。

一个模块想要对外暴露变量（函数也是变量），可以用`module.exports = variable`;，一个模块要引用其他模块暴露的变量，用`var ref = require('module_name');`就拿到了引用模块的变量。

#### 小结

- 在模块中对外输出变量
```
module.exports = variable;
```
输出的变量可以是任意对象、函数、数组等等。

- 引入其他模块输出的对象
```
var foo = require('other_module');
```
引入的对象具体是什么，取决于引入模块输出的对象。
