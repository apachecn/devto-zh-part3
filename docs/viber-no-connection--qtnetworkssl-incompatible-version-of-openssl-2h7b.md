# Viber:无连接–Qt . network . SSL:OpenSSL 版本不兼容

> 原文：<https://dev.to/setevoy/viber-no-connection--qtnetworkssl-incompatible-version-of-openssl-2h7b>

[![](img/b550a333de9511fb0feee7abe59122bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kcBcFSCw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2019/02/viber-logo.png) 在一个全新的 Arch Linux 设置上——Viber 启动，但是在授权期间——无法连接到网络。

总的来说——Viber 的桌面版本确实有限，但无论如何，它比在手机上使用要好。

我试着谷歌了几次，因为我没有发现在谷歌搜索中使用的信息中的错误。，但最后——找到了。

所有错误如下所示:

```
$ viber
Qt WebEngine ICU data not found at /opt/viber/resources. Trying parent directory...
Qt WebEngine resources not found at /opt/viber/resources. Trying parent directory...
Qt WebEngine ICU data not found at /opt/viber/resources. Trying parent directory...
Qt WebEngine resources not found at /opt/viber/resources. Trying parent directory...
qt.network.ssl: QSslSocket: cannot resolve CRYPTO\_num\_locks
qt.network.ssl: QSslSocket: cannot resolve CRYPTO\_set\_id\_callback
qt.network.ssl: QSslSocket: cannot resolve CRYPTO\_set\_locking\_callback
qt.network.ssl: QSslSocket: cannot resolve ERR\_free\_strings
qt.network.ssl: QSslSocket: cannot resolve EVP\_CIPHER\_CTX\_cleanup
qt.network.ssl: QSslSocket: cannot resolve EVP\_CIPHER\_CTX\_init
qt.network.ssl: QSslSocket: cannot resolve sk\_new\_null
qt.network.ssl: QSslSocket: cannot resolve sk\_push
qt.network.ssl: QSslSocket: cannot resolve sk\_free
qt.network.ssl: QSslSocket: cannot resolve sk\_num
qt.network.ssl: QSslSocket: cannot resolve sk\_pop\_free
qt.network.ssl: QSslSocket: cannot resolve sk\_value
qt.network.ssl: QSslSocket: cannot resolve SSL\_library\_init
qt.network.ssl: QSslSocket: cannot resolve SSL\_load\_error\_strings
qt.network.ssl: QSslSocket: cannot resolve SSL\_get\_ex\_new\_index
qt.network.ssl: QSslSocket: cannot resolve SSLv3\_client\_method
qt.network.ssl: QSslSocket: cannot resolve SSLv23\_client\_method
qt.network.ssl: QSslSocket: cannot resolve SSLv3\_server\_method
qt.network.ssl: QSslSocket: cannot resolve SSLv23\_server\_method
qt.network.ssl: QSslSocket: cannot resolve X509\_STORE\_CTX\_get\_chain
qt.network.ssl: QSslSocket: cannot resolve OPENSSL\_add\_all\_algorithms\_noconf
qt.network.ssl: QSslSocket: cannot resolve OPENSSL\_add\_all\_algorithms\_conf
qt.network.ssl: QSslSocket: cannot resolve SSLeay
qt.network.ssl: QSslSocket: cannot resolve SSLeay\_version
qt.network.ssl: Incompatible version of OpenSSL
qt.network.ssl: QSslSocket: cannot call unresolved function SSLv23\_client\_method
qt.network.ssl: QSslSocket: cannot call unresolved function SSL\_library\_init
QQuickItem: Cannot set activeFocusOnTab to false once item is the active focus item.
QQuickItem: Cannot set activeFocusOnTab to false once item is the active focus item.
qt.network.ssl: QSslSocket: cannot call unresolved function SSLv23\_client\_method
qt.network.ssl: QSslSocket: cannot call unresolved function SSL\_library\_init 
```

### 方案 1(不起作用)

我谷歌的第一个是修复建议的`libssl.so`库路径[这里> > >](https://bytefreaks.net/applications/viber-desktop-on-fedora-27-64bit-viber-cannot-connect-to-the-internet-openssl-error) 。

检查文件 itelf:

```
$ ls -l /usr/lib64/libssl.so.1.1
-rwxr-xr-x 1 root root 585328 Nov 20 19:41 /usr/lib64/libssl.so.1.1 
```

创建符号链接:

```
22:52:01 [setevoy@setevoy-arch-pc ~]
$ sudo ln -s /usr/lib64/libssl.so.1.1 /opt/viber/lib/libssl.so 
```

再次运行 Viber 仍然不起作用，同样的错误。

### 方案二(不起作用)

下一个我找到的是讨论[这里> > >](https://forum.manjaro.org/t/unable-to-run-viber-under-manjaro-i3-community-edition-solved/44300/3) 那里提到了`pulseaudio`，它在 Viber 的[依赖项](https://aur.archlinux.org/packages/viber/)中，但是在 Viber 建立的时候没有安装。

安装它:

```
$ sudo pacman -S pulseaudio 
```

重启电脑，运行 Viber——还是没反应。

还有`pulseaudio-git`包——让我们试试:

```
...
==> Install or build missing dependencies for pulseaudio-git:
resolving dependencies...
looking for conflicting packages...
:: jack2-dbus and jack are in conflict. Remove jack? [y/N]
... 
```

什么是`jack2-dbus`？

检查一下描述[这里>>>](https://www.archlinux.org/packages/community/x86_64/jack2-dbus/)——看起来没什么关键的，如果有的话——`pacman`/`yay`会说的。

移除`pulseaudio`:

```
$ sudo pacman -Rsn pulseaudio
checking dependencies...
:: firefox optionally requires pulseaudio: Audio support
:: phonon-qt5 optionally requires pulseaudio: PulseAudio support
:: phonon-qt5-gstreamer optionally requires pulseaudio: PulseAudio support
:: polybar optionally requires pulseaudio: pulseaudio module support

Packages (3) rtkit-0.11+10+g493a135-1  webrtc-audio-processing-0.3.1-1  pulseaudio-12.2-2

Total Removed Size:  6.75 MiB

:: Do you want to remove these packages? [Y/n]
... 
```

安装`pulseaudio-git`:

```
$ yaourt -S pulseaudio-git
...
delete :: jack2-dbus and jack are in conflict. Remove jack? [y/N] y
...
:: pulseaudio-git and libpulse are in conflict. Remove libpulse? [y/N]
... 
```

同意，删除它们，运行 Viber–再次出现同样的错误…

### 方案三(“管用！”)

图书馆到底是怎么回事？

用`ldd`检查:

```
$ ldd /opt/viber/Viber
linux-vdso.so.1 (0x00007ffd2b346000)
libXss.so.1 =\> /usr/lib/libXss.so.1 (0x00007f5e3a0ca000)
libqrencode.so =\> /opt/viber/lib/libqrencode.so (0x00007f5e39eb9000)
libGL.so.1 =\> /usr/lib/libGL.so.1 (0x00007f5e39e26000)
libXext.so.6 =\> /usr/lib/libXext.so.6 (0x00007f5e39c14000)
libX11.so.6 =\> /usr/lib/libX11.so.6 (0x00007f5e39ad5000)
... 
```

一切都好，都找到了…

也只有在这里我注意到了 Viber 的消息中的**Qt . network . SSL:OpenSSL**行的不兼容版本，从而引发了我对[这个> > >](https://bbs.archlinux.org/viewtopic.php?id=237337) 的讨论。

安装 [`openssl-1.0`](https://www.archlinux.org/packages/core/x86_64/openssl-1.0/) :

```
$ sudo pacman -S openssl-1.0 
```

运行 Viber——它现在就可以工作了！

就在成功连接和授权之后，Viber 出现了新的错误:

```
...
PulseAudioService: pa_context_connect() failed
PulseAudioService: pa_context_connect() failed

Assertion 'pthread_mutex_unlock(&m->mutex) == 0' failed at pulsecore/mutex-posix.c:108, function pa_mutex_unlock(). Aborting.

Aborted (core dumped) 
```

为了解决它——我恢复了“普通”`pulseaudio`:

```
$ sudo pacman -S pulseaudio

resolving dependencies...

looking for conflicting packages...

:: pulseaudio and pulseaudio-git are in conflict. Remove pulseaudio-git? [y/N] y

Packages (3) libpulse-12.2-2  pulseaudio-git-v12.0.269.gdb788229c-1 [removal]  pulseaudio-12.2-2

... 
```

将其作为守护程序运行:

```
$ pulseaudio -D 
```

再次启动 Viber:

```
$ viber
Qt WebEngine ICU data not found at /opt/viber/resources. Trying parent directory...
Qt WebEngine resources not found at /opt/viber/resources. Trying parent directory...
Qt WebEngine ICU data not found at /opt/viber/resources. Trying parent directory...
Qt WebEngine resources not found at /opt/viber/resources. Trying parent directory...
qt.network.ssl: QSslSocket: cannot resolve SSLv3\_client\_method
qt.network.ssl: QSslSocket: cannot resolve SSLv3\_server\_method
qml: type=""
qml: type="" 
```

现在一切正常。

### 类似的帖子

*   <small>02/27/2019</small>[viber:нетподключения–Qt . network . SSL:不兼容版本的 OpenSSL](https://rtfm.co.ua/viber-net-podklyucheniya-qt-network-ssl-incompatible-version-of-openssl/) <small>(0)</small>
*   <small>02/20/2019</small> [Viber:找不到 Linux–Qt web engine 资源](https://rtfm.co.ua/en/viber-linux-qt-webengine-resources-not-found-2/) <small>(0)</small>
*   <small>02/20/2019</small> [Viber:找不到 Linux–Qt web engine 资源](https://rtfm.co.ua/viber-linux-qt-webengine-resources-not-found/) <small>(0)</small>
*   <small>03/17/2017</small>[Arch:nm-applet service unknown:org . free desktop . secrets 这个名字不是任何人提供的。服务文件](https://rtfm.co.ua/arch-nm-applet-serviceunknown-the-name-org-freedesktop-secrets-was-not-provided-by-any-service-files/) <small>(0)</small>
*   <small>05/06/2017</small>[Arch Linux:запускаем暗黑破坏神 2](https://rtfm.co.ua/arch-linux-zapuskaem-diablo-2/) <small>(3)</small>