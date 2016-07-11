# html5 获取地理位置
===
html5提供了在网页中通过`navigator.geolocation`来获取地理位置的支持，目前大部分浏览器都已经支持了；
`navigator.geolocation`用于获取基于浏览器的当前用户地理位置，提供了3个方法:
- void getCurrentPosition(onSuccess, onError, options); // 获取用户当前位置
- int watchPosition(onSuccess, onError, options); // 持续获取当前用户位置
- void clearWatch(watchId); // 取消持续获取位置，`watchId` 为 `watchCurrentPosition` 返回的值

onSuccess方法成功时调用的(必选)，onError方法失败是调用的(可选)，options其他参数(可选)
```
options = {
    enableHighAccuracy, // boolean 是否要求高精度的地理信息，默认false，在手机端设置为true可以获取到更准确的位置；
    timeout, // 表示等待响应的最大时间，即请求超时时间，单位ms；
    maximumAge, // 最长有效期，在重复获取地理位置时，此参数指定获取位置的时间间隔；
}
```
下边是一个实例：
```
    <script>

        function getLocation() {
            var options = {
                enableHighAccuracy: true,
                maximumAge: 1000
            }
            if (navigator.geolocation) {
                //浏览器支持geolocation
                navigator.geolocation.watchPosition(onSuccess, onError, options);
            }
        }

        function onSuccess(location) {
            //经度
            var longitude = location.coords.longitude;
            //纬度
            var latitude = location.coords.latitude;
            // TODO
        }

        function onError(error) {
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    alert("位置服务被拒绝");
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert("暂时获取不到位置信息");
                    break;
                case error.TIMEOUT:
                    alert("获取信息超时");
                    break;
                case error.UNKNOWN_ERR:
                    alert("未知错误");
                    break;
            }
        }

        window.onload = getLocation();
    </script>
```

注意：获取到的位置需要加上偏移量
- 经度校正值： 0.008774687519;
- 纬度校正值： 0.003745316879;

通过浏览器获取到地理位置只有经纬度，如果需要获取街道信息，可以使用百度地图的`jsapi`进行转换，需要提前申请一个百度地图应用；
```
<script>
    /**
     * 位置解码
     * @param longitude
     * @param latitude
     * @param successCallback
     * @param failCallback
     * @param errorCallback
     */
    function geocode(longitude, latitude, successCallback, failCallback, errorCallback) {
        var ak1 = "baidu_appkey";
        var location = latitude + "," + longitude;
        var url_address = 'http://api.map.baidu.com/geocoder/v2/';
        $.ajax({
            url: url_address,
            type: "POST",
            data: {ak: ak1, location: location, output: "json", pois: 0, coordtype: "wgs84ll"},
            async: false,
            dataType: "jsonp",
            jsonp: "callback",
            jsonpCallback: "showLocation",
            success: function (data) {
                if (data.status == 0) {
                    var province;
                    if (data.result.addressComponent.province == null || data.result.addressComponent.province == "") {
                        province = "";
                    } else {
                        province = data.result.addressComponent.province;
                    }
                    var city;
                    if (data.result.addressComponent.city == null || data.result.addressComponent.city == "") {
                        city = "";
                    } else {
                        city = data.result.addressComponent.city;
                    }
                    var area;
                    if (data.result.addressComponent.district == null || data.result.addressComponent.district == "") {
                        area = "";
                    } else {
                        area = data.result.addressComponent.district;
                    }
                    var address;
                    if (data.result.formatted_address == null || data.result.formatted_address == "") {
                        address = "";
                    } else {
                        address = data.result.formatted_address;
                    }
                    successCallback(data.result.location.lng, data.result.location.lat, address);
                } else {
                    if (null != failCallback) {
                        failCallback();
                    }
                }
            },
            error: errorCallback
        });
    }
</script>
```
在`successCallback`中，就可以获取到街道信息了；
