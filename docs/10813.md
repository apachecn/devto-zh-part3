# 在 Raspberry Pi 上设置机器学习环境

> 原文：<https://dev.to/0xkoji/setup-machine-learning-environment-on-raspberry-pi-4n4k>

我在树莓 Pi3 和 Pi3+上做了这个设置。很可能，对于 Pi zero，这个设置需要一个额外的步骤。(将被添加)

安装`berryconda`、`tensorflow`、`keras`、`opencv`

首先改变交换文件大小

```
$ sudo vim /etc/dphys-swapfile
default 100MB --> 1024
$ sudo /etc/init.d/dphys-swapfile stop
$ sudo /etc/init.d/dphys-swapfile start
$ free -m 
                total        used        free      shared  buff/cache   available
Mem:            875          65         378          11         431         747
Swap:          1023           0        1023 
```

Enter fullscreen mode Exit fullscreen mode

## 安装贝里康达

```
# download berryconda
$ wget https://github.com/jjhelmus/berryconda/releases/download/v2.0.0/Berryconda3-2.0.0-Linux-armv7l.sh
$ chmod +x Berryconda3-2.0.0-Linux-armv7l.sh
$ ./Berryconda3-2.0.0-Linux-armv7l.sh 
```

Enter fullscreen mode Exit fullscreen mode

### 创建虚拟环境&激活

```
$ conda create --name mlenv python=3.6
$ source activate mlenv 
```

Enter fullscreen mode Exit fullscreen mode

## Install Tensorflow

这个过程花费了太多的时间(我的情况差不多一个小时)。

```
$ sudo apt-get install libatlas-base-dev
$ pip install --user tensorflow
$ pip list | grep tensor
DEPRECATION: The default format will switch to columns in the future. You can use --format=(legacy|columns) (or define a format=(legacy|columns) in your pip.conf under the [list] section) to disable this warning.
tensorboard (1.11.0)
tensorflow (1.11.0) 
```

Enter fullscreen mode Exit fullscreen mode

## Install Keras

这个过程也花了太多时间(我的情况差不多一个小时)。

```
$ sudo apt install libhdf5-serial-dev
$ pip install h5py
$ pip install keras 
```

Enter fullscreen mode Exit fullscreen mode

## 安装 OpenCV3

```
#　Remove previous version
sudo apt autoremove libopencv3
# Install 
wget https://github.com/mt08xx/files/raw/master/opencv-rpi/libopencv3_3.4.3-20180907.1_armhf.deb
sudo apt install -y ./libopencv3_3.4.3-20180907.1_armhf.deb
sudo ldconfig 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们试着在 Pi
上运行图像分类器，这可能需要很长时间。

```
$ git clone https://github.com/tensorflow/models.git
$ cd models/tutorials/image/imagenet
$ python classify_image.py 
```

Enter fullscreen mode Exit fullscreen mode

我的树莓派笔记回购

【https://github.com/koji/RaspberryPi-Note T2】