# 使用div+css实现Android GridView
===
在`html`里面，可以通过设置元素的`float`样式来控制元素的显示位置，常用到的`float`的可选值为`left`和`right`，分别表示将元素靠左/右显示，注意，这里说的靠左/右表示的是相对于父元素的位置，同时，还可以设置元素的`width`，可以直接设定成固定宽度，也可以设定成百分比的形式；

举例说明：如果一个元素有多个`child`的`float`属性被设置为`left`，则如果在一行的剩余空间内能显示下一个`child`，则这个`child`就被添加到上一个`child`的右边，否则就换行添加到下一行；

基于以上，下面的代码实现了一个两行两列的GridView。
```
<style>

    .percent50 {
        width: 50%;
        height: 40px;
        line-height: 40px;
        text-align: center;
        float: left;
        background-color: #ff4400;
    }

    .percent50 div {
        margin-left: 1px;
        margin-top: 1px;
        background-color: #4aaa4d;
    }

</style>

<div style="overflow: hidden; margin-left: -1px; margin-top: -1px;">
    <div class="percent50">
        <div>A</div>
    </div>
    <div class="percent50">
        <div>B</div>
    </div>
    <div class="percent50">
        <div>C</div>
    </div>
    <div class="percent50">
        <div>D</div>
    </div>
</div>
```

解释：
- `percent50` 设置 `width: 50%;` 表示该样式的宽度为父元素的50%; 
- `percent50 div` 设置 `margin-left`，`margin-top` 用于展示 `divider` 分割线；
- 最顶层的div也设置了`margin-left`，`margin-top`属性，都为-1px，是因为 `percent50` 元素的子元素 `div` 元素设置了边距，否则将会在顶层的 `div` 中最左边最顶部显示出边距；

