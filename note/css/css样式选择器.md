# css 样式选择器
===
css中有多种样式选择器，可以满足多种不同的使用场景。

一、标签选择器：对指定的标签使用统一的样式;
```
<style>
    p {
        color: #2aabd2;
    }
</style>
<div>
    <p>HelloWorld</p>
</div>
```
定义了`p`标签选择器之后，所以的`p`标签的字体颜色就统一了；
二、class选择器
- 给样式定一个名字，在元素中选择使用哪个样式，元素主动找样式，多个样式之间在中间加空格隔开，当有多个样式的时候，冲突的部分将被自身的style样式覆盖；
- 标签的名字.样式的名字(针对不同的标签，实现同样的样式名对于不同的标签有不同的样式) 比如，`input.test{}`；
```
<style>

    .input-test {
        color: #ff4400;
    }

    input.test {
        color: #2aabd2;
    }
</style>
<div>
    <input class="test" value="class样式"/>
    <input class="input-test" value="class样式2"/>
</div>
```
三、id选择器：为指定id的元素设定样式，id前加#，`#username{}` 定义了元素id为username的控件的样式；
```
<style>
	#username {
	}
</style>
```
四、关联选择器：`p strong{}`；p标签内的`strong`标签内的内容使用的样式；
```
<style>
    p strong {
        color: rgb(255, 0, 0);
    }
</style>
<div>
    <p><strong>内联选择器</strong></p>
</div>
```
五、组合选择器：同时为多个标签设定一个样式，`h1,h2,input,label{}` 表示 `h1,h2,input,label` 的样式都为设定的样式；
```
    body, html, input{
        
    }
```
六、伪选择器：为标签的不同状态设定不同的样式，常见于超链接，链接前后样式的改变；
```
<style>
    A:visited {
        text-decoration: none
    }

    A:active {
        text-decoration: none
    }

    A:link {
        text-decoration: none;
        color: Blue
    }

    A:hover {
        text-decoration: underline
    }

</style>
```

