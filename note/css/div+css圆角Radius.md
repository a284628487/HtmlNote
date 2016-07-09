# div+css圆角Radius
===
```
<style>
    .radius {
        background-clip: padding-box;
        background-color: rgb(255, 255, 255);
        border-radius: 6px;
        border: 1px solid rgb(221, 221, 221);
        box-sizing: border-box;
        color: rgb(0, 0, 0);
        display: block;
        outline: none;
        overflow-x: hidden;
        overflow-y: hidden;
        width: 100%;
        -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
        -webkit-user-select: none;
    }

    .radius .listitem {
        height: 44px;
        padding-left: 16px;
        line-height: 44px;
        background-color: #84c6ff;
    }
</style>

<div class="radius" style="width: 80%; margin-left: 10%;">
    <div class="listitem">
        23
    </div>
    <div class="listitem" style="margin-top: 1px;">
        24
    </div>
</div>
```
要点：
- `radius` 样式里面通过 `border-radius` 定义了圆角半径，`border` 则定义了边线的样式宽1px，实线，颜色；
- 除了通过 `radius` 统一设置4个角之外，还可以设置指定某个角的圆角半径，比如 `border-bottom-left-radius`；
- 重要的一点是 `radius` 的 `overflow` 属性为 `hidden`，所以不用去设置`div`的子元素的样式；
