# html5调用硬件设备
===

`Html5`在手机上和PC上的表现略有不同；

一、`Html5`调用移动设备，获取`media`数据：
```
	<input type="file" accept="video/*;capture=camcorder">
	<p>iPhone上弹出选择对话框让选择现有照片或者 (拍照或者录相),Android上直接进入媒体库选择.</p>
	<input type="file" accept="audio/*;capture=microphone">
	<p>效果同上</p>
	<input type="file" accept="image/*;capture=camera">
	<p>效果同上</p>
	<input type="file" accept="image/*" />
	<p>iPhone上弹出选择对话框让选择现有照片或者拍照,Android上直接进入图片库选择.</p>

```
上面的代码如果直接在PC上使用，则只有一种表现形式，就是弹出文件选择框；

二、`Html5`调用PC设备，获取`camera`视频流：
```
<body>
<video id="video" autoplay="" style='width:640px; height:480px'></video>

<div><button id='capture' style="width: 100%;">Capture</button></div>

<canvas id="canvas" width="640" height="480"></canvas>

<script type="text/javascript">
    var video = document.getElementById("video");
    var context = canvas.getContext("2d");
    var error_cb = function() {
        console.log('sth wrong!');
    }
    // navigator.getUserMedia这个写法在Opera中好像是navigator.getUserMedianow 
    if (navigator.getUserMedia) { // 标准的API
        navigator.getUserMedia({
            "video": true
        }, function(stream) {
            video.src = stream;
            video.play();
        }, error_cb);
    } else if (navigator.webkitGetUserMedia) { // WebKit 核心的API
        navigator.webkitGetUserMedia({
            "video": true
        }, function(stream) {
            video.src = window.webkitURL.createObjectURL(stream);
            video.play();
        }, error_cb);
    }
    document.getElementById("capture").addEventListener("click", function() {
        context.drawImage(video, 0, 0, 640, 480);
    });
</script>
</body>
```
上面的代码在用户授权使用`camera`后，如果调用`camera`成功，会在`video`标签里面实时播放`camera`获取到的视频流数据；
除此之外，还给`button#capture`按钮添加了点击事件，实时获取图片帧；

```
<script>
    var interval = setInterval(uploadCapture, 500);

    function uploadCapture() {
        var canvans = document.getElementById("canvas");
        //获取浏览器页面的画布对象
        //以下开始编码数据
        var imgData = canvans.toDataURL();
        //将图像转换为base64数据
        var base64Data = imgData.substr(22);
        //在前端截取22位之后的字符串作为图像数据
        //开始异步上传
        $.post("uploadImgCode.ashx", {
            "img": base64Data
        }, function (data, status) {
            if (status == "success") {
                if (data == "OK") {
                    // alert("ok");
                } else {
                    // alert(data);
                }
            } else {
                // alert("数据上传失败");
            }
        }, "text");
    }
</script>
```
除了截图外，还可以实时上传图片帧，上面的代码实现了每秒上传2帧图片的功能；

图片获取流程</br>

一、图片获取：
从`Canvas`获取图片数据的核心思路是用`canvas`的`toDataURL`将`Canvas`的数据转换为`base64`位编码的PNG图像，类似于`data:image/png;base64,xxxxx`的格式。
```
var imgData=canvas.toDataURL(“image/png”);
```
这样，`imgData`变量就存储了一长串的字符数据内容，表示的就是一个PNG图像的`base64`编码。因为真正的图像数据是`base64`编码逗号之后的部分，所以要让实际服务器接收的图像数据应该是这部分，有两种办法来获取。 
- 在前端截取22位以后的字符串作为图像数据，例如：
```
var data = imgData.substr(22); // 如果要在上传前获取图片的大小，可以使用：
var length = atob(data).length; // atob 可解码用base-64解码的字串
```
- 在后端获取传输的数据后用后台语言截取22位以后的字符串（也就是在前台略过上面这步直接上传）。例如PHP里：
```
$image=base64_decode(str_replace(‘data:image/jpeg;base64,’,”,$data);
```
二、图片上传：

在前端可以使用Ajax将上面获得的图片数据上传到后台脚本。例如使用jQuery时可以用：
```
$.post(‘upload.php’, {‘data’:data});
```

