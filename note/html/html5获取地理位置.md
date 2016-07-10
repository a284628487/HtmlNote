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
    enableHighAccuracy, // boolean 是否要求高精度的地理信息
    timeout, // 表示等待响应的最大时间，默认是0毫秒，表示无穷时间
    maximumAge, // 应用程序的缓存时间
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
