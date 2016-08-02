
下面是一个自定义定位插件，用于获取位置信息；

### easyLocation.js
```
cordova.define("com.ccflying.easyLocation", function (require, exports, module) {
    window.locationService = {
        execute: function (action, data, successCallback, errorCallback) {
            cordova.exec(
                function (pos) {
                    var errcode = pos.code;
                    if (errcode == 61 || errcode == 65 || errcode == 161) {
                        successCallback(pos);
                    } else {
                        if (typeof(errorCallback) != "undefined") {
                            if (errcode >= 162) {
                                errcode = 162;
                            }
                            errorCallback(pos)
                        }
                        ;
                    }
                },
                function (err) {
                },
                "EasyLocation", // mark1
                action,
                data
            )
        },
        getLocation: function (data, successCallback, errorCallback) {
            this.execute("getLocation", data, successCallback, errorCallback);
        },
    }
    module.exports = locationService;
});

`cordova.define`方法用于定义一个插件；
第一个参数插件是该`js`文件的路径: `com.ccflying.easyLocation`对应的在项目目录中的路径为:
```
- assets
    - www
        - plugins
            - com.ccflying
                - www
                    - easyLocagion.js
```
`execute`方法用于执行`html`请求执行的元方法，所以的请求都会使用`execute`来执行;
`getLocation`方法则定义了一个快捷接口;
`module.exports`将定义的`window.locationService`暴露出去，供调用者使用;

### EasyLocation.java

```
package com.ccflying;

public class EasyLocation extends CordovaPlugin {
    private static final String GET_LOCATION = "getLocation";

    @Override
    public boolean execute(String action, JSONArray args, final CallbackContext callbackContext) {
        if(action.equqls(GET_LOCATION)){
            // TODO
            PluginResult mPResult = new PluginResult(PluginResult.Status.OK, "{\"error\":0}");
            mPResult.setKeepCallback(true);
            callbackContext.sendPluginResult(mPResult);
            return true;
        }
    }
}
```

### `js`与`java`绑定

在 `res -> xml -> config.xml` 中，添加一个配置项
```
<feature name="EasyLocation">
    <param name="android-package" value="com.ccflying.EasyLocation" />
</feature>
```
`feature` 的 `name` 对应第一步中的 `mark1`;

### 在`html`中调用

```
    var easyLocation = window.easyLocation;
    var bean = new Array();
    easyLocation.getLocation(bean, function (data) {
    }, function (data) {
        var json = JSON.parse(data);
        // TODO
    });
```
