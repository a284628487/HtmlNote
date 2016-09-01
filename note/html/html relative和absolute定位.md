# html relative 和 absolute 定位

### relative（相对定位）

定位原点是以自己本身原来所在位置做为原点

### absolute（绝对定位）

定位原点是离自己这一级元素最近的一级`position`设置为`absolute`或者`relative`的父元素的左上角为原点
(如果自己的所有父元素都没有设置position，那么就以body为定位原点),

### static（继承）

`position`的默认值，一般不设置`position`属性时，会按照正常的文档流进行排列。

### 实例分析

- demo_1
```
<body>
<div class="list">
    <div>A</div>
    <div>B</div>
    <div>C</div>
</div>
<div id="a" style="border: 1px solid #ff2abb; position: absolute; left: 200px; top: 0px; width: 100px; height: 100px;">a</div>
</body>
```
渲染效果
```
***********************
A        *******
B        *  a  *
C        *     *
         *******

***********************
```
由于设置了`left:200px`和`top:0px`,所以该`div#a`距离左边`200px`,上边`0x`;

- demo_2
```
<body>
<div class="list">
    <div>A</div>
    <div>B</div>
    <div>C</div>
</div>
<div id="a" style="border: 1px solid #ff2abb; position: absolute; left: 200px; width: 100px; height: 100px;">a</div>
</body>
```
渲染效果
```
***********************
A
B
C
         *******
         *  a  *
         *     *
         *******

***********************
```
由于只设置了`left:200px`,所以该`div#a`距离左边`200px`,同时由于`div.list`默认的`position`为`static`,所以`div#a`自动定位到`div.list`下边;

- demo_2_1

将上面的`div#a`的`position`改为`relative`,也是同样的效果;

- demo_3

```
<body>
<div class="list">
    <div>A</div>
    <div>B</div>
    <div>C</div>
</div>

<div style="border: 1px solid #ff2a00; position: absolute; left: 100px; width: 100px; height: 100px;">a</div>
<div style="border: 1px solid #ff2abb; position: absolute; left: 200px; width: 100px; height: 100px;">b</div>
// 或者
<!--<div style="border: 1px solid #ff2abb; position: relative; left: 200px; width: 100px; height: 100px;">b</div>-->
</body>
```

渲染效果
```
***********************
A
B
C
         *************
         *  a  *  b  *
         *     *     *
         *************

***********************
```

- demo_3_1

```
<body>
<div class="list">
    <div>A</div>
    <div>B</div>
    <div>C</div>
</div>

<div style="border: 1px solid #ff2a00; position: relative; left: 100px; width: 100px; height: 100px;">a</div>
<div style="border: 1px solid #ff2abb; position: absolute; left: 200px; width: 100px; height: 100px;">b</div>

</body>
```

渲染效果
```
***********************
A
B
C
         *******
         *  a  *
         *     *
         *******
               *******
               *  b  *
               *     *
               *******

***********************
```

- demo_3_2

将`deom_3_1`中的后面两个`div`改成如下的`style`
```
<div style="border: 1px solid #ff2a00; position: relative; left: 100px; width: 100px; height: 100px;"></div>
<div style="border: 1px solid #ff2abb; position: relative; left: 200px; width: 100px; height: 100px;"></div>
```
渲染效果同上


#### 结论

1. **在`position:absolute;`时不设置`top:_`的情况下,会自动根据`html`文档自上而下布局,
如果显示指定了`top:_`属性,则将会以离自己这一级元素最近的一级`position`设置为`absolute`或者`relative`的父元素的左上角为原点,进行偏移布局;**

2. **对于块级元素如`div`,设置样式`position: relative;`,将会占用整行,后面的`html`元素将自动往下布局(不设置`float:_`的情况下),`position: relative;`则不会这样;**


