# 我的 GDAL CLI 片段

> 原文：<https://dev.to/lucifer1004/my-gdal-cli-snippets-12ci>

## 根据 shapefile 裁剪光栅文件

```
gdalwarp -overwrite -s_srs EPSG:32649 -q -cutline shapefile.shp -of GTiff raw.tiff crop.tiff 
```

## 将几个波段合并成一个光栅文件

```
gdal_merge.py -o target.tiff -of GTiff -ps 10 10 -separate B1.tiff B2.tiff B3.tiff 
```

这里的`10 10`是指 x 轴 10 米，y 轴 10 米。如果未指定`-ps`，第一个光栅文件的空间分辨率将被视为目标分辨率。

## 在空间上合并多个文件

```
gdal_merge.py -o target.tiff -of GTiff TL.tiff BL.tiff TR.tiff BR.tiff 
```

## 对光栅文件进行重采样

```
gdal_translate -outsize 50 50 raw.jp2 target.jp2 
```

这里`50 50`表示 x 轴 50%，y 轴 50%。

* * *

## 附录:在 Ubuntu 上安装 GDAL for Python

```
# Install gdal library
sudo apt-get install -y gdal-bin libgdal-dev
# gdal-bin is not necessary, but you will definitely need it to run the CLI commands above

# Export include path
export CPLUS_INCLUDE_PATH=/usr/include/gdal
export C_INCLUDE_PATH=/usr/include/gdal

# Install the exact version of Python package
gdal-config --version | xargs -0 -I {} pip install gdal=={} 
```