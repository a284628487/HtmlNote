# IScroll 使用参考
===

[IScroll](https://github.com/cubiq/iscroll)
最新的IScroll进行了分拆:
- iscroll.js 包含了全部的`iscroll`提供的功能；
- iscroll-lite.js 没有 `snap`, `scrollbars`, `key bindings`, `mouse wheel`，非常适用移动端；
- iscroll-probe.js 提供了实时获取滚动位置的功能；
- iscroll-zoom.js 在标准`iscroll`基础上，添加了缩放功能；
- iscroll-infinite.js 无限滚动；

#### Getting started
IScroll是一个类对象，任何需要使用的地方都需要进行初始化。使用的时候尽量使`DOM`树最简单；
```
<div id="wrapper">
    <ul>
        <li>...</li>
        <li>...</li>
        ...
    </ul>
</div>
```
IScroll必须被包装在`wrapper`的滚动区域内，即在上面的例子中，被滚动的对象将是`ul`，只有`wraper`中的第一个元素才可以滚动；

最简单的初始化方法如下:
```
<script type="text/javascript">
var myScroll = new IScroll('#wrapper');
</script>
```

最简单的一个使用IScroll的代码块:
```
<head>
...
<script type="text/javascript" src="iscroll.js"></script>
<script type="text/javascript">
var myScroll;
function loaded() {
    myScroll = new IScroll('#wrapper');
}
</script>
</head>
...
<body onload="loaded()">
<div id="wrapper">
    <ul>
        <li>A</li>
        <li>B</li>
    </ul>
</div>
</body>
```

除了上面的只传一个选择器的构造方法之外，还可以传入一些配置性的参数
```
var myScroll = new IScroll('#wrapper',{});
```

##### 参数
- 点击

```
{ click: true }
```

- 鼠标滚动

```
{ mouseWheel: true }
```

- tap

```
{ tap: true }
// 获取需要监听tab事件的元素然后添加监听；
document.getElementById('me').addEventListener('tap', function () {
	this.style.background = !this.style.background ? '#a00' : '';
}, false);
```

- scrollX | scrollY

```
{ scrollX: true, scrollY: true, freeScroll: true } // scrollY 默认就是true
// 需要指定 scroller 的 width 和 height，可直接使用px也可以使用百分比；
#scroller {
	position: absolute;
	z-index: 1;
	-webkit-tap-highlight-color: rgba(0,0,0,0);
	width: 200%;
}
```

- bounce

```
{ bounceEasing: 'elastic', bounceTime: 1200 }
```

- snap(分页?)

```
<script>
	myScroll = new IScroll('#wrapper', {
		scrollX: true,
		scrollY: false,
		momentum: false,
		snap: true,
		snapSpeed: 400,
		keyBindings: true,
		indicators: {
			el: document.getElementById('indicator'),
			resize: false
		}
	});
</script>
```
需要指定`scroller`的宽度，比如如果有4个选项，每个选项占满一屏的情况下，设置`scroller`的宽度 `width: 400%`;`scroller`里面的`item`的宽度为`width: 25%；`。

indicators: 滑动指示器；
```
<div id="indicator">
	<div id="dotty"></div>
</div>
```
指示器由一个div层包装一个div，里面的div才是真正的指示器，可以为dotty指定一些样式；
```
<script>
#dotty {
	position: absolute;
	width: 20px;
	height: 20px;
	border-radius: 10px;
	background: #f00;
}
</script>
```

- keyBindings

```
{ keyBindings: true } // 绑定之后，按下键盘的上下键，即可滚动列表；
```

- probe

```
<script type="text/javascript">
function updatePosition () {
	console.log(this.y);
	position.innerHTML = this.y>>0;
}

function loaded () {
	position = document.getElementById('position');
	myScroll = new IScroll('#wrapper', { probeType: 3, mouseWheel: true });
	myScroll.on('scroll', updatePosition);
	myScroll.on('scrollEnd', updatePosition);
}</script>
```
使用`probe`参数可以进行滚动位置的监听;

- scroll
滚动到指定位置:

```
<script type="text/javascript">

// 滚动打指定元素到页面最顶端
myScroll.scrollToElement(document.querySelector('#scroller li:nth-child(10)'))
// 滚动元素到屏幕中间
myScroll.scrollToElement(document.querySelector('#scroller li:nth-child(25)'), null, null, true)
// 滚动到指定元素(带动画）
myScroll.scrollToElement(document.querySelector('#scroller li:nth-child(50)'), 1200, null, null, IScroll.utils.ease.elastic)
</script>
```


