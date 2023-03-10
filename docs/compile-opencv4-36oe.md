# 编译 OpenCV4

> 原文：<https://dev.to/lucifer1004/compile-opencv4-36oe>

因为 OpenCV4 不生成。pc 文件默认情况下，如果其他程序需要 pkg-config 文件，我们需要从源代码编译它并打开`OPENCV_GENERATE_PKGCONFIG`选项。

我遇到的另一个问题是一些类似于`undefined reference to TIFFReadRGBAStrip@LIBTIFF_4.0`的错误。要解决这个问题，需要打开`BUILD_TIFF`选项。

下面是编译和安装的脚本。

```
wget https://github.com/opencv/opencv/archive/4.1.0.zip
unzip opencv-4.1.0.zip
cd opencv-4.1.0
mkdir build && cd build
cmake -D CMAKE_BUILD_TYPE=Release -D OPENCV_GENERATE_PKGCONFIG=YES -D CMAKE_INSTALL_PREFIX=/usr/local -D BUILD_TIFF=ON
sudo make -j`nproc`
sudo make install 
```