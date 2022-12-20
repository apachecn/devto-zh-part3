# 对让你保持理智的自然清理实践做出反应。

> 原文：<https://dev.to/mjmaix/react-native-cleanup-practices-that-will-keep-you-sane-4o5j>

这里的所有命令都可以单独执行。不要害怕每隔一段时间或者当意想不到的事情发生时清理 React 原生项目。

首先做好清理工作通常可以省去提交问题单和等待别人建议你重新安装应用程序的麻烦..等等。

## 脚本会做什么？

1.  清理 react 依赖项和缓存

2.  清理 metro bundler 缓存和文件

3.  清理反应-本机加速缓存引用

4.  清理 ios 和 android 文件

5.  使用`npm start`时总是重置缓存

6.  可选地在每个软件包安装后运行脚本文件

7.  在每个软件包安装后运行`pod install`

## 如何使用:

~~不要忘记在运行之前先关闭所有 Metrobundler(或类似的)实例。~~添加了一个 killpackager 脚本，每隔`npm start`
关闭所有仿真器或模拟器
`run cleanup:all`并休息一会儿就启动一个关闭和新实例

请在此处查看代码:

[React 本机清理脚本](https://gist.github.com/mjmaix/4f37e38ce8846ea02b931c9f9103c5f7) 

## 错误没有修复怎么办？

去阅读错误日志，并询问社区是否需要。使用存储库问题跟踪器或 stackoverflow