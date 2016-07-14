# JQueryRotate 元素旋转
===

使用JQueryRotate，可以快速的实现元素旋转的效果，除了普通的生硬的旋转之外还可以定义旋转的过度效果；

在Html页面中有这样一个元素`testRotate`用于旋转；
```
    <div id="testRotate" onclick=""
         style="background-color: #2aabd2; width: 120px; overflow: hidden; margin-left:80px; height: 24px;">Rotated Div
    </div>
```

##### 普通旋转，有两种写法：
```
            angle += 20;
            $("#testRotate").rotate(angle);
            $("#testRotate").rotate({
                angle: angle,
            });
```
##### 带动画的旋转
```
            $("#testRotate").rotate({
                animateTo: angle
            });
```
##### 旋转事件绑定：除了直接定义`div`的点击事件来实现旋转之外，还可以通过事件绑定的方式来实现旋转；
```
        $(function () {
        	// 点击
            $("#testRotate").rotate({
                bind: {
                    click: function () {
                        $(this).rotate({
                            animateTo: 90,
                        });
                    }
                }
            });
        });
        // 除了点击事件的绑定外，还可以对其它事件比如鼠标的事件进行绑定；
        $(function () {
            $('#testRotate').rotate({
                bind: {
                    mouseover: function () {
                        $(this).rotate({animateTo: 180});
                    }, mouseout: function () {
                        $(this).rotate({animateTo: 0});
                    }
                }
            });
            // 注：绑定的鼠标移动事件只适用于PC
        }
```
##### 旋转完成回调
```
        function callbackRotate() {
            $('#testRotate').rotate({
                angle: 0,
                animateTo: 360,
                // callback: callbackRotate,
                callback: function () {
                    alert("rotate over");
                }
            });
            // 如果把`callback`方法设置为`callbackRotate`方法本身，则可以让`testRotate`元素一直旋转下去；
        }
```
##### 旋转过渡效果
```
        function easingRotate() {
            $("#testRotate").rotate({
                angle: 0,
                animateTo: 360,
                callback: function () {
                    alert("rotate over");
                },
                easing: function (x, t, b, c, d) { // t: 当前时间，b: 起始值，c: 改变的值, d: 执行时间
                    console.log(c / (t / d ) + b);
                    return c / (t / d ) + b;
                }
            });
        }

        function easingRotate2() {
            $("#testRotate").rotate({
                angle: 0,
                animateTo: 360,
                callback: function () {
                    alert("rotate over");
                },
                // http://gsgd.co.uk/sandbox/jquery/easing/jquery.easing.1.3.js
                easing: $.easing.easeInOutExpo,
            });
        }
```
上面两个方法，都定义一旋转的过渡效果`easing`，不同的是前者自定义了动画效果，而后者使用了`jquery.easing`的动画效果；

##### 旋转时间
```
        function durationRotate() {
            $("#testRotate").rotate({
                duration: 2000, // 转动时间
                angle: 0, // 默认角度
                animateTo: 360 * 4, // 转动角度
                easing: $.easing.easeOutSine,
                callback: function () {
                    alert("durationRotate");
                }
            });
        }
```

##### rotate函数参数
- angle 旋转一个角度 0
- animateTo 从当前的角度旋转到多少度 0
- step 每个动画步骤中执行的回调函数，当前角度值作为该函数的第一个参数 无
- easing 自定义旋转速度、旋转效果，需要使用 `jQuery.easing.js` 无
- duration 旋转持续时间，以毫秒为单位
- callback 旋转完成后的回调函数 无
- getRotateAngle 返回旋转对象当前的角度 无
- stopRotate 停止旋转 无

