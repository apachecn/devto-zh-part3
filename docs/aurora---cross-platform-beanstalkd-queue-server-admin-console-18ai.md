# 跨平台 beanstalkd 队列服务器管理控制台。

> 原文：<https://dev.to/xuri/aurora---cross-platform-beanstalkd-queue-server-admin-console-18ai>

[![aurora](img/54c2ba5550eb23003152c1ce27162552.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rl0tg5TS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/xuri/aurora/raw/master/aurora.png)

GitHub:[github.com/xuri/aurora](https://github.com/xuri/aurora)

## 概述

aurora 是一个基于 web 的 Beanstalkd 队列服务器控制台，用 Go 编写，可以在 macOS、Linux 和 Windows 机器上运行。使用 Go 进行后端开发的主要思想是利用编译器的能力为多个平台生成零依赖的二进制文件。创建 aurora 是为了尝试构建一个非常简单且可移植的应用程序，以便与本地或远程 Beanstalkd 服务器一起工作。

## 特性

*   跨平台支持 macOS/Linux/Windows 32/64 位
*   简单安装(作为单个二进制文件分发)
*   零依赖性
*   所有用户配置中的通用服务器列表+可选的基本身份验证
*   可用试管的完整列表
*   关于试管中作业的完整统计
*   实时自动更新，突出显示更改的值
*   您可以查看每个试管中处于就绪/延迟/隐藏状态的作业
*   您可以添加/取消/删除每个试管中的作业
*   您可以通过正则表达式选择多个试管并清除它们
*   您可以为每个试管设置统计概览图
*   您可以在试管之间移动作业
*   暂停试管的能力
*   搜索工作数据字段
*   可定制的用户界面(代码荧光笔，选择列，编辑自动刷新秒，暂停管秒)

## 安装

支持的操作系统的预编译二进制文件可用。

## 截图

服务器列表

[![Server list](img/3e24e2f41180324a9b8e72d143a78722.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VPkSYpp_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xuri.me/wp-content/uploads/2016/11/aurora-cross-platform-beanstalk-queue-server-console-1.jpg)

试管状态

[![Tube status](img/489e8466bf8404acdd8ea36abd93d569.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VuHxCS9p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xuri.me/wp-content/uploads/2016/11/aurora-cross-platform-beanstalk-queue-server-console-2.jpg)

统计概述

[![Statistics overview](img/d25a1fc70b83e56796641cc6dd5059ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a0SIKymr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xuri.me/wp-content/uploads/2016/11/aurora-cross-platform-beanstalk-queue-server-console-3.jpg)