# 淘宝html5页面 Header + Content + Footer
===
相比于其它网站的header + content + footer在手机端的展现，个人更喜欢淘宝的样式，因为其更接近原生，在UC浏览器中，上下滚动到了最顶部、底部时，不会出现还能继续滚动，显示出空白区域的难看的样式。下面的css就是从淘宝提取出来的！
```
html,
body,
.view {
    display: block;
    font-family: sans-serif;
    width: 100%;
    height: 100%;
    margin: 0px;
    padding: 0px;
    overflow-x: hidden;
    overflow-y: hidden;
    position: relative;
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}

.scrollview {
    display: flex;
    flex-direction: column;
    font-size: 12px;
    width: 100%;
    height: 100%;
    margin: 0px;
    padding: 0px;
    overflow-x: hidden;
    overflow-y: hidden;
    -webkit-box-direction: normal;
    -webkit-box-orient: vertical;
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}

.top-fixed {
    display: block;
    width: 100%;
    height: 40px;
    left: 0px;
    top: 0px;
    margin: 0px;
    padding: 0px;
    background-color: #2aabd2;
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}

.scroll-wrap {
    display: block;
    flex-basis: 0%;
    flex-grow: 1;
    flex-shrink: 1;
    padding: 0px;
    width: 100%;
    margin-bottom: 44px;
    position: relative;
    overflow: scroll;
    -webkit-box-flex: 1;
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}

.bottom-fixed {
    bottom: 0px;
    color: rgb(0, 0, 0);
    display: block;
    width: 100%;
    height: 44px;
    left: 0px;
    margin: 0px;
    position: fixed;
    padding: 0px;
    background-color: #5bc0de;
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}

```
定义完css后，接下来就在html页面中使用这些样式：
```
<body>

<style>
    .test-div {
        padding: 8px;
        text-align: center;
    }
</style>
<div class="view">
    <div class="scrollview">
        <div class="top-fixed"></div>
        <div class="scroll-wrap">
            <div class="test-div">top</div>
            <div style="height: 400px; background-color: #f8d9ac; width: 100%;"></div>
            <div class="test-div">center</div>
            <div style="height: 400px; background-color: #f09990; width: 100%;"></div>
            <div class="test-div">bottom</div>
        </div>
        <div class="bottom-fixed" style="background-color: darkgray"></div>
    </div>
</div>

</body>
```

重点观察属性样式：
- html&body&view: `display:block`, `position:relative`;
- scrollview: `display:flex`;
- scroll-wrap: `flex-grow:1`, `overflow:scroll`;
- bottom-fixed: `position:fixed`, `bottom:0px`;

使用：
```
body
--| div .view
----| div .scrollview
------| div .top-fixed
------| div .scroll-wrap
------| div .bottom-fixed
```
