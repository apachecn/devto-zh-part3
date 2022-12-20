# 自定义传单 GUI

> 原文：<https://dev.to/itsmestevieg/customising-leafletjs-f55>

[![](img/86b0e4f8c1d9ab9639bbb7b97b32abb1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tjT_BWbx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h3a3v1a7dvgpbj2burvh.png)

**先决条件:**
[字体牛逼](https://fontawesome.com/)
[自举 4](https://getbootstrap.com/)
[jQuery](https://jquery.com/)
[小叶 JS](https://leafletjs.com/)
[小叶 Draw](https://github.com/Leaflet/Leaflet.draw)
[小叶侧边栏 v2](https://github.com/nickpeihl/leaflet-sidebar-v2)

首先启动一个基本的 HTML5 模板页面。

```
<html lang="en">
  <head>
    <meta charset="UTF-8">
    Leaflet JS Customisation Demo
    <meta name="viewport" content="width=device-width, initial-scale=1">
  </head>
  <body>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

在`</head>`结束标签中，包含以下 CSS 库:

```
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Roboto+Mono">
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.8.2/css/all.css">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.5.1/leaflet.css">
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet.draw/1.0.4/leaflet.draw.css">
<link rel="stylesheet" href="https://gitcdn.link/repo/nickpeihl/leaflet-sidebar-v2/master/css/leaflet-sidebar.min.css"> 
```

Enter fullscreen mode Exit fullscreen mode

在`</body>`结束标签之前包含以下 JavaScript 库:

```
<script src='https://cdnjs.cloudflare.com/ajax/libs/jquery/3.4.1/jquery.min.js'></script>
<script src='https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js'></script>
<script src='https://use.fontawesome.com/releases/v5.8.2/css/all.css'></script>
<script src='https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/4.3.1/js/bootstrap.min.js'></script>
<script src='https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.5.1/leaflet.js'></script>
<script src='https://cdnjs.cloudflare.com/ajax/libs/leaflet.draw/1.0.4/leaflet.draw.js'></script>
<script src='https://gitcdn.link/repo/nickpeihl/leaflet-sidebar-v2/master/js/leaflet-sidebar.min.js'></script> 
```

Enter fullscreen mode Exit fullscreen mode

通过在`<body></body`标签
中插入以下代码来创建侧栏和地图画布

```
<div id="sidebar" class="leaflet-sidebar collapsed">
  <!-- Nav tabs -->
  <div class="leaflet-sidebar-tabs">
    <ul role="tablist">
      <li><a href="#home" role="tab"><i class="fa fa-bars"></i></a></li>
      <li class="disabled"><a href="#profile" id="toggledraw" role="tab"><i class="fa fa-pen"></i></a></li>
    </ul>
  </div>

  <!-- Tab panes -->
  <div class="leaflet-sidebar-content">
    <div class="leaflet-sidebar-pane" id="home">
      <h1 class="leaflet-sidebar-header">
        Layers
        <span class="leaflet-sidebar-close"><i class="fa fa-caret-right"></i></span>
      </h1>
      <span id="layercontrol"></span>
      <div id="latlng"></div>

    </div>

    <div class="leaflet-sidebar-pane" id="profile">
      <h1 class="leaflet-sidebar-header">Profile<span class="leaflet-sidebar-close"><i class="fa fa-caret-right"></i></span></h1>
    </div>
  </div>
</div>

<div id="map"></div> 
```

Enter fullscreen mode Exit fullscreen mode

您将需要定义以下 CSS 来设置页面和地图大小:

```
body {
  padding: 0;
  margin: 0;
}
html,
body,
#map {
  height: 100%;
  font-family: "Roboto", sans-serif;
} 
```

Enter fullscreen mode Exit fullscreen mode

初始化传单地图选项并设置任何地图基层和覆盖层

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

初始化传单地图

```
//Render Main Map
var map = L.map("map", mapOptions); 
```

Enter fullscreen mode Exit fullscreen mode

我们必须修改 CSS 来准备层控件被分离并放入侧边栏

```
/* Leaflet Layers Control */

.leaflet-control-layers {
  border-radius: none;
  box-shadow: none;
}

.leaflet-control-layers-expanded {
  width: 100% !important;
  padding: 0px;
  background-color: transparent;
  border: none !important;
}

#layercontrol {
  display: inline-block;
  width: 100%;
  padding: 10px;
  background-color: transparent;
  -webkit-border-radius: 4px;
  -moz-border-radius: 4px;
  border-radius: 4px;
}

#layercontrol .title {
  font-size: 16px;
  font-weight: bold;
  margin-bottom: 5px;
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们初始化地图缩放控件、侧边栏，并将图层控件分离到侧边栏中

```
//Render Zoom Control
L.control
  .zoom({
    position: "topleft"
  })
  .addTo(map);

var sidebar = L.control
  .sidebar({
    autopan: false,
    container: "sidebar",
    position: "right"
  })
  .addTo(map);

//Render Layer Control & Move to Sidebar
var layerControl = L.control
  .layers(basemaps, overlays, {
    position: "topright",
    collapsed: false
  })
  .addTo(map);
var oldLayerControl = layerControl.getContainer();
var newLayerControl = $("#layercontrol");
newLayerControl.append(oldLayerControl);
$(".leaflet-control-layers-list").prepend("<strong class='title'>Base Maps</strong><br>");
$(".leaflet-control-layers-separator").after("<br><strong class='title'>Layers</strong>");
//$(".leaflet-control-layers-separator").remove(); 
```

Enter fullscreen mode Exit fullscreen mode

在我们初始化传单绘制控件之前，我们需要添加一些 CSS 来隐藏默认的控件

```
/* Leaflet Draw */

.leaflet-draw {
  display: none;
}

.leaflet-draw-toolbar {
  display: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

初始化可编辑的化妆品层和传单绘制控件

```
//######## Leaflet Draw
var editableLayers = new L.FeatureGroup();
layerControl.addOverlay(editableLayers, "Cosmetic Layer");
map.addLayer(editableLayers);

var drawOptions = {
  position: "topleft",
  draw: {
    polyline: true,
    polygon: {
      allowIntersection: false, // Restricts shapes to simple polygons
      drawError: {
        color: "#e1e100", // Color the shape will turn when intersects
        message: "<strong>Oh snap!<strong> you can't draw that!" // Message that will show when intersect
      }
    },
    circle: true, // Turns off this drawing tool
    rectangle: true,
    marker: true
  },
  edit: {
    featureGroup: editableLayers, //REQUIRED!!
    remove: true
  }
};

var drawControl = new L.Control.Draw(drawOptions);
map.addControl(drawControl);

map.on(L.Draw.Event.CREATED, function(e) {
  var type = e.layerType,
    layer = e.layer;

  if (type === "marker") {
    layer
      .bindPopup(
        "LatLng: " + layer.getLatLng().lat + "," + layer.getLatLng().lng
      )
      .openPopup();
  }

  editableLayers.addLayer(layer);
  $(".drawercontainer .drawercontent").html(
    JSON.stringify(editableLayers.toGeoJSON())
  );
});

map.on(L.Draw.Event.EDITSTOP, function(e) {
  $(".drawercontainer .drawercontent").html(
    JSON.stringify(editableLayers.toGeoJSON())
  );
});

map.on(L.Draw.Event.DELETED, function(e) {
  $(".drawercontainer .drawercontent").html("");
}); 
```

Enter fullscreen mode Exit fullscreen mode

接下来创建一个 jQuery 事件处理程序来切换绘制控件

```
//Edit Button Clicked
$('#toggledraw').click(function(e) {
  $(".leaflet-draw").fadeToggle("fast", "linear");
  $(".leaflet-draw-toolbar").fadeToggle("fast", "linear");
  this.blur();
  return false;
}); 
```

Enter fullscreen mode Exit fullscreen mode

在我们捕捉地图点击以将坐标放入侧边栏之前，我们需要设置 CSS

```
#latlng {
  position: absolute;
  right: 0px;
  bottom: 0px;
  font-size: 12px;
  font-weight: bold;
  padding: 2px 7px 1px 3px;
  margin-left: auto;
  margin-right: auto;
  color: black;
  background-color: white;
  -webkit-border-top-left-radius: 5px;
  -webkit-border-top-right-radius: 5px;
  -moz-border-radius-topleft: 5px;
  -moz-border-radius-topright: 5px;
  border-top-left-radius: 5px;
  border-top-right-radius: 5px;
  -webkit-box-shadow: 0px -2px 5px 0px rgba(0, 0, 0, 0.75);
  -moz-box-shadow: 0px -2px 5px 0px rgba(0, 0, 0, 0.75);
  box-shadow: 0px -2px 5px 0px rgba(0, 0, 0, 0.75);
  z-index: 1000;
  display: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以添加 jQuery map click 事件处理程序来捕获坐标，还可以创建 click 事件处理程序，用于在侧栏
中单击坐标时复制剪贴板

```
//Handle Map click to Display Lat/Lng
map.on('click', function(e) {
  $("#latlng").html(e.latlng.lat + ", " + e.latlng.lng);
    $("#latlng").show();
});

//Handle Copy Lat/Lng to clipboard
$('#latlng').click(function(e) {
  var $tempElement = $("<input>");
    $("body").append($tempElement);
    $tempElement.val($("#latlng").text()).select();
    document.execCommand("Copy");
    $tempElement.remove();
    alert("Copied: "+$("#latlng").text());
    $("#latlng").hide();
}); 
```

Enter fullscreen mode Exit fullscreen mode

你们都完了！请随意进一步定制，让我看看你的作品！

在 CodePen
[https://codepen.io/ItsMeStevieG/embed/QRgWNR?height=600&default-tab=result&embed-version=2](https://codepen.io/ItsMeStevieG/embed/QRgWNR?height=600&default-tab=result&embed-version=2) 上看到它的动作

还有黑暗模式
[https://codepen.io/ItsMeStevieG/embed/QRgjmL?height=600&default-tab=result&embed-version=2](https://codepen.io/ItsMeStevieG/embed/QRgjmL?height=600&default-tab=result&embed-version=2)

加入我的[哈希节点](https://hashnode.com/@ItsMeStevieG/joinme)