# 使用 Python 从 web 地图(xyz 切片服务器)生成合并的 GeoTIFF 影像

> 原文：<https://dev.to/jimutt/generate-merged-geotiff-imagery-from-web-maps-xyz-tile-servers-with-python-4d13>

本指南将向您展示如何以编程方式从 web 地图(通常称为 slippy maps)切片服务器下载地图影像，对其进行地理配准并将其保存为 GeoTIFF。GeoTIFF 图像可以用于其他目的，在我的例子中，我用它来着色激光雷达数据点云。

**先决条件:**

*   非常基础的 Python 知识(我本人真的不是 Python 开发者)
*   GDAL 和它的 Python 绑定(如果你使用的是 [Conda](https://docs.conda.io/en/latest/) 你可以按照下面的说明操作)

如果你只是在这里寻找完成的源代码，你会在这个回购中找到:[https://github.com/jimutt/tiles-to-tiff](https://github.com/jimutt/tiles-to-tiff)

## 网络地图和 XYZ 磁贴格式

大多数在线地图使用共享方式来渲染地图。大多数地图通过使用多个 256x256px 栅格切片来可视化。通过提供它们的 x、y 和 z 坐标来加载正确的图块。其中 z 对应于当前缩放级别。

例如，[开放街道地图](https://osm.org)的图块可以通过以下模式的 URL 获取:【https://a.tile.openstreetmap.org/】T2T4【z】/**{ x }**/**{ y }**。png

x、y 和 z 参数是整数，按照 [OSM 维基](https://wiki.openstreetmap.org/wiki/Slippy_map_tilenames)上的描述工作:

> 在墨卡托投影中，x 从 0(左边缘为 180 W)到 2^zoom 1(右边缘为 180 E)
> Y 从 0(上边缘为 85.0511 N)到 2^zoom 1(下边缘为 85.0511 S)
> 
> 出于好奇，数字 85.0511 是反正切(sinh(π))的结果。通过使用这个边界，整个地图变成了一个(非常大的)正方形。

对于缩放级别为 **n** 的情况，覆盖整个世界所需的图块数量等于 2^n × 2^n。

## 项目设置

我们将结合使用 Python 和 GDAL，GDAL 为地理空间栅格数据的操作提供了很多便利的工具。

我正在使用 [Conda](https://docs.conda.io/en/latest/) 来管理我的 Python 环境，因为我发现它非常方便地设置隔离环境，可以轻松安装第三方依赖项。

创建并激活一个新的依赖于 gdal 的 Conda 环境:

```
conda create -n env_name gdal
conda activate env_name 
```

## 将经纬度转换成相应的图块

创建一个名为`tile_convert.py`的文件，用于从 WGS84 坐标转换到指定缩放级别的相应图块(该填充将包含 WGS84 坐标和图块名称之间转换的各种实用函数):

```
from math import log, tan, radians, cos, pi

def sec(x):
    return(1/cos(x))

def latlon_to_xyz(lat, lon, z):
    tile_count = pow(2, z)
    x = (lon + 180) / 360
    y = (1 - log(tan(radians(lat)) + sec(radians(lat))) / pi) / 2
    return(tile_count*x, tile_count*y) 
```

为了从纬度和经度导出瓦片名称，必须执行许多操作。结合下面的步骤，你将完成`latlon_to_xyz`功能:

1.  计算当前缩放级别下的图块总数:
    tile_count = 2^z

2.  将纬度和经度重新投影到墨卡托投影(EPSG:3857)。
    x = lon
    y = log(tan(lat)+sec(lat))

3.  变换 x 和 y 值以适应 0 - 1 的范围，并将原点设置为左上角。
    x =(lon+180)/360
    y =(1(y/π))/2

4.  将 x 和 y 乘以瓷砖的数量。

接下来，我们将添加一个新函数(到同一个文件中)，它允许我们获取指定矩形区域/边界框的图块范围:

```
def bbox_to_xyz(lon_min, lon_max, lat_min, lat_max, z):
    x_min, y_max = latlon_to_xyz(lat_min, lon_min, z)
    x_max, y_min = latlon_to_xyz(lat_max, lon_max, z)
    return(floor(x_min), floor(x_max),
           floor(y_min), floor(y_max)) 
```

现在我们知道如何计算我们需要哪些单独的瓷砖来覆盖一个边界框，它的角被指定为 WGS84 坐标！

## 瓷砖下载

要下载地图切片，您只需发送一个与切片服务器提供的 URL 规范相匹配的 GET 请求。但是请确保您遵守您正在访问的切片服务器的用户协议/服务条款！

创建一个名为`tiles_to_tiff.py`的新 Python 文件。这将是我们的主要脚本，将使用下载所需的瓷砖，地理参考每个瓷砖，然后将它们合并成一个单一的图像。

```
import math
import urllib.request
import os

#---------- CONFIGURATION -----------# tile_server = "https://api.mapbox.com/v4/mapbox.satellite/{z}/{x}/{y}.png?access_token=" + os.environ.get(
    'MAPBOX_ACCESS_TOKEN')
temp_dir = os.path.join(os.path.dirname(__file__), 'temp')
output_dir = os.path.join(os.path.dirname(__file__), 'output')
zoom = 16
lon_min = 21.49147
lon_max = 21.5
lat_min = 65.31016
lat_max = 65.31688
#-----------------------------------# 

def download_tile(x, y, z, tile_server):
    url = tile_server.replace(
        "{x}", str(x)).replace(
        "{y}", str(y)).replace(
        "{z}", str(z))
    path = f'{temp_dir}/{x}_{y}_{z}.png'
    urllib.request.urlretrieve(url, path)
    return(path) 
```

`download_tile`函数调用指定的图块服务器下载请求的图块，并将其保存为输出目录中的 PNG 文件。

在您的工作区创建一个空的“temp”文件夹，并将下面一行添加到`tiles_to_tiff.py` :

```
download_tile(0,0,0, tile_server) 
```

运行它以验证下载功能是否正常:

```
$ python tiles_to_tiff.py 
```

如果您能够成功执行该脚本，您现在应该会看到一个 256x256px 的 PNG 图像，它显示了 temp 目录中的整个世界。

[![Map tile 0,0,0](img/2a714c2e1cbc7b5189ac25b1946671e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KeIDonWi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7k5l2yghwxx01vwuqq69.png)

### 下载与边界框相交的图块

您很有可能需要一个分辨率比单个单幅图块更高的大图像。因此，我们将使用`bbox_to_xyz`函数来获取所需的图块范围，然后我们将遍历每个图块并将其传递给下载函数。这可以通过多个并行请求来实现，以提高效率，但目前它尽可能保持简单。

```
x_min, x_max, y_min, y_max = bbox_to_xyz(
    lon_min, lon_max, lat_min, lat_max, zoom)

print(f"Downloading {(x_max - x_min + 1) * (y_max - y_min + 1)} tiles")

for x in range(x_min, x_max + 1):
    for y in range(y_min, y_max + 1):
        print(f"{x},{y}")
        download_tile(x, y, zoom, tile_server)

print("Download complete") 
```

上述代码将下载所有所需的切片作为标准的、非地理配准的 PNG 图像。稍后我们将把所有的图片合并成一张图片，为此我们将使用方便的 [gdal_merge.py](https://gdal.org/programs/gdal_merge.html) 脚本。尽管这要求我们首先对输入影像进行地理配准。

## 地理参照

为了对下载的切片进行地理配准，我们首先需要反向执行从纬度和经度到切片名称格式的转换。我们需要知道瓷砖四个角的坐标。然后我们将使用`gdal.Translate`将图像保存为嵌入了地理位置数据的 TIFF 文件。

如果我们记得从 WGS84 坐标到 XYZ 切片名称所需的步骤，我们还可以使用以下函数计算出如何从切片中检索最小和最大经度。将函数添加到`tile_convert.py`文件中。

```
def x_to_lat_edges(x, z):
    tile_count = pow(2, z)
    unit = 360 / tile_count
    lon1 = -180 + x * unit
    lon2 = lon1 + unit
    return(lon1, lon2) 
```

对于纬度转换，我们将使用相同的方法，但是增加了一个额外的函数来从墨卡托投影转换回来:

```
def mercatorToLat(mercatorY):
    return(degrees(atan(sinh(mercatorY))))

def y_to_lat_edges(y, z):
    tile_count = pow(2, z)
    unit = 1 / tile_count
    relative_y1 = y * unit
    relative_y2 = relative_y1 + unit
    lat1 = mercatorToLat(pi * (1 - 2 * relative_y1))
    lat2 = mercatorToLat(pi * (1 - 2 * relative_y2))
    return(lat1, lat2) 
```

有了这些函数，我们将编写一个函数，返回一个包含最小和最大经度和纬度值的数组，其顺序与`gdal.Translate`方法的 [outputBounds 参数](https://gdal.org/python/osgeo.gdal-module.html#TranslateOptions)所期望的顺序相同。在`tile_Convert.py`上增加`tile_edges`功能。

```
def tile_edges(x, y, z):
    lat1, lat2 = y_to_lat_edges(y, z)
    lon1, lon2 = x_to_lon_edges(x, z)
    return[lon1, lat1, lon2, lat2] 
```

最后，我们将在`tiles_to_tiff.py`中编写一个`georeference_raster_tile`函数，然后从下载循环中调用该函数:

```
def georeference_raster_tile(x, y, z, path):
    bounds = tile_edges(x, y, z)
    filename, extension = os.path.splitext(path)
    gdal.Translate(filename + '.tif',
                   path,
                   outputSRS='EPSG:4326',
                   outputBounds=bounds) 
```

```
for x in range(x_min, x_max + 1):
    for y in range(y_min, y_max + 1):
        print(f"{x},{y}")
        png_path = download_tile(x, y, zoom, tile_server)
        georeference_raster_tile(x, y, zoom, png_path) 
```

## 将拼贴合并成一幅大图

因为我们已经包含了对 GDAL 的依赖，所以我选择也使用 GDAL python 脚本来合并图块图像。我们将请求`gdal_merge.py`为我们做这项工作。您需要先为`glob`和`subprocess`添加导入。

```
def merge_tiles(input_pattern, output_path):
    merge_command = ['gdal_merge.py', '-o', output_path]

    for name in glob.glob(input_pattern):
        merge_command.append(name)

    subprocess.call(merge_command) 
```

在您的工作区中创建一个名为“output”的文件夹。根据您的喜好设置`lon_min`、`lon_max`、`lat_min`、`lat_max`以及`zoom`。我正在使用这些设置:

```
zoom = 15
lon_min = 21.49147
lon_max = 21.5
lat_min = 65.31016
lat_max = 65.31688 
```

在`tiles_to_tiff.py`底部添加对`merge_tiles`函数的调用。

```
for x in range(x_min, x_max + 1):
    for y in range(y_min, y_max + 1):
        print(f"{x},{y}")
        png_path = download_tile(x, y, zoom, tile_server)
        georeference_raster_tile(x, y, zoom, png_path)

print("Download complete")

print("Merging tiles")
merge_tiles(temp_dir + '/*.tif', output_dir + '/merged.tif')
print("Merge complete") 
```

### 清除临时目录

在我们运行脚本并查看结果之前，您可能需要添加一个自动清理 temp 文件夹的功能。我通过导入`shutil`来删除临时目录，然后重新创建它:

```
shutil.rmtree(temp_dir)
os.makedirs(temp_dir) 
```

## Showtime！

现在让我们运行脚本并检查输出。我使用先前指定的边界框和来自 Mapbox 的航空影像。

```
$ python tiles_to_tiff.py 
```

输出:

```
Downloading 8 tiles
36680,16917
36680,16918
36680,16919
36680,16920
36681,16917
36681,16918
36681,16919
36681,16920
Download complete
Merging tiles
0...10...20...30...40...50...60...70...80...90...100 - done.
Merge complete 
```

当检查“输出”文件夹时，它现在包含指定区域的单一拼接 TIFF 图像。
[![Output directory containing merged.tif file](img/c06e3b3ffdacb5b1d79d0026de536dad.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--wTt-dKi0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fx060gqccmzn9rlkfxcg.png)

在标准影像查看器中打开时，它看起来与任何其他栅格影像一样，但是如果将它导入到 QGIS 中，您将能够直观地验证它是否已被正确地理配准:

[![Merged TIFF in QGIS](img/5586ad49ea59192468332163341e4642.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Uj3Ok7ZC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/019wxqzpuqwoxvfdv3qw.png)

这就对了。来源如下:[https://github.com/jimutt/tiles-to-tiff](https://github.com/jimutt/tiles-to-tiff)