# div+css添加蒙层、选择菜单
===

```
<style>
    .mask {
        width: 100%;
        height: 100%;
        display: block;
        background-color: rgba(0, 0, 0, 0.3);
        box-sizing: border-box;
        opacity: 1;
        outline: none;
        position: fixed;
        transition-delay: 0s;
        transition-duration: 0.4s;
        transition-property: all;
        transition-timing-function: ease;
        z-index: 999;
        -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
        -webkit-user-select: none;
    }
</style>
```

上面的`mask`样式，定义了`z-index： 999`，`z-index`默认是0，值越大，显示优先级越高，也即 `z-index` 为999的元素将覆盖`z-index` 小于999的元素。

通常，添加一个蒙层往往另有其用，比如，在蒙层上添加菜单选择项，初始化显示的时候，将它隐藏起来，然后用户触发某些操作的时候，将选项显示出来，下面就来实现这样一个例子；

- 添加一个`maskContent样式` ，宽度为父元素60%，左右居中；

```
<style>
    .maskContent {
        width: 60%;
        background-color: #ffffff;
        margin-left: 20%;
        box-shadow: none;
        box-sizing: border-box;
        display: block;
        line-height: 21px;
        opacity: 1;
        outline: none;
        transform: matrix(1, 0, 0, 1, 0, 0);
        transition-delay: 0s, 0s;
        transition-duration: 0.3s, 0.3s;
        transition-property: transform, opacity;
        transition-timing-function: ease, ease;
        -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
        -webkit-user-select: none;
    }
</style>
```
- 结合另一篇笔记 [div元素居中](https://github.com/a284628487/HtmlNote/blob/master/note/css/div%2Bcss%E5%85%83%E7%B4%A0%E5%B1%85%E4%B8%AD.md)，定义两个菜单选项，居中显示在div中；

```
<div class="mask">
    <div class="box" style="width: 100%; height: 100%;">
        <div class="center">
            <div class="maskContent" style="background-color: #f5f5f5">
                <div style="height:40px; background-color: #ffffff">ZXCVBNM</div>
                <div style="height:40px; background-color: #ffffff; margin-top: 1px">QWERTY</div>
            </div>
        </div>
    </div>
</div>
```
- 最终效果类似`Android`里面弹出的选项对话框，为了与用户交互，可以在`div`上添加点击事件；
