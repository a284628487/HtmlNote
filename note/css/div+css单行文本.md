# div+css圆角Radius
===
```
<style>
    .textEllipse {
        box-sizing: border-box;
        display: block;
        font-size: 17px;
        height: 21px;
        line-height: 21px;
        list-style: none;
        outline: none;
        overflow-x: hidden;
        overflow-y: hidden;
        position: relative;
        text-align: left;
        text-overflow: ellipsis;
        white-space: nowrap;
        -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
        -webkit-user-select: none;
    }
</style>
<div class="textEllipse">Hello World,Hello World,Hello World,Hello World,
    Hello World,Hello World,Hello World,Hello World!
</div>

```
要点：
- `textEllipse` 样式里面设置 `text-overflow: ellipsis`，表示超出的文本将截断显示；
