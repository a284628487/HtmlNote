# div+css 使元素居中
===
在`div`中，要使元素左右居中，只需要一句话`text-align:center`，但实现元素上下居中，则微微复杂一些；

```
<style>

    .box {
        display: table;
        text-align: center;
    }

    .box .center {
        display: table-cell;
        vertical-align: middle;
    }
</style>

<div class="box" style="width: 100%; height: 240px;">
    <div class="center" style="background-color: #84c6ff">
        <img src="add.png">
    </div>
</div>

```

要点：
- `box` 定义父元素的 `display` 属性为 `table`，以及 `text-align: center` 使子元素左右居中；
- `center` 字义了元素的 `display` 属性为 `table-cell`，也就是表明此元素的表现属性为一个`cell`，再结合 `vertical-align: middle` 就实现了元素的上下居中；
- 使用 `box` `center` 方式实现时，需要最外层的`div`的宽高确定，比如100%，`center`的宽高不用设置，因为它的`display` 属性为 `table-cell`，所以它会铺满整个 `box`，最后展现出的效果就是`img`左右上下居中了；

如果某一个 `div` 的宽高是固定的`px`值，注意，一定是固定的`px`值，可以直接使用如下的方式更加方便；

```
<style>
    .center2 {
        display: table-cell;
        text-align: center;
        vertical-align: middle;
    }
</style>

<div class="center2" style="background-color: #84c6ff; width: 200px; height: 200px;">
    <div>Center</div>
</div>
```
