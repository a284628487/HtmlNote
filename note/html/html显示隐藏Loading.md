# html 显示、隐藏Loading状态
===
在网页里面显示、隐藏`Loading`状态，比起`Android`，要容易得多，因为Html里面的大部分元素，都可以随意的向里面添加子元素，基于此，要显示一个`Loading`，只需要在`body`里面添加一个元素即可；

`Loading`元素需要能够阻止用户的操作，所以通常需要定义它的`z-index`属性，以及`Loading`状态图，需要居中显示，于是可以定义如下的样式：
```
    <style>
        body {
            margin: 0px;
        }

        .loading {
            width: 100%;
            height: 100%;
            z-index: 10;
            top: 0;
            margin: 0;
            position: absolute;
        }

        .box {
            display: table;
            text-align: center;
        }

        .box .center {
            display: table-cell;
            vertical-align: middle;
        }
    </style>
```

下面的代码`hardcode`添加一个`Loading`状态图；
```
<div class="loading">
    <div class="box" style="width: 100%; height: 100%;">
        <div class="center"><img src="ajax-loader.gif"></div>
    </div>
</div>
```

实际上更常见的情况是定义一个能用的方法，上面的`style`样式也定义成通用的，然后在需要的时候通过`$`动态的添加到`body`中；
