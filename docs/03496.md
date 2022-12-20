# 在 Windows 10 上使用 OpenSSL 和 WAMP 启用 SSL

> 原文：<https://dev.to/nahuelhds/enable-ssl-with-openssl-and-wamp-on-windows-10-2ni1>

[![](img/52928b50f68449aea3a4ea29241c1cf3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E92Dk_92--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nahuelhds.dev/img/0__HC__ouHIX5ypCpxsF.jpg)

这是我的环境:Windows 10 x64，WampServer 版本 3 . 0 . 4 64 位和 Apache 2.4.18

### TL；速度三角形定位法(dead reckoning)

说明在 WampServer 论坛的这个帖子里。

如果你不能启动 Apache，可能是你的配置有问题。下一节将介绍我的问题。你可以看到你的错误直接在 **cmd** 上执行`httpd`。

```
cd \\wamp\bin\apache\apachex.y.z\binhttpd -t 
```

这将显示配置文件和行中的错误。

#### 重要

有必要使用来自 WAMP 的非本地版本的 OpenSSL。我从 SourceForge 的页面下载了我的[。下载的文件是](https://sourceforge.net/projects/openssl/)[https://ufpr . dl . SourceForge . net/project/OpenSSL/OpenSSL-1 . 0 . 2j-FIPS-x86 _ 64/OpenSSL-1 . 0 . 2j-FIPS-x86 _ 64 . zip](https://ufpr.dl.sourceforge.net/project/openssl/openssl-1.0.2j-fips-x86_64/openssl-1.0.2j-fips-x86_64.zip)

### 我曾面对的错误

#### WAMP 上的原生 OpenSSL:“找不到序数(…)”

在 *WampServer 64bits* 上， **OpenSSL** 可执行文件在 *Windows 10 64bits* 上无法运行，所以你需要为它下载一个工作版本。

我特别的错误是“序数 113 在动态链接库中找不到”。解决方案是我自己安装 OpenSSL，如上所述。

#### WAMP httpd:“无效命令 SSLCipherSuite”

取消对`httpd.conf`文件中该行的注释。

```
LoadModule ssl_module modules/mod_ssl.so 
```

您可以按照来源的说明进行操作。

**来源:**[http://impradeep . com/invalid-command-sslciphersuite-maybe-misselled-or-defined-by-a-module-not-included-in-the-server-configuration/](http://impradeep.com/invalid-command-sslciphersuite-perhaps-misspelled-or-defined-by-a-module-not-included-in-the-server-configuration/)

#### WAMP httpd:“无法将模块/mod_ssl.so 加载到服务器”

这里有两条可能的路径。这些秒钟对我来说很有用。

**在 Windows 目录中包含 OpenSSL 的 DDL**

您必须安装不同的 OpenSSL 版本。可以看到这个 StackOverlow 的回答，按照说明来:[http://stack overflow . com/questions/40017498/cannot-load-modules-mod-SSL-so-into-server](http://stackoverflow.com/questions/40017498/cannot-load-modules-mod-ssl-so-into-server)

**替换阿帕奇目录上的 libeay32.dll 和 ssleay32.dll**

很奇怪但是 WAMP 64 船的那些文件*libeay32.dll*和*ssleay32.dll*都不能用 SSL 模块。你需要下载一个 Apache 32bits 版本，并复制&粘贴到`\\wamp\bin\apache\apachex.y.z\bin`。

我从这个 Apache 版本中取出了文件[。](https://www.apachelounge.com/download/win32/)

明确的说，这个链接:[https://www . Apache lounge . com/download/win32/binaries/httpd-2 . 2 . 32-win32 . zip](https://www.apachelounge.com/download/win32/binaries/httpd-2.2.32-win32.zip)。

**来源:**[http://server fault . com/questions/477706/Apache-SSL-on-64-bit-windows-not-a-valid-win32-application](http://serverfault.com/questions/477706/apache-ssl-on-64-bit-windows-not-a-valid-win32-application)。