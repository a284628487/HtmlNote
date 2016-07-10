# html 事件
===

事件处理器的值是一个或一系列以分号隔开的 Javascript 表达式、方法和函数调用，并用引号引起来。

#### 窗口事件 (Window Events)
仅在 body 和 frameset 元素中有效。

```
onload		当文档被载入时执行脚本
onunload	当文档被卸下时执行脚本
```
#### 表单元素事件 (Form Element Events)
仅在表单元素中有效。

```
onchange	当元素改变时执行脚本
onsubmit	当表单被提交时执行脚本
onreset		当表单被重置时执行脚本
onselect	脚本	当元素被选取时执行脚本
onblur		当元素失去焦点时执行脚本
onfocus		当元素获得焦点时执行脚本
```

#### 图像事件 (Image Events)
该属性可用于 img 元素：

```
onabort		当图像加载中断时执行脚本
```

#### 键盘事件 (Keyboard Events)
在下列元素中无效：base、bdo、br、frame、frameset、head、html、iframe、meta、param、script、style 以及 title 元素。

```
onkeydown		当键盘被按下时执行脚本
onkeypress		当键盘被按下后又松开时执行脚本
onkeyup			当键盘被松开时执行脚本
```

#### 鼠标事件 (Mouse Events)
在下列元素中无效：base、bdo、br、frame、frameset、head、html、iframe、meta、param、script、style 以及 title 元素。

```
onclick			当鼠标被单击时执行脚本
ondblclick		当鼠标被双击时执行脚本
onmousedown		当鼠标按钮被按下时执行脚本
onmousemove		当鼠标指针移动时执行脚本
onmouseout		当鼠标指针移出某元素时执行脚本
onmouseover		当鼠标指针悬停于某元素之上时执行脚本
onmouseup		当鼠标按钮被松开时执行脚本
```
