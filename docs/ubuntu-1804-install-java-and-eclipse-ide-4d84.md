# Ubuntu 18.04 安装 Java 和 Eclipse IDE

> 原文：<https://dev.to/baddate/ubuntu-1804-install-java-and-eclipse-ide-4d84>

# 要求

1.  下载一个`JDK` gz 文件。
    [https://www . Oracle . com/tech network/Java/javase/downloads/index . html](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

2.  下载`Eclipse IDE installer` gz 文件。
    https://www.eclipse.org/downloads/download.php?[file =/oomph/EPP/2018-12/R/eclipse-inst-Linux 64 . tar . gz](https://www.eclipse.org/downloads/download.php?file=/oomph/epp/2018-12/R/eclipse-inst-linux64.tar.gz)
    可以选择镜像站点下载。

# Config JDK

## 解压并安装 Java

`tar -xvf ~/Downloads/jdk1.8.0_201.tar.gz`

## 创建一个文件夹来存储它。

`sudo mkdir /usr/lib/jvm/jdk1.8.0_201`

## 将解压后的包移动到目录下。

`sudo mv jdk1.8.0_201*/* /usr/lib/jvm/jdk1.8.0_201/`

## 运行下面的命令来配置 java。

```
sudo update-alternatives --install "/usr/bin/java" "java" "/usr/lib/jvm/jdk1.8.0_201//bin/java" 1010 
sudo update-alternatives --install "/usr/bin/javac" "javac" "/usr/lib/jvm/jdk1.8.0_201//bin/javac" 1010 
sudo update-alternatives --install "/usr/bin/javaws" "javaws" "/usr/lib/jvm/jdk1.8.0_201//bin/javaws" 1010 
```

## 创造`Java Environment Variables`。

新建一个文件。
`sudo vim /etc/profile.d/javajdk.sh`

将这一行复制到`javajdk.sh`到

```
export J2SDKDIR=/usr/lib/jvm/jdk1.8.0_201
export J2REDIR=/usr/lib/jvm/jdk1.8.0_201/jre
export PATH=$PATH:/usr/lib/jvm/jdk1.8.0_201/bin:/usr/lib/jvm/jdk1.8.0_201/db/bin:/usr/lib/jvm/jdk1.8.0_201/jre/bin
export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_201
export DERBY_HOME=/usr/lib/jvm/jdk1.8.0_201/db 
```

使其有效。
`source /etc/profile.d/javajdk.sh`

测试是否成功。
运行命令`java -version`，会显示这样的消息

```
java version "1.8.0_201"
Java(TM) SE Runtime Environment (build 1.8.0_201-b09)
Java HotSpot(TM) 64-Bit Server VM (build 25.201-b09, mixed mode) 
```

如果是，恭喜你！

# 安装 Eclipse

解压`eclipse installer` gz 文件，然后运行`eclipse-inst`可执行文件。
或者运行`eclipse installer`方向
T3 上的命令。

接下来，是一些简单的步骤！

# 在应用程序选择器上添加图标

## 第一步:将当前方向改为`/usr/share/applicaitons`

`cd /usr/share/applicaitons`

## Step2:新建一个`Java.desktop`文件。

输入行:

```
[Desktop Entry]
Encoding=UTF-8
Name=Java #name it
Comment=Eclipse-Java
Exec=/path/eclipse #your path
Icon=/path/icon.xpm #your path
Terminal=false
StartupNotify=true
Type=Application
Categories=Application;Development; 
```

## 第三步:给它执行权限。

运行命令:
`sudo chmod +x Java.desktop`

# 引用

[https://website for students . com/install-Oracle-Java-JRE-JDK-on-Ubuntu-18-04-lts-beta/](https://websiteforstudents.com/install-oracle-java-jre-jdk-on-ubuntu-18-04-lts-beta/)

**这是我的第一篇帖子，如有错误，请指正:)**