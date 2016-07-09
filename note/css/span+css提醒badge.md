# span+css提醒badge
===
```
<style>
    span.badge {
        border-radius: 100px;
        box-sizing: border-box;
        background-color: #ff3c3c;
        color: rgb(255, 255, 255);
        display: block;
        font-size: 12px;
        height: 18px;
        list-style: none;
        line-height: 12px;
        outline: none;
        padding: 3.5px 6px;
        position: absolute;
        text-align: left;
        /*right: 15px;*/
        white-space: nowrap;
        -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
        -webkit-user-select: none;
    }
</style>
<span class="badge">12</span>
```
要点：
- `badge` 样式里面通过 `border-radius` 定义了圆角半径，当定义的值大于元素本身的宽高时，实际的圆角半径取值为宽高中小的一条边的一半，作为实际圆角半径值；
- 通常 `badge` 样式都是位于元素的右边或者右上角，可以通过 `right` 属性设置距离父元素右边距的距离来进行定位；
