---
layout: post
title: ionic开发之连接百度地图api
categories: [ionic ]
tags: [ionic ]
description: ionic
---


## 来张效果图先
![](/images/ionic/ionic_map.jpg "ionic_map ionic")
(居然定位不到子午线，百度还是太low了。)

## 步骤

1. 首先是下载百度插件
ionic plugin add https://github.com/mrwutong/cordova-qdc-baidu-location --variable API_KEY="你申请的百度地图钥匙"(直接进百度开发者注册就可以申请了，free)

2. 然后需要为你的phonegap项目加一个Gelolcation的插件，大概的步骤就是：使用dos命令进入到你的phonegap项目目录，然后输入以下命令：

cordova plugin add cordova-plugin-geolocation

3. html页面：
> ```html
    <input  type="text" name="city" ng-model="serchval.address"  placeholder="请输入地点" style="position: absolute;height: 40px;top: 5px;left:5%;z-index: 998;background: rgba(12,12,12,.5);width: 90%;padding: 1rem;  color:#ffffff;"/>
    <button id="searchbtn" ng-click="run();" class="ion-search" style="width: 15%;height: 40px;position: absolute;top: 5px;right: 5%;font-size:2rem ;background:rgba(12,12,12,.5);color: #ffffff;z-index: 999 ;border: none; " ></button>
    <div id="allmap" style="height: 100%;width:100%; "></div>
```
> 记得引入地图js资源
```javascript
    <script type="text/javascript" src="http://api.map.baidu.com/getscript?v=2.0&ak=CcZMawdycw5SPGc7rKXpokqR"></script>
    <script type="text/javascript" src="http://developer.baidu.com/map/jsdemo/demo/convertor.js"></script>
```

> 记一个小坑
使用ionic的指令，相当与有了一层scope,所以在指令上面在加上双向数据绑定只能用对象模式
```javascript
    $scope.serchval={
             address:null
         };
```
> 如果使用变量就会变成单向数据绑定
```javascript
    $scope.serchval="guangzhou";     
```
 

>回到正题 实例化百度地图
![](/images/ionic/ionic_map2.jpg "ionic_map2 ionic")
绑定搜索框事件
![](/images/ionic/ionic_map3.jpg "ionic_map3 ionic")
	上面有一个run函数ng-click到btn;记得注入$ionicLoading，才能使用警告框！
	下面是判断设备是否支持定位，获得首次进入会获得当前位置。
```javascript
	//检测设备是否支持定位功能
        if(window.navigator.geolocation){
            var geolocation=window.navigator.geolocation;

            //每一次位置的变化，就获取一次设备的位置，都会执行getPositionSuccess回调函数
            watchID= geolocation.watchPosition(getPositionSuccess,getPositionError);
        }else{
            $rootScope.showAlert("您的应用程序不支持地理位置定位")
        }
        // 获取用户位置信息失败调用的方法
        function getPositionError(err) {
            console.log(err);
            switch (err.code) {
                case 3:
                    $rootScope.showAlert("连接超时，请重试");
                    break;
                case 1:
                    $rootScope.showAlert("您拒接了使用位置共享服务");
                    break;
                case 2:
                    $rootScope.showAlert("无法提供位置服务");
                    break;
            }
        }
        //获取地理位置
        function getPositionSuccess(position){
            var lon=position.coords.longitude;  //读取经度
            var lat=position.coords.latitude;   //读取纬度
            console.log('get point');
            //设置起点坐标
            var gpsPoint = new BMap.Point(lon,lat);  //119.69138,30.655101
            $scope.run(gpsPoint) ;//终点
        }
```
[百度地图api参数](http://developer.baidu.com/map/reference/index.php?title=Class:%E6%9C%8D%E5%8A%A1%E7%B1%BB/LocalSearch)
[百度地图demo](http://developer.baidu.com/map/jsdemo.htm#i7_1)