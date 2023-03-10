# 用 mitmproxy 代替 Charles 调试 android 9

> 原文：<https://dev.to/callas1900/debug-android-9-with-mitmproxy-instead-of-charles-3foi>

我用的是**查尔斯**。
我决定停止使用它，因为

*   linux 版的 UI 不好
*   不开源
*   不是命令行

# 安装

你可以从[https://mitmproxy.org/](https://mitmproxy.org/)
下载 tar 文件，解压后你就可以看到了

*   mitmdump
*   mitmproxy
*   mitmweb

你应该把它移到任何你想要的地方。

# 开机

```
mitmproxy 
```

使用默认的 8080 端口。

# 设置您的 android

## 连接您的 android 和 mitmproxy

1.  为您的 android 打开 wifi 设置屏幕
2.  选择您的 wifi 连接设置
3.  点击铅笔图标(编辑按钮)
4.  将代理设置更改为`Manual`
5.  输入您计算机的本地 IP 地址作为`Proxy hostname`
6.  输入 8080 作为`Proxy port`

现在你可以打开浏览器访问任何网站，然后你就可以在 mitmproxy 上看到日志。

## andor id 应用认证

参见:[https://docs.mitmproxy.org/stable/concepts-certificates/](https://docs.mitmproxy.org/stable/concepts-certificates/)

让你通过 mitmproxy 通过任何浏览器访问 [http://mitm.it](http://mitm.it) 。
您可以为每台设备安装认证。

如果您设备的 android 版本低于 9(Pie)。你很幸运可以看到每个应用程序的所有日志。

## 为 Pie 设置您的 android 应用程序

另见:[https://stack overflow . com/questions/55154263/do-not-use-proxy-on-Android-9-pie](https://stackoverflow.com/questions/55154263/do-not-use-proxy-on-android-9-pie)

将 xml 资源文件添加到`res/xml/network_security_config.xml`中

```
<?xml version="1.0" encoding="utf-8"?>
<!-- SECURITY RISK -  This app's network data can now be intercepted!!! -->
<network-security-config>
    <base-config>
        <trust-anchors>
            <!-- Trust preinstalled CAs -->
            <certificates src="system" />
            <!-- Additionally trust user added CAs -->
            <certificates src="user" />
        </trust-anchors>
    </base-config>
</network-security-config> 
```

在`AnroidManifest.xml`
的`<applicaiton>`标签中添加一个属性

```
 <application
...
        android:networkSecurityConfig="@xml/network_security_config" 
```

搞定了。

## 追加

*   一些 android 设备有一个关于 wifi 代理的 bug。
*   不仅仅是 android 9 有时候还需要添加`network_security_config.xml`