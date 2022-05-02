---
title: js中cookie值的设置和获取
keywords:
- js中cookie值的设置和获取
- mlzhang
description : "js中cookie值的设置和获取"
---
## JS cookie的取值和设置值



第一步：引入js

    <script src="/XX/js/login/jquery-1.5.1.min.js"></script>
     <script src="/XX/js/login/jquery.cookie.js"></script>

第二步：存放值

`$.cookie('the_cookie', 'the_value', { expires: 7, path: '/' });`
一步写到位，不要轻易把path去掉。不然只能在当前js使用，我吃过亏的

举个实例吧：
需求：城市定位，需要下次进入页面时记住上次自动定位的城市名字或者手动选择的城市名字

百度地图API功能

```javascript

var geolocation = new BMap.Geolocation();
geolocation.getCurrentPosition(function(r){
    if(this.getStatus() == BMAP_STATUS_SUCCESS){
        var 城市名= r.address.city；（拿到的城市名字）
        //往cookie里面放城市名称
        $.cookie('locateCity', 城市名, { expires: 7 ,path:'/'});
        }
        else {
            //alert('failed'+this.getStatus());
            mui.alert("城市定位失败");
        }        
    },{enableHighAccuracy: true})

```



第三步：取值

```javascript

var locateCity = $.cookie('locateCity');

```