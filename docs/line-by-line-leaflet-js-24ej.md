# 逐行:fleed . js

> 原文：<https://dev.to/kauresss/line-by-line-leaflet-js-24ej>

## 简介

fleed . js 是一个用于创建地图的 JavaScript 库。JS 类是函数。

示例:

```
function Person() {
            this.firstName = "unknown";
            this.lastName = "unknown";
            this.getFullName = function(){
                return this.firstName + " " + this.lastName;
            }
        };

var person1 = new Person();
person1.firstName = "Steve";
person1.lastName = "Jobs"; 
```

Enter fullscreen mode Exit fullscreen mode

## 启动并运行

1.  声明 var mapData，它是一个带有中心键和缩放键的对象文本。其值是经度和纬度以及表示缩放级别的整数。数字越大意味着缩放级别越高

```
var mapData = {
   center: [35.083498, -106.651960],
   zoom: 16
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  使用 leaflet 的 map 类在页面上创建一个 map，通过调用 map 类实例化一个新的 map，该 map 类有两个参数:DIV id，其中 map 将被放置在对象文本中

```
var map = new L.map('map', mapData); 
```

Enter fullscreen mode Exit fullscreen mode

1.  使用 tile layers 类在地图上显示切片图层 Wikipedia 切片 web 地图、滑动地图 [1](https://dev.toin%20OpenStreetMap%20terminology) 或切片地图(栅格或矢量)是通过无缝连接互联网上数十个单独请求的图像文件而在浏览器中显示的地图。Tilelayer 类将指定提供程序(openstreetmap)中的 tilelayer 作为参数

```
var layer = new L.TileLayer('http://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png'); 
```

Enter fullscreen mode Exit fullscreen mode