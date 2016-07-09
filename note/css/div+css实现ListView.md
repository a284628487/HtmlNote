# 使用div+css实现Android ListView
===
使用`div+css`实现`ListView`本身很简单，甚至不需要写`css`样式就行，但是，如果想要做的更接近`Android原生`的话，就理写几行`css`了

```
<style>

    .listItem {
        box-sizing: border-box;
        width: 100%;
        /*display: list-item;*/
        font-size: 17px;
        line-height: 44px;
        outline: none;
        overflow-x: hidden;
        overflow-y: hidden;
        position: relative;
        text-align: left;
        padding-left: 8px;
        transform-style: flat;
        -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
        -webkit-user-select: none;
    }

    .listItem:active {
        background-color: #f5f5f5;
    }

    .listItem::after {
        background-color: rgb(200, 199, 204);
        bottom: 0px;
        content: "";
        display: block;
        width: 100%;
        height: 1px;
        left: 0px;
        right: 0px;
        position: absolute;
        transform: matrix(1, 0, 0, 0.5, 0, 0);
    }

    .listItem:last-child::after {
        height: 0px;
    }

</style>

<div class="listItem">ABC</div>
<div class="listItem">DEF</div>
<div class="listItem">HIJ</div>

</body>
```
要点：
- `listItem` 定义了`listItem`的行高，字体等信息；
- `listItem:active` 定义了当`listItem`的活动状态样式，也就是用户按下的时候的样式，类似`android`中的`selector`里面的`state_active`;
- `listItem::after` 定义了`listItem`底部`divider`分割条，也可以省略此样式，在`listItem`中直接定义`margin-top`，也可以实现`divider`分割条，但某些情况下，使用`::after`将会更灵活，除了`::after`，还有`::before`；
- `listItem:last-child::after` 和前一个样式相比，多了一个条件`last-child`，顾名思义，这个样式是列表中的最后一个元素特有的样式，除了`last-child`，还有`first-child`；
