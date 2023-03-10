# 制备树莓酱并从嵌入铁锈开始

> 原文：<https://dev.to/h_ajsf/prepare-raspberry-pi-5cpa>

我刚刚得到了我的 Raspberry Pi 3(以及传感器套件)，下面是我如何设置和运行它的:

1.  下载蚀刻机复制 iso。
2.  下载 Raspbian Stretch Lite(基于 Debian Stretch 的极简镜像)，2018 年 11 月版本只有 351MB(带桌面的 Raspbian Stretch 是 1GB，带桌面和推荐软件的 Raspbian Stretch 是 1.8GB)
3.  用蚀刻机刷新 SD 卡
4.  将 Raspberry Pi 3 连接到:
    *   HDMI 电缆连接到屏幕
    *   键盘
    *   老鼠
    *   连接到电源(或电脑)的 USB 电缆
    *   以太网电缆(如果您或您的设备没有 WiFi 网络)
5.  将闪存 SD 卡插入树莓插槽。
6.  打开设备电源(通过将 USB 电缆的另一端连接到计算机或电源)
7.  一旦设备启动(可能需要 2 分钟，取决于您的 SD 卡速度)，您将被提示输入您的登录，默认用户是:`pi`，默认 pswd 是`rasbperry`:

```
Raspbian GNU/Linux 9 raspberrypi tty1
rasbperrypi login: pi 
Password: raspberry 
```

然后，您将准备好使用设备，显示一个:

```
pi@raspberrypi:~$ 
```

1.  对配置进行必要的更改，如`user password`、`WiFi`和`ssh`连接，如下所示:

```
pi@raspberrypi:~$ sudo raspi-config 
```

```
- For `changing password` select `1 Change Password`
- For `wifi` select `2 Network Options`, you'll be asked to select country of the WiFi, then entering the SSID (i.e. the wifi name) and the wifi passphrase, BE CAREFULL both network name and password are case-sensetive.
- For `ssh` select `5 Interfacing Options`, scroll down to `P2 SSH`, and confirm `Yes` as an answer to `Would you like the SSH server to be enabled`
- For `Updating the OS` select `8 Update`
- for `Auto login`, i.e avoid being asked everytime for username and password, select `3 Boot Options` then `B1 Desktop / Cli` then `B2 Console Autologin`
- Change the `keyboard layout if required` you can check if the correct one selected or no, by trying printing `#` and `|` 
```

1.  完成后，单击“完成”返回到命令提示符:

```
pi@raspberrypi:~$ 
```

1.  设置`static ip`

```
// check your current ip
pi@raspberrypi:~$ sudo ifconfig
// update your ip in the DHCPCD configeration file as static ip
pi@raspberrypi:~$ sudo nano /etc/dhcpcd.conf 
```

```
- Scroll down to `define static profile`, and uncommit (i.e. remove the `#`)notice the ip is appearing as `static ip_address=192.168.x.xx`
- Press `Ctrl+X` to exit, confirm `Yes` that you want to save, then press `Enter` to save with the same name 
```

1.  重新启动您的设备:

```
pi@raspberrypi:~$ sudo reboot 
```

1.  要检查您的设备是否连接到互联网，请运行以下程序:

```
$ sudo ping -c 5 www.google.com 
```

1.  现在你可以断开显示器，键盘和鼠标。
2.  在您的另一台设备上，以 ssh 身份登录您的 raspberry pi:

```
ssh pi@192.168.x.xx 
```

1.  如果你想将 raspberry 文件映射到你的系统，这样你就可以用你电脑上的应用程序打开保存在 raspberry 中的文件，你需要从[这里](https://osxfuse.github.io/)有 Fuse 和 SSHFS
    *   在你的电脑上创建映射文件夹，比如说`ssh_pi`

```
$ mkdir ~/ssh_pi
//Map the raspberry pi `home` folder
$ sshfs pi@192.168.x.xx:/home/pi ~/ssh_pi
pi@192.168.1.43's password: 
```

从 keyboard / ssh / FUSE_SSHFS 安装你的应用程序，并以你喜欢的方式工作，我可以安装 rust

```
$ curl https://sh.rustup.rs -sSf | sh 
```

制作我的第一个应用程序

```
$ cargo new my_rust 
```

在我的 mac 上打开 Intellij 上的文件。
现在将开始玩[锈埋](https://docs.rust-embedded.org/book/)和[物理计算锈覆盆子](https://rahul-thakoor.github.io/physical-computing-with-rust-on-raspberry-pi/)和[物理计算锈](https://rahul-thakoor.github.io/physical-computing-rust/)

注意:在 raspberry 上编译很慢，所以最好在你的 PC 上为 Raspberry 平台编译，在你的`ssh_pi`文件夹中工作，然后它将与 Pi 平滑同步。

在您的 PC 中，在终端打开`ssh_pi`文件夹，并写入:

```
// get the raspberry pi platform target
$ rustup target add armv7-unknown-linux-gnueabihf
// set the raspberry pi platform as default target for this folder (ssh_pi if covring all the folders in the raspberry pi)
$ rustup override set stable-armv7-unknown-linux-gnueabihf
// To confirm you set the atrget correctly
$ rustup show
// To compile for rasberry pi in your pc
$cargo build --target=armv7-unknown-linux-gnueabihf 
```

要关闭您的 Pi

```
$ sudo shutdown 
```