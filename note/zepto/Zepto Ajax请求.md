# Zepto Ajax请求
===
使用`Zepto`发起`ajax`请求，和使用`JQuery`大体相同，但又有些小的区别；

#### 函数定义
```
$.ajax(options);
```

#### 常用参数
- type 请求方法 (“GET”, “POST”, or other)，默认"GET";
- url 地址;
- data 请求数据;
- dataType 预期服务器返回的数据类型(“json”, “jsonp”, “xml”, “html”, or “text”)
- jsonp JSONP回调查询参数的名称(默认：“callback”)
- timeout 以毫秒为单位的请求超时时间, 0 表示不超时。(默认： 0)
- headers: Ajax请求中额外的HTTP信息头对象
- async 默认设置下，所有请求均为异步。如果需发送同步请求，请将此设置为 false。(默认：true)
- global 请求将触发全局Ajax事件处理程序，设置为 false 将不会触发全局 Ajax 事件。(默认：true)

#### Ajax 回调函数
可以为请求指定以下的回调函数，他们将按给定的顺序执行：
- beforeSend(xhr, settings)：请求发出前调用，它接收xhr对象和`settings`作为参数对象。如果它返回 false ，请求将被取消。
- success(data, status, xhr)：请求成功之后调用。传入返回后的数据，以及包含成功代码的字符串。
- error(xhr, errorType, error)：请求出错时调用。 (超时，解析错误，或者状态码不在HTTP 2xx)。
- complete(xhr, status)：请求完成时调用，无论请求失败或成功。

#### Ajax 事件
当`global`为`true`时。在Ajax请求生命周期内，以下这些事件将被触发。
- ajaxStart (global)：如果没有其他Ajax请求当前活跃将会被触发。
- ajaxBeforeSend (data: xhr, options)：再发送请求前，可以被取消。
- ajaxSend (data: xhr, options)：像 ajaxBeforeSend，但不能取消。
- ajaxSuccess (data: xhr, options, data)：当返回成功时。
- ajaxError (data: xhr, options, error)：当有错误时。
- ajaxComplete (data: xhr, options)：请求已经完成后，无论请求是成功或者失败。
- ajaxStop (global)：如果这是最后一个活跃着的Ajax请求，将会被触发。

默认情况下，`Ajax`事件在`document`对象上触发。然而，如果请求的`context`是一个DOM节点，该事件会在此节点上触发然后再DOM中冒泡。`ajaxStart`和`ajaxStop`这两个全局事件除外。

```
<script>
    $(document).on('ajaxBeforeSend', function (e, xhr, options) {
        console.log('ajaxBeforeSend');
    });

    var loginUser = {
        UserId: "uid",
        UPwd: "1",
        UIp: '192.168.1.101',
        UClientOs: 'Android',
        UMac: "mac",
        AreaLocale: "xl",
    };

    $.ajax({
        url: "http://192.168.1.200:8014/xx.asmx/UserLogin?callback=?",
        type: "GET",
        data: loginUser,
        dataType: "jsonp",
        success: function (data, status, xhr) {
            console.log(data);
        },
        error: function (xhr, data) {
            console.log(data);
        }
    });
</script>
```
上面的`Ajax`请求在请求之前，会打印出`ajaxBeforeSend`，也即请求前触发了绑定在`document`上的`ajaxBeforeSend`事件；

### ajaxSettings
和`JQuery`不同的是，在`Zepto`中，并没有提供`ajaxSetup`函数来进行全局的请求设定，但是在`Zepto`中有`ajaxSettings`可以用于请求的全局配置；
```
<script type="text/javascript">
    $.ajaxSettings = $.extend($.ajaxSettings, {
        crossDomain: true,
        timeout: 3000,
        beforeSend: function (xhr, settings) {
            console.log("beforeSend");
            return true;
        },
        complete: function (xhr, status) {
            console.log("complete");
        }
    });
</script>
```
上面通过`ajaxSettings`配置了`Zepto`请求发起前的回调函数和请求完成之后的回调函数；

如果是使用`JQuery`，要配置`beforeSend`和`complete`则应该是这样：
```
<script type="text/javascript">
    $.ajaxSetup({
        global: true,
        beforeSend: function (XMLHttpRequest, settings) {
        },
        complete: function (XMLHttpRequest, textStatus) {
        }
    });
</script>
```
#### 其它
同`JQuery`一样，`Zepto`也提供了`get`，`getJSON`，`post`等方法；
