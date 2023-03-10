# 带有人脸检测和自动发布功能的 Instagram 刮刀

> 原文：<https://dev.to/reliefs/instagram-scraper-with-autopost-51p8>

[![image](img/1043c71617e0420598c3e40de9670ca0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--psaz2qgG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/reliefs/Instagram-scraper-with-autopost/master/img/banner.png)

* * *

### [网站](https://instascraper.github.io/) | [看文档](https://instascraper.github.io/docs/) | [投稿](https://github.com/instagrambot/docs/blob/master/CONTRIBUTING.md) | [购买 Instagram 专家营销](https://www.fiverr.com/hourapp/grow-your-instagram-followers-for-7-days)

* * *

Instagram scraper 使用机器学习来识别人脸，如果检测到人脸，它会重新发布照片。

## 通过网络摄像头实时检测工作中的人脸

[![image](img/2d780a2e52fb7cd43bf24f814012e44f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XBq-kX4d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/practicaldev/image/fetch/s--qdvR8Vl8--/c_limit%252Cf_auto%252Cfl_progressive%252Cq_66%252Cw_880/https://cloud.githubusercontent.com/assets/896692/24430398/36f0e3f0-13cb-11e7-8258-4d0c9ce1e419.gif)

脚本现在保存用户会话并拥有 CLI 会话。
我还把它和 instabot api 集成在了一起。90%都是垃圾代码，我以后会修复的。

这个脚本从用户那里抓取图片，然后用你自己的标签重新发布到你的 Instagram 账户下。

演示:
[https://www.instagram.com/siliconeheaven/](https://www.instagram.com/siliconeheaven/)

## 要求

*   Python 3.6 以上版本
*   instaprofiles.txt 中至少有 10-20 个个人资料

该脚本不适用于新帐户。如果你知道如何修复，给我发消息。

## 用 Docker 安装脚本:

```
git clone https://github.com/reliefs/Instagram-scraper-with-autopost.git

cd Instagram-scraper-with-autopost

Create a file called secret.txt inside that folder with the following info:
yourusername:yourpassword
Save it

change yourusername to your instagram username in file example.py line 31: InstaUsername = "yourusername"

Docker build .

Docker run [imageid] 
```

## 在 Windows 上安装脚本:

### 安装 Cmake

下载链接:[https://github . com/Kitware/CMake/releases/download/v 3 . 14 . 1/CMake-3 . 14 . 1 . zip](https://github.com/Kitware/CMake/releases/download/v3.14.1/cmake-3.14.1.zip)

### 安装 Dblib

下载 dlip。作为 ur 系统要求的‘wheel’文件(使用下面的链接)
下载链接:[https://pypi.python.org/simple/dlib/](https://pypi.python.org/simple/dlib/)T3】

```
Open cmd navigate to dlib wheel file path and hit command
pip install dlib_file_name.wheel 
```

### 然后运行

```
git clone https://github.com/instagrambot/Instagram-scraper-with-autopost --recursive 
```

```
cd Instagram-scraper-with-autopost 
```

```
pip install -r requirements.txt 
```

```
change yourusername to your instagram username in file example.py line 31: InstaUsername = "yourusername" 
```

```
change instaprofiles.txt to the instagram profiles you wanna scrape 
```

```
Run: python example.py -u yourusername 
```

然后按回车键。

## 在 Linux 上安装脚本:

```
Ubuntu:
apt-get install python-dev python3-dev
sudo apt install g++
sudo apt install cmake
sudo apt install python3-pip

Arch Linux:
sudo pacman -S python3-dev
sudo pacman -S cmake
sudo pacman -S python3-pip 
```

首先，确保已经用 Python 绑定安装了 dlib:

*   [如何在 macOS 或 Ubuntu 上从源码安装 dlib】](https://gist.github.com/ageitgey/629d75c1baac34dfa5ca2a1928a7aeaf)

### 接着做

```
git clone https://github.com/reliefs/Instagram-scraper-with-autopost.git

cd Instagram-scraper-with-autopost

sudo pip install -r requirements.txt

change yourusername to your instagram username in file example.py line 31: InstaUsername = "yourusername"

change instaprofiles.txt to the instagram profiles you wanna scrape

Run: python3 example.py 
```

## 故障排除

如果你正在使用 face_recognition 获取非法指令，请遵循以下指南:
[https://github . com/ageitgey/face _ recognition/issues/11 # issue comment-475482716](https://github.com/ageitgey/face_recognition/issues/11#issuecomment-475482716)

AttributeError:“模块”对象没有属性“face_recognition_model_v1”

解决方案:您安装的 dlib 版本太旧。您需要 19.7 或更高版本。升级 dlib。

做你他妈的想做的事情，复制、分发和修改公共许可条款和条件。

1.  你他妈想做什么就做什么。