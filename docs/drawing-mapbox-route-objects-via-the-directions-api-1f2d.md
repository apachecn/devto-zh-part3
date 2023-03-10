# 通过方向 API 绘制地图框路径对象

> 原文：<https://dev.to/hackersandslackers/drawing-mapbox-route-objects-via-the-directions-api-1f2d>

[![Drawing Mapbox Route Objects via the Directions API](img/d083db7b15c574140eed61eceab4f138.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2T3vxBx---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hackersandslackers.com/conteimg/2019/02/mapboxroutes.jpg)

如果你以前来过这里，你可能已经知道我们对 **Mapbox** 的喜爱，以及它为数据科学家和分析师提供的可视化工具。在过去，我们已经介绍了从原始地址编码位置数据的[，以及为熟悉这个工具的人介绍的 Mapbox Studio](https://dev.to/hackersandslackers/geocoding-raw-datasets-for-mapbox-pma-temp-slug-8139646) 的[探索。今天我们更进一步:在地图上画方向。](https://dev.to/hackersandslackers/geographic-data-visualization-with-mapbox-ck7)

这听起来很简单:我们已经知道如何对地址进行地理编码，所以我们需要做的就是从 A 点到 b 点。也就是说，事情总是会变得棘手，如果你以前从未与 [GeoJSON](http://geojson.org/) 合作过，你会很高兴。

## 加载一些数据

我假设您已经准备好包含这些列的数据帧:

*   起点 _ 经度
*   原点 _ 纬度
*   目的地 _ 经度
*   目的地纬度
*   此路线的名称/描述

如果你想一起玩，有很多免费的数据集可以玩——我在测试时从 **BigQuery** 获得了一些信息。

```
import os
import pandas as pd
import requests
import json

routes_df = pd.read_csv('datasources/routes.csv').head(10)
token = os.environ.get('mapbox_token') 
```

到目前为止，我们所做的就是加载我们的数据，并从环境变量中保存我们的 Mapbox 令牌。

## 地图框方向端点

接下来，我们将使用 Mapbox 的[方向 API](https://docs.mapbox.com/api/navigation/#directions) 为我们返回一条路线。接收方向的 GET 调用的结构如下所示:

```
https://api.mapbox.com/directions/v5/mapbox/{{method_of_transportation}}/{{origin_longitude}},{{origin_latitude}};{{destination_longitude}},{{destination_latitude}}

PARAMS:
access_token={{your_mapbox_access_token}}
geometries=geojson 
```

*   **交通方式**是指 Mapbox 提供的三种创建路线的方式之一:*驾车交通*、*驾车*、*步行*、*骑行*。请注意，目前没有办法画出跟随公共交通的路线对象:这可能是 Mapbox 目前最大的败笔。然而，如果这是你需要的东西，数据可以从谷歌地图导入，以便与 Mapbox 一起使用。
*   **访问令牌**可以是您的公共令牌(在 mapbox.com 登录时可见)，也可以是生成的秘密令牌。
*   **几何图形**接受绘制对象的方法。这可以是 *GeoJSON、* *polyline、*或 *polyline6。让我们继续关注 GeoJSON。*

## 构造 API 请求

让我们在数据帧中为每一行构造一个请求。通过使用 Pandas 的 apply，我们为每一行触发一个函数来实现这一点:

```
import os
import pandas as pd
import requests
import json

routes_df = pd.read_csv('datasources/routes.csv').head(10)
token = os.environ.get('mapbox_token')

def create_walking_route(row):
    """Get route JSON."""
    base_url = 'https://api.mapbox.com/directions/v5/mapbox/driving/'
    url = base_url + str(row['home_longitude']) + \
        ',' + str(row['home_latitude']) + \
        ';' + str(row['destination_longitude']) + \
        ',' + str(row['destination_latitude'])
    params = {
        'geometries': 'geojson',
        'access_token': token
    }
    req = requests.get(url, params=params)
    route_json = req.json()['routes'][0]
    # Now what?

routes_df.apply(create_walking_route, axis=1) 
```

这就是事情变得有点棘手的地方。你看，GeoJSON 遵守严格的格式。它看起来像这样:

```
{
  "type": "Feature",
  "geometry": {
    "coordinates": [
      [-73.985897, 40.748133], [-73.985046, 40.747773], 
      [-73.984579, 40.748431], [-73.973437, 40.743885],
      [-73.972844, 40.744452], [-73.970728, 40.743885], 
      [-73.970611, 40.735137], [-73.9714, 40.733734],
      [-73.973503, 40.732341], [-73.969823, 40.729864], 
      [-73.969243, 40.727535], [-73.975074, 40.711418],
      [-73.976603, 40.710276], [-73.978077, 40.710587], 
      [-73.979462, 40.70932], [-73.992664, 40.708145],
      [-73.996237, 40.707307], [-74.001135, 40.704086], 
      [-74.0055, 40.70243], [-74.006778, 40.703628],
      [-74.009173, 40.702484], [-74.010637, 40.70371], 
      [-74.014535, 40.703624], [-74.014665, 40.704034],
      [-74.017057, 40.703259]
    ],
    "type": "LineString"
  },
  "legs": [{
      "summary": "",
      "weight": 3873.3,
      "duration": 3873.3,
      "steps": [],
      "distance": 9660.2
  }],
  "weight_name": "duration",
  "weight": 3873.3,
  "duration": 3873.3,
  "distance": 9660.2,
  "properties": {
    "name": "Empire State"
  }
} 
```

为了方便起见，Mapbox Directions API 并不完全以这种格式返回响应。相反，他们的反应是这样的:

```
{
  "routes": [{
    "geometry": {
      "coordinates": [
        [-73.985897, 40.748133],
        [-73.985046, 40.747773],
        [-73.984579, 40.748431],
        [-73.973437, 40.743885],
        [-73.972844, 40.744452],
        [-73.970728, 40.743885],
        [-73.970611, 40.735137],
        [-73.9714, 40.733734],
        [-73.973503, 40.732341],
        [-73.969823, 40.729864],
        [-73.969243, 40.727535],
        [-73.975074, 40.711418],
        [-73.976603, 40.710276],
        [-73.978077, 40.710587],
        [-73.979462, 40.70932],
        [-73.992664, 40.708145],
        [-73.996237, 40.707307],
        [-74.001135, 40.704086],
        [-74.0055, 40.70243],
        [-74.006778, 40.703628],
        [-74.009173, 40.702484],
        [-74.010637, 40.70371],
        [-74.014535, 40.703624],
        [-74.014665, 40.704034],
        [-74.017057, 40.703259]
      ],
      "type": "LineString"
    },
    "legs": [{
      "summary": "",
      "weight": 3873.3,
      "duration": 3873.3,
      "steps": [],
      "distance": 9660.2
    }],
    "weight_name": "duration",
    "weight": 3873.3,
    "duration": 3873.3,
    "distance": 9660.2
  }],
  "waypoints": [{
      "distance": 34.00158252003884,
      "name": "West 33rd Street",
      "location": [
        -73.985897,
        40.748133
      ]
    },
    {
      "distance": 6.627227256764976,
      "name": "",
      "location": [
        -74.017057,
        40.703259
      ]
    }
  ],
  "code": "Ok",
  "uuid": "cjsomodyl025642o6f1jsddx6"
} 
```

这种格式并不差太远，但它的不同足以使它不起作用。

## 正确格式化 GeoJSON

我们需要编写一个函数来获取 Mapbox 给我们的响应，并将其转换为可用的 GeoJSON 格式:

```
import os
import pandas as pd
import requests
import json

routes_df = pd.read_csv('datasources/routes.csv').head(10)
token = os.environ.get('mapbox_token')

def create_route_geojson(route_json, name):
    """Properly formats GeoJson for Mapbox visualization."""
    routes_dict = {
        "type": "Feature",
        "geometry": {
            "type": "LineString"
        },
        "weight_name": "duration",
        "weight": 718.9,
        "duration": 0,
        "distance": 0,
        "properties": {
            "name": ""
        }
    }
    routes_dict['geometry']['coordinates'] = route_json['geometry']['coordinates']
    routes_dict['legs'] = route_json['legs']
    routes_dict['duration'] = route_json['legs'][0]['duration']
    routes_dict['distance'] = route_json['legs'][0]['distance']
    routes_dict['properties']['name'] = name
    with open('dataoutput/' + name + '.json', 'w') as f:
        json.dump(routes_dict, 
                  f, 
                  sort_keys=True, 
                  indent=4, 
                  ensure_ascii=False)

def create_walking_route(row):
    """Get route JSON."""
    base_url = 'https://api.mapbox.com/directions/v5/mapbox/driving/'
    url = base_url + str(row['home_longitude']) + \
        ',' + str(row['home_latitude']) + \
        ';' + str(row['destination_longitude']) + \
        ',' + str(row['destination_latitude'])
    params = {
        'geometries': 'geojson',
        'access_token': token
    }
    req = requests.get(url, params=params)
    route_json = req.json()['routes'][0]
    create_route_geojson(route_json, str(int(row['route_id'])))

routes_df.apply(create_walking_route, axis=1) 
```

它并不漂亮，但是很可靠:我们用`routes_dict`显式地创建了我们需要的 JSON 结构，并用从 Mapbox 返回的 API 响应修改它。当然，对于数据帧中的每一行，我们仍然是一次做一个。

您会注意到，现在我将每个 JSON 文件都保存在本地。将来，我们将编写一个脚本来自动化上传 GeoJSON 对象并将其添加到适当的 Tilesets 的过程，但现在我只想看到我们的工作有了回报！

通过使用 Mapbox studio，我们可以看到第一条路线的结果:

[![Drawing Mapbox Route Objects via the Directions API](img/5b8966619d5c124f6f7fdb7c7f6a3d77.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--of2elMI_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hackersandslackers.com/conteimg/2019/02/Screen-Shot-2019-02-28-at-8.05.21-AM.png) 

<figcaption>一条从帝国大厦到炮台公园的“行驶”路线。</figcaption>

啊哈！你看那个-地图箱知道走罗斯福路。那是一些有希望的东西。

### 全体绘制路线

当然，这只是冰山一角:在我们加载的信息数据框架中，到目前为止我们只看到了一个结果。如果数据中有什么事情值得做，那就必须系统地做上千次，不能失败。幸运的是，Mapbox 为我们提供了这样的工具:从借给我们一个 S3 桶，到通过 API 修改数据集，没有什么可担心的。

下次做更多事情时请继续收听...东西！