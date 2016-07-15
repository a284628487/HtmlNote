# JQuery 添加动画
===

用`JavaScript`实现动画，原理非常简单：我们只需要以固定的时间间隔（例如，0.1秒），每次把`DOM`元素的`CSS`样式修改一点（例如，高宽各增加10%），看起来就像动画了。但是要用`JavaScript`手动实现动画效果，需要编写非常复杂的代码。如果想要把动画效果用函数封装起来便于复用，那考虑的事情就更多了。`JQuery`为我们封装好了一些默认的动画；

#### show / hide / toggle
通常调用`show`和`hide`，都是无参调用，如果给传入参数，就可以添加上动画效果了；
```
var div = ...
div.show(1500);
div.hide('slow');
div.toggle('fast');
```
传入的参数可以是指定的毫秒，也可以是`fast`或者`slow`这样的字符串；

#### slideUp / slideDown / slideToggle
`show`和`hide`是从左上角逐渐展开或收缩的，而`slideUp`和`slideDown`则分别是在垂直方向逐渐收缩和展开的，`slideToggle`则根据元素是否可见来进行下一步动作：
```
var div = ...
div.slideUp(3000);
div.slideDown('slow');
div.slideToggle('fast');
```

#### fadeIn / fadeOut / fadeToggle
`fadeIn`和`fadeOut`的动画效果是淡入淡出，也就是通过不断设置DOM元素的`opacity`属性来实现；
```
var div = ...
div.fadeIn(3000);
div.fadeOut('slow');
div.fadeToggle('fast');
```

#### 自定义动画`animate`
它可以实现任意动画效果，我们需要传入的参数就是DOM元素最终的CSS状态和时间，`jQuery`在时间段内不断调整CSS直到达到我们设定的值：
```
var div = ...
div.animate({
    opacity: 0.25,
    width: '256px',
    height: '256px'
}, 3000); // 在3秒钟内CSS过渡到设定值
```

#### 动画回调查
`animate`还可以再传入一个函数，当动画结束时，该函数将被调用：
```
var div = ...;
div.animate({
    opacity: 0.25,
    width: '256px',
    height: '256px'
}, 3000, function () {
    console.log('动画已结束');
    // 恢复至初始状态：实际上这个回调函数参数对于基本动画也是适用的。
    $(this).css('opacity', '1.0').css('width', '128px').css('height', '128px');
});
```
同样，上面的`fadeIn`、`show`等方法同样也可以设置回调方法：
```
var div = ...
div.fadeIn(3000, function(){
    // over;
});
```

#### 串行动画
`jQuery`的动画效果还可以串行执行，通过`delay`方法还可以实现暂停，这样就能实现更复杂的动画效果，而代码却相当简单：
```
<script>
var div = ...;
// 动画效果：slideDown - 暂停 - 放大 - 暂停 - 缩小
div.slideDown(2000)
   .delay(500)
   .animate({
       width: '256px',
       height: '256px'
   }, 2000)
   .delay(500)
   .animate({
       width: '128px',
       height: '128px'
   }, 2000);
}
</script>
```
因为动画需要执行一段时间，所以`jQuery`必须不断返回新的`Promise`对象才能后续执行操作。简单地把动画封装在函数中是不够的。


有时候可能会遇到，有的动画如`slideUp`根本没有效果。这是因为`jQuery`动画的原理是逐渐改变CSS的值，如`height`从100px逐渐变为0。但是很多不是`block`性质的DOM元素，对它们设置`height`根本就不起作用，所以动画也就没有效果。
此外，`jQuery`也没有实现对`background-color`的动画效果，用`animate`设置`background-color`也没有效果。这种情况下可以使用CSS3的`transition`实现动画效果。



