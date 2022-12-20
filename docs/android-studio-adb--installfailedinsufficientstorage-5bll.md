# Android Studio:ADB–安装失败存储不足

> 原文：<https://dev.to/setevoy/android-studio-adb--installfailedinsufficientstorage-5bll>

[![](img/2c9cf22a6277c4a4f7aa6ca73efeef7e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SW2_tLZN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2018/11/logo-androidstudio.png) 继续与我们的 UI 自动测试战斗 [![🙂](img/42c484ce6bf0d9433de29d1f04c1cd48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i5n0Ut5W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11.2.0/72x72/1f642.png)

之前的两个帖子这次真的很有用——[Android studio:установкаLinuxиииаботасAndroid 虚拟设备](https://rtfm.co.ua/android-studio-ustanovka-na-linux-i-rabota-s-android-virtual-devices/)和 [Appium: Android 虚拟设备，原错误:60159 ms и костыль](https://rtfm.co.ua/appium-android-virtual-device-original-error-condition-unmet-after-60159-ms-i-kostyl/) 后条件未满足。

总的来说——测试工作正常，但有时会给我带来有趣的问题。

这次 QA 工程师给我发来了新的错误:

```
[debug] [W3C] Encountered internal error running command: Error executing adbExec. Original error: 'Command '/android/Android/sdk/platform-tools/adb -P 5037 -s emulator-5554 install -r -g /home/projectname/projectnameMenBuilds/projectnameMen-stage.apk' exited with code 1'; Stderr: 'adb: failed to install /home/projectname/projectnameMenBuilds/projectnameMen-stage.apk: Failure [INSTALL_FAILED_INSUFFICIENT_STORAGE]'; Code: '1'
[debug] [W3C] Error: Command '/android/Android/sdk/platform-tools/adb -P 5037 -s emulator-5554 install -r -g /home/projectname/projectnameBuilds/projectnameMen-stage.apk' exited with code 1
[debug] [W3C]     at ChildProcess.proc.on.code (/home/projectname/node_modules/teen_process/lib/exec.js:94:19)
[debug] [W3C]     at emitTwo (events.js:126:13)
[debug] [W3C]     at ChildProcess.emit (events.js:214:7)
[debug] [W3C]     at maybeClose (internal/child_process.js:925:16)
[debug] [W3C]     at Process.ChildProcess._handle.onexit (internal/child_process.js:209:5)
[HTTP] <-- POST /wd/hub/session 500 2312 ms - 1165
[HTTP]
[HTTP] --> POST /wd/hub/session
[HTTP] 
```

第一个显眼的消息——“***失败【安装 _ 失败 _ 不足 _ 存储】”；*代号:‘1’**。

在第一个带有“*Android INSTALL _ FAILED _ INSUFFICIENT _ STORAGE*”的谷歌请求上，我在 StackOverflow 上找到了[这个> > >](https://stackoverflow.com/questions/4709137/solution-to-install-failed-insufficient-storage-error-on-android) 问题和那里的一个答案，描述了几乎相同的问题——[这里> > >](https://stackoverflow.com/a/8105497/2720802) 。

让我们检查一下。

运行测试中使用的模拟器:

```
projectname@projectname-ci:~$ emulator -avd Nexus_5X_API_26 
```

在那里运行 shell:

```
projectname@projectname-ci:~$ adb shell
generic_x86:/ $ 
```

检查可用空间:

```
1|generic_x86:/ $ df -h
Filesystem      Size  Used Avail Use% Mounted on
rootfs          611M  4.6M  606M   1% /
tmpfs           756M  488K  756M   1% /dev
/dev/block/vda1 2.4G  1.4G  0.9G  59% /system
/dev/block/vde1  90M   35M   53M  40% /vendor
tmpfs           756M     0  756M   0% /mnt
/dev/block/vdb   61M   56K   60M   1% /cache
/dev/block/dm-0 1.9G  1.7G  148M  93% /data
/data/media     1.9G  1.7G  148M  93% /storage/emulated 
```

*148m 93%/data*–148 MB，显然不算多。

我还没有看过我们的应用程序使用的 apk 文件，但无论如何——太低的可用空间。

试检查`/data`内容:

```
1|generic_x86:/ $ ls -l /data/
ls: /data/: Permission denied 
```

啊哈…

尝试使用根目录:

```
projectname@projectname-ci:~$ adb root
adbd cannot run as root in production builds 
```

嗯——好吧。有一些关于如何获得 root 访问权限的解决方案，但是我现在不想为此浪费时间。

只会增加用于此设备的磁盘–构建主机上有足够的可用空间。

接下来用“ *android 模拟器磁盘空间*”进行谷歌搜索，把我带到了[快速提示:增加你的 android 模拟器](https://www.appcelerator.com/blog/2013/04/quick-tip-increase-the-storage-size-of-your-android-emulator/)帖子的存储空间。

查找设备使用的目录:

```
projectname@projectname-ci:~$ ls -l ~/.android/avd/
total 16
drwxrwxr-x 5 projectname projectname 4096 бер  4 11:35 Nexus_5X_API_26.avd
-rw-rw-r-- 1 projectname projectname  127 лис 20 17:50 Nexus_5X_API_26.ini
drwxrwxr-x 4 projectname projectname 4096 гру 18 18:44 test.avd
-rw-rw-r-- 1 projectname projectname  105 лис 19 17:18 test.ini 
```

而`~/.android/avd/Nexus_5X_API_26.avd`:

```
projectname@projectname-ci:~$ ls -la ~/.android/avd/Nexus_5X_API_26.avd/
total 2861472
drwxrwxr-x 5 projectname projectname       4096 бер  4 11:35 .
drwxrwxr-x 4 projectname projectname       4096 бер  4 11:41 ..
-rw-rw-r-- 1 projectname projectname         96 бер  4 11:35 AVD.conf
-rw-r--r-- 1 projectname projectname   69206016 бер 12  2018 cache.img
-rw-r--r-- 1 projectname projectname   42598400 бер  4 11:35 cache.img.qcow2
-rw-rw-r-- 1 projectname projectname       1173 лют 14 11:10 config.ini 
```

下面是上面帖子里提到的`config.ini`:

```
projectname@projectname-ci:~$ cat ~/.android/avd/Nexus_5X_API_26.avd/config.ini | grep data
disk.dataPartition.size=2G 
```

在这里设置一个新值，比如说 5 GB:

```
...
disk.dataPartition.size=5G
... 
```

再次运行模拟器:

```
projectname@projectname-ci:~$ emulator -avd Nexus_5X_API_26
emulator: WARNING: userdata partition is resized from 2048 M to 5120 M 
```

外壳:

```
projectname@projectname-ci:~$ adb shell
error: device unauthorized.
This adb server's $ADB_VENDOR_KEYS is not set
Try 'adb kill-server' if that seems wrong.
Otherwise check for a confirmation dialog on your device. 
```

呃……好吧——杀死`avd`:

```
projectname@projectname-ci:~$ adb kill-server 
```

再次脱壳:

```
projectname@projectname-ci:~$ adb shell
* daemon not running; starting now at tcp:5037
* daemon started successfully 
```

立即检查磁盘空间:

```
generic_x86:/ $ df -h
Filesystem      Size  Used Avail Use% Mounted on
rootfs          611M  4.6M  606M   1% /
tmpfs           756M  488K  756M   1% /dev
/dev/block/vda1 2.4G  1.4G  0.9G  59% /system
/dev/block/vde1  90M   35M   53M  40% /vendor
tmpfs           756M     0  756M   0% /mnt
/dev/block/vdb   61M   56K   60M   1% /cache
/dev/block/dm-0 4.8G   88M  4.7G   2% /data
/data/media     4.8G   88M  4.7G   2% /storage/emulated 
```

运行测试——现在一切正常。

### 类似的帖子

*   <small>12/06/2018</small>[appium:Android 虚拟设备，原错误:60159 ms 后条件未满足и костыль](https://rtfm.co.ua/appium-android-virtual-device-original-error-condition-unmet-after-60159-ms-i-kostyl/) <small>(0)</small>
*   <small>11/21/2018</small> [安卓工作室:установканаLinuxииаботас安卓虚拟设备](https://rtfm.co.ua/android-studio-ustanovka-na-linux-i-rabota-s-android-virtual-devices/) <small>(0)</small>
*   <small>12/12/2018</small>[Android Studio:emulator–qt _ setup . CPP:28:找不到 Qt 库](https://rtfm.co.ua/android-studio-emulator-qt_setup-cpp28qt-library-not-found/) <small>(0)</small>
*   <small>03/01/2019</small>[Linux:systemd-unit 文件编辑，故障重启和邮件通知](https://dev.to/setevoy/linux-systemd-unit-files-edit-restart-on-failure-and-email-notifications-5h3k) <small>(0)</small>