# Node.js 常用工具

`util`是一个`Node.js`核心模块，提供常用函数的集合，用于弥补核心`JavaScript`的功能过于精简的不足。

#### util.inherits
`util.inherits(constructor, superConstructor)`是一个实现对象间原型继承的函数。`JavaScript的面向对象特性是基于原型的，与常见的基于类的不同。`JavaScript`没有 提供对象继承的语言级别特性，而是通过原型复制来实现的。
```
var util = require('util'); 

function Base() { 
	this.name = 'base'; 
	this.birthday = 1991; 
	this.sayHello = function() { 
		console.log('Hello ' + this.name); 
	}; 
} 
Base.prototype.showName = function() { 
	console.log(this.name);
};

Base.prototype.age = 25;

function Sub() { 
	this.name = 'sub'; 
} 

util.inherits(Sub, Base); 
var objBase = new Base(); 
objBase.showName(); // base
objBase.sayHello();  // Hello base
console.log(objBase);  // { name: 'base', base: 1991, sayHello: [Function] } 
var objSub = new Sub(); 
objSub.showName(); // sub
//objSub.sayHello(); // error!!! 
console.log(objSub); // { name: 'sub' }
console.log(objSub.age); // 25
console.log(objSub.birthday); // undefined
```
上面的代码定义了一个基础对象`Base`和一个继承自`Base`的`Sub`，`Base`有三个在构造函数内定义的属性和一个原型中定义的函数，通过`util.inherits`实现继承。

注意：`Sub`仅仅继承`Base`在原型中定义的函数，而构造函数内部创造的`base`属性和`sayHello`函数都没有被`Sub`继承。
同时，在原型中定义的属性不会被`console.log`作为对象的属性输出。如果去掉`objSub.sayHello();`这行将会报错。

#### util.inspect

`util.inspect(object,[showHidden],[depth],[colors])`是一个将任意对象转换为字符串的方法，通常用于调试和错误输出。它至少接受一个参数`object`，即要转换的对象。`showHidden`是一个可选参数，如果值为`true`，将会输出更多隐藏信息。`depth`表示最大递归的层数，如果对象很复杂，你可以指定层数以控制输出信息的多少。如果不指定`depth`，默认会递归2层，指定为`null`表示将不限递归层数完整遍历对象。 如果`color`值为`true`，输出格式将会以`ANSI`颜色编码，通常用于在终端显示更漂亮的效果。
特别要指出的是，`util.inspect`并不会简单地直接把对象转换为字符串，即使该对象定义了`toString`方法也不会调用。

```
var util = require('util'); 
function Person() { 
	this.name = 'byvoid'; 
	this.toString = function() { 
		return this.name; 
	}; 
} 
var obj = new Person(); 
console.log(util.inspect(obj)); 
console.log(util.inspect(obj, true)); 
```
执行结果:
```
{ name: 'byvoid', toString: [Function] } 
{ toString: 
{ [Function] 
[prototype]: { [constructor]: [Circular] }, 
[caller]: null, 
[length]: 0, 
[name]: '', 
[arguments]: null }, 
name: 'byvoid' } 
```

#### util.isArray(object)
```
var util = require('util');

util.isArray([])
  // true
util.isArray(new Array)
  // true
util.isArray({})
  // false
```

#### util.isRegExp(object)

如果给定的参数`object`是一个正则表达式返回`true`，否则返回`false`。
```
var util = require('util');

util.isRegExp(/some regexp/)
  // true
util.isRegExp(new RegExp('another regexp'))
  // true
util.isRegExp({})
  // false
```

#### util.isDate(object)
```
var util = require('util');

util.isDate(new Date())
  // true
util.isDate(Date())
  // false (without 'new' returns a String)
util.isDate({})
  // false
```

#### util.isError(object)

返回给定的`object`是否是一个错误对象
```
var util = require('util');

util.isError(new Error())
  // true
util.isError(new TypeError())
  // true
util.isError({ name: 'Error', message: 'an error occurred' })
  // false
```

[更多工具模块](http://www.runoob.com/nodejs/nodejs-utitlity-module.html)


