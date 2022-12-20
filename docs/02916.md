# 传单入门

> 原文：<https://dev.to/itsmestevieg/getting-started-with-leaflet-260d>

[![](img/85aaf576df2fd553f3d5fe82fd7aa0e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iW3g1yQd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1bnfsz7moboq2ngou41i.png)

启动一个基本的 HTML 模板

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    Getting Started with Leaflet JS
    <meta name="viewport" content="width=device-width, initial-scale=1">
  </head>
  <body>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

在文档的头部分包含活页 CSS 文件:

```
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.5.1/leaflet.css"> 
```

Enter fullscreen mode Exit fullscreen mode

在传单的 CSS 之后包含传单 JavaScript 文件(在结束`</body>`标记之前):

```
<script src='https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.5.1/leaflet.js'></script> 
```

Enter fullscreen mode Exit fullscreen mode

将 id 为`map`的 div 元素放在您希望地图位于的位置:

```
<div id="map"></div> 
```

Enter fullscreen mode Exit fullscreen mode

确保地图容器有一个定义的高度，例如通过在 CSS:
中设置它

```
body {
  padding: 0;
  margin: 0;
}
html,
body,
#map {
  height: 100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，您已经准备好初始化地图并对其进行一些处理。

让我们从设置底图服务开始。更多信息请见([文档](https://leafletjs.com/reference-1.5.0.html#map)):

```
//Init Overlays
var overlays = {};

//Init BaseMaps
var basemaps = {
  "OpenStreetMaps": L.tileLayer(
    "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
    {
      minZoom: 2,
      maxZoom: 19,
      id: "osm.streets"
    }
  ),
  "Google-Map": L.tileLayer(
    "https://mt1.google.com/vt/lyrs=r&x={x}&y={y}&z={z}",
    {
      minZoom: 2,
      maxZoom: 19,
      id: "google.street"
    }
  ),
  "Google-Satellite": L.tileLayer(
    "https://mt1.google.com/vt/lyrs=s&x={x}&y={y}&z={z}",
    {
      minZoom: 2,
      maxZoom: 19,
      id: "google.satellite"
    }
  ),
  "Google-Hybrid": L.tileLayer(
    "https://mt1.google.com/vt/lyrs=y&x={x}&y={y}&z={z}",
    {
      minZoom: 2,
      maxZoom: 19,
      id: "google.hybrid"
    }
  )
}; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们设置地图选项，如中心和缩放。

```
//Map Options
var mapOptions = {
  zoomControl: false,
  attributionControl: false,
  center: [-29.0529434318608, 152.01910972595218],
  zoom: 10,
  layers: [basemaps.OpenStreetMaps]
}; 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以初始化地图

```
//Render Main Map
var map = L.map("map", mapOptions); 
```

Enter fullscreen mode Exit fullscreen mode

除了切片图层，您还可以轻松地向地图添加其他内容，包括标记、折线、多边形、圆和弹出窗口。让我们添加一个标记:

```
var marker = L.marker([-29.0529434318608, 152.01910972595218]).addTo(map); 
```

Enter fullscreen mode Exit fullscreen mode

当您想要将一些信息附加到地图上的特定对象时，通常会使用弹出窗口。传单有一个非常方便的快捷方式:

```
marker.bindPopup("<b>Hello world!</b><br>I am a popup.").openPopup(); 
```

Enter fullscreen mode Exit fullscreen mode

有关更多信息，请参见[传单快速入门指南](https://leafletjs.com/examples/quick-start/)

在 CodePen [https://codepen.io/ItsMeStevieG/embed/YbQKMQ?height=600&default-tab=result&embed-version=2](https://codepen.io/ItsMeStevieG/embed/YbQKMQ?height=600&default-tab=result&embed-version=2) 上查看它的运行情况

加入我的[哈希节点](https://hashnode.com/@ItsMeStevieG/joinme)