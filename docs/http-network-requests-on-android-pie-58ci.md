# Android Pie 上的 HTTP 网络请求

> 原文：<https://dev.to/varunbarad/http-network-requests-on-android-pie-58ci>

最近，当我开始为公司开发一个新的应用程序时，我注意到我所有的请求甚至在到达服务器之前就被拒绝了。这是我只在模拟器上遇到的问题，而不是在我进行物理测试的 Nexus 5 上遇到的问题。我在网上搜索了一下，发现 Android Pie 已经默认禁用了明文网络流量。

我找到了两个解决方案。您可以从应用中为特定域启用明文流量，也可以为所有域设置明文流量。

## 为所有域启用明文流量(由于不太安全，不推荐)

将该属性添加到您的`AndroidManifest.xml`
中的`<application>`标签

```
android:usesCleartextTraffic="true" 
```

这将为您的应用程序请求的任何和所有域启用应用程序范围的明文流量。

这是不鼓励的，因为你没有明确地只允许需要明文的域。

## 为特定的域启用 HTTP 请求(如果可以的话，使用这个)

您首先需要在您的`res/xml`目录中创建一个名为`network_security_config.xml` file 的文件。文件内容应该如下

```
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <domain-config cleartextTrafficPermitted="true">
        <domain includeSubdomains="true">example-domain.com</domain>
    </domain-config>
</network-security-config> 
```

您需要将`example-domain.com`替换为您想要启用 http 流量的域。如果您想要为多个域启用 http，那么为每个域添加相关的`<domain>`标签。

然后你需要在你的`AndroidManifest.xml`中指定这个文件如下:

```
...
<application
    ...
    android:networkSecurityConfig="@xml/network_security_config">
... 
```

## 这就是所有的乡亲

我在工作中遇到了这个问题，现在我把它写下来，这样其他人(也包括我)将来可以从中受益。如果你有更多这样的想法/例子或任何其他建议，请[联系我](https://varunbarad.com/contact)或发推特给我 [@varun_barad](https://twitter.com/varun_barad) 。