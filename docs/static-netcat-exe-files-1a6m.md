# 静态 netcat。exe 文件

> 原文：<https://dev.to/wincentbalin/static-netcat-exe-files-1a6m>

今天我们将为 [netcat](http://nc110.sourceforge.net/) 创建静态的`.exe`二进制文件。

虽然 netcat 有许多[端口和重新实现，但我们将坚持使用](https://en.wikipedia.org/wiki/Netcat#Ports_and_reimplementations) [netcat 1.11](https://github.com/ReneNyffenegger/netcat) 或 [netcat 1.12](https://eternallybored.org/misc/netcat/) 的`win32`端口(后一个支持使用命令行选项`-c`发送`CRLF`行尾，而不是`CR`)。

我们将使用 T2 MXE T3 作为我们的交叉编译环境。按照[教程](https://mxe.cc/#tutorial)中的描述编译`cc`包。将带有交叉编译器的目录添加到您的`PATH`中。

然后从上面的`win32`端口下载并解压源代码，并发出以下命令:

```
i686-w64-mingw32.static-gcc -DNDEBUG -DWIN32 -D_CONSOLE -DTELNET -DGAPING_SECURITY_HOLE getopt.c doexec.c netcat.c -O2 -s -static -lkernel32 -luser32 -lwsock32 -lwinmm -o nc.exe 
```

Enter fullscreen mode Exit fullscreen mode

和

```
x86_64-w64-mingw32.static-gcc -DNDEBUG -DWIN32 -D_CONSOLE -DTELNET -DGAPING_SECURITY_HOLE getopt.c doexec.c netcat.c -O2 -s -static -lkernel32 -luser32 -lwsock32 -lwinmm -o nc64.exe 
```

Enter fullscreen mode Exit fullscreen mode

分别编译 32 位和 64 位版本。

我给你编译了 netcat 1.12，放在一个 [Dropbox 文件夹](https://www.dropbox.com/sh/p55p891k15yisbg/AABtTEKNjMTGgYCDN26vNFG-a)里。

如果你决定使用它们，这里有 MD5 校验和:

```
f630164e41932360440f43602f670d21 nc64.exe
bc3029d3e79265f9ab2e906009adb196 nc.exe 
```

Enter fullscreen mode Exit fullscreen mode

以下是 netcat 教程列表:

*   [https://en.wikipedia.org/wiki/Netcat](https://en.wikipedia.org/wiki/Netcat)
*   [http://nc110.sourceforge.net/](http://nc110.sourceforge.net/)
*   https://www.heise.de/select/ix/2017/11/1509815804306324(德语)