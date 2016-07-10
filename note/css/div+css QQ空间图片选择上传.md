# div+css QQ空间图片上传
===
手机qq空间上传图片的时候，以`gridView`的样式来展现用户选择的图片；
如何使用`div+css`实现`gridView`，参考[GridView](https://github.com/a284628487/HtmlNote/blob/master/note/css/div%2Bcss%E5%AE%9E%E7%8E%B0GridView.md)

下面是布局代码:
```
<body>

<style>
    #choosePhoto {
        width: 100px;
        height: 100px;
        border: 1px dashed #4caf50;
        overflow: hidden;
    }

    .picBox {
        width: 100px;
        height: 100px;
    }

    .picBox .image {
        width: 100%;
        height: 100%;
        border: 1px solid rgb(204, 204, 204);
        background-size: cover;
        background-repeat: stretch;
        position: relative;
    }
</style>
<script>
    // 添加li
    function addImgGrid(index) {
        var li = "<li class=\"ui-grid-li picBox\"></li>";
        var liImg = $(li);
        liImg.html("<div class=\"image\" id=\"image-preview-" + index + "\"></div>");
        return liImg;
    }
</script>

<script>
    // 选择图片回调事件
    function onFileSelected() {
        var fileInput = document.getElementById('selectImage');
        if (!fileInput.value) {
            return;
        }
        var choosephoto = $('#choosePhoto');
        var access = $('#accessorys');
        var index = access.children().length;
        var li = addImgGrid(index);
        // 添加到`input`元素之前
        choosephoto.before(li);
        var preview = document.getElementById("image-preview-" + index);
        // 文件
        var file = fileInput.files[0];
        if (file.type !== 'image/jpeg' && file.type !== 'image/png' && file.type !== 'image/gif') {
            return;
        }
        // 创建文件读写对象去读取文件
        var reader = new FileReader();
        // 添加读取成功回调
        reader.onload = function (e) {
            var data = e.target.result;
            // index = data.indexOf(';base64,');
            preview.style.backgroundImage = 'url(' + data + ')';
        };
        reader.readAsDataURL(file);
        // 如果需要能够选择重复的图片，需要在选择之后清空`input`的选择的值；
        $('#selectImage').val('');
    }

    $(function () {
        // 添加选择事件监听
        var fileInput = document.getElementById('selectImage');
        fileInput.addEventListener('change', onFileSelected);
    });
</script>

<div style="display: block; overflow: hidden;">
    <ul id="accessorys" class="ul-grid">
        <li id="choosePhoto">
            <div class="picBox" style="z-index: 1; position: absolute;">
                <div class="box" style="width: 100%; height: 100%;">
                    <div class="center"><img src="add.png"/>
                    </div>
                </div>
            </div>
            <input id="selectImage" type="file" accept="image/jpeg,image/png,image/gif,image/bmp"
                   style="z-index: 2; opacity: 0; position: relative; width: 100%; height: 100%;">
        </li>
    </ul>
</div>

</body>

```

要点：
- 定义选择按钮为正文形，这里用到了div中居中的样式，可参考：[div+css居中](https://github.com/a284628487/HtmlNote/blob/master/note/css/div%2Bcss%E5%85%83%E7%B4%A0%E5%B1%85%E4%B8%AD.md)
- 用户选择图片之后，生成一个新元素并插入到选择按钮前面，同时将图片显示出来，以设置`div`背景的方式；

当用户选择图片之后，生成一个`li`元素，并添加到选择框之前，`li`元素的内容如下：
```
<li class="ui-grid-li picBox">
    <div class="image" id="image-preview-index0" style="background-image: url(data:image/png;base64,...;);"></div>
</li>
```
将图片作为`li`中的`div`的背景显示出来；
