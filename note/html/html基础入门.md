# Html 基础入门
===
默认情况下，HTML会自动地在块级元素(如:h1,p,ul,table,div)前后添加一个额外的空行，而内联元素(如:b,td,a,img)在显示时不会以新行开始；
[w3c颜色查询](http://www.w3school.com.cn/html/html_colornames.asp)

常用标签:

### a
普通的跳转不作记录，很简单，下面来看使用a标签进行锚定位：
- 首先，在 HTML 文档中对锚进行命名（创建一个书签）：
```
<a name="tips">我是一个锚</a>
```
- 然后，同一个文档中创建指向该锚的链接("#+锚的name")：
```
<a href="#tips">跳到上一个锚</a>
```
也可以在其他页面中创建指向该锚的链接：

```
<a href="http://www.w3school.com.cn/html/html_links.asp#tips">跳到其它页面的锚</a>
```
在上面的代码中，将 # 符号和锚名称添加到 URL 的末端，就可以直接链接到`tips`这个命名锚了。
提示：命名锚经常用于在大型文档开始位置上创建目录。可以为每个章节赋予一个命名锚，然后把链接到这些锚的链接放到文档的上部。

### img
```
<img align="bottom/middle/top/right/left"> // 设置图像在文本中的对齐方式
```

### fieldset
```
<fieldset><legend>XXX</legend></fieldset> 将控件划分一个区域，相当于GroupBox，XXX为区域名称；
```

### table
`tr`：创建一行
`td`：创建一个单元格，`<td colspan = "2">`当一个单元格上一行或者下一行中不只一列，而自身恰好只有一列的时候，设置colspan为列的数目；
`caption`：设置Table的标题

```
	colspan	跨行
	rowspan	跨列
	cellpadding	元素内容与表格线的距离
	ceppspacing	表格与表格线之间的距离
	<td>的属性；空单元格设置为:"&nbsp"
```

### 列表

[无序列表](http://www.w3school.com.cn/tiy/t.asp?f=html_lists_unordered)

[有序列表](http://www.w3school.com.cn/tiy/t.asp?f=html_lists_ordered)

```
	<ol>	有序列表
	<ul>	无序列表
	<li>	列表项
	<dl>	自定义列表
	<dt>	自定义列表中的一项
	<dd>	定义项目的描述
```
