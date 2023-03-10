# 空间关系

> 原文：<https://dev.to/korenmiklos/spatial-relations-5e5f>

测量通常具有空间维度。如果[思考时间间隔](https://dev.to/korenmiklos/spells-221a)觉得复杂，欢迎来到[T3】空间关系 T5。在时间中只有点和间隔的地方，在空间中有更多不同类型的物体和更多不同的关系。一个观测可能与一个点(如传感器)、一条线(如河流或高速公路)或一个区域(在空间分析中通常称为*多边形*)(如城市)相关。](https://en.wikipedia.org/wiki/Spatial_relation)

[![Photo by Fleur Treurniet on Unsplash](img/db244f6526aff87c9bd422dfa068e905.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a2Av1RMf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AAvKFJTeB8sPSxG5Q)

这些空间实体彼此之间可能有许多关系。传感器可能位于城市内部。公路可能在某一点与河流相交。一条高速公路可能穿过这座城市。一条河可以作为城市的边界。

> ### 简单特征
> 
> 一个**点**由一对坐标(x，y)给出。(我们忽略 3D，只处理地球表面。)一条**线**是一列连接点(x1，y1) - (x2，y2) -...一个**区域**是由一条闭合的线(x1，y1) - (x2，y2)包围的多边形...- (x1，y1)。你可以收集这些物品。国家通常是一堆感叹词。

[![By Krauss - Own work, CC BY-SA 3.0, https://commons.wikimedia.org/w/index.php?curid=21299138](img/006bcb26fc3f31c994391e2b2da01695.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TNyq-0kR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/5/55/TopologicSpatialRelarions2.png)

理解空间关系的首要任务是理解您所拥有的空间观测的类型。城市不是点，尽管当你在谷歌地图中输入城市名称时，它们肯定会出现中点或中心。城市是区域。事实上，很少有实体是真正的点，尽管有些可以合理地近似为点。包括街道号在内的精确街道地址可以安全地近似为其地理坐标。

从人类可读的地址到机器可读的 GPS 坐标被称为**地理编码**。我们每天在谷歌地图中输入地址时都会这样做。要以可扩展的方式对数据集中的所有观测值执行此操作，您需要地理编码服务。Google Maps 有一个 API，但是只允许为了在地图上显示点而进行地理编码。对于批量地理编码，你应该使用 OpenStreetMap 数据，求助于其他提供商，如[提名](https://nominatim.openstreetmap.org/)。

> ### 投影和空间参考系统
> 
> 地理编码将地址转换为一对坐标:纬度和经度。但是这些坐标是什么意思呢？由于两个数字代表一个平面，所以问题是如何将地球表面(与某些说法相反，地球不是平的)的点映射到平面上的点。这种映射被称为**投影**。有许多投影，取决于他们假设的地球形状，地球与完美的球体略有不同。是的，有一种投影分类，叫做[空间参考系统标识符](https://en.wikipedia.org/wiki/Spatial_reference_system)。目前最广泛使用的是[世界大地测量系统](https://en.wikipedia.org/wiki/World_Geodetic_System#WGS84)，WGS84，其 SRID 为 4326。这是你在谷歌地图和全球定位系统中看到的。(墨卡托投影就是你在旧的印刷地图上看到的，格陵兰岛看起来比非洲还大。不要在真实数据中使用墨卡托。)

如果你经常使用空间数据，你应该投资去了解更多关于地理信息系统的知识。有专门的 GIS 软件来绘制空间数据或进行空间分析，如 ESRI ArcGIS、MapInfo 或开源的[量子 GIS](https://www.qgis.org/en/site/) 。很多数据库管理工具还实现了空间查询，所以你可以很容易地选择“这条路 10 公里范围内的所有加油站。”

虽然空间中的点可以很容易地用两个数字来表示，但更丰富的空间要素需要它们特殊的文件格式。[知名文本](https://en.wikipedia.org/wiki/Well-known_text)提供了空间特征的简单文本表示，如`LINESTRING (30 10, 10 30, 40 40)`。这非常直观，但在实践中没有太大帮助，因为线和多边形有数千个顶点。 [GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) 是 JSON 的开放标准扩展。如果您习惯于使用 web 应用程序和 JSON 数据，请将您的空间信息转换为 GeoJSON 标准。到目前为止，所有主要的 GIS 软件包都可以读写 GeoJSON。还有 ESRI Shapefiles 的专有二进制文件格式。因为 ArcGIS 软件包无处不在，所以这些工具被广泛使用。例如，美国人口普查局在 ESRI 形状文件中发布了人口普查区域的[边界](https://www.census.gov/geo/maps-data/data/tiger-line.html)。