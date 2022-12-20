# Firebase 项目托管

> 原文：<https://dev.to/codingcatdev/firebase-project-hosting-342c>

> 原帖:[https://ajonp.com/lessons/firebase-project-hosting/](https://ajonp.com/lessons/firebase-project-hosting/)

[https://www.youtube.com/embed/sOn3YMdpYR4](https://www.youtube.com/embed/sOn3YMdpYR4)

## Firebase 控制台

转到 [Firebase 控制台](https://console.firebase.google.com)

如果这是你的第一个 firebase 项目，你需要登录谷歌

[![Google Login](img/492de34ec441d4a72fbdbbb205d5b151.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FzuskmG0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1543272442/ajonp-ajonp-com/1-lesson/Screen_Shot_2018-11-26_at_5.44.44_PM.jpg)

## 创建项目

现在，您可以单击按钮添加一个新项目

[![Create Project](img/7b6ec497ed4a7f53fccc2652994a2406.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QaRMzWip--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1543272442/ajonp-ajonp-com/1-lesson/Screen_Shot_2018-11-26_at_5.46.16_PM.jpg)

## 托管

在这一点上，许多博客将开始谈论建立数据库，但我想保持这种真正简单的“你好，世界”的风格。

现在，您可以导航到 firebase 中的托管区域。

[![Firebase Hosting](img/2f18a359da0dbe611d4b0074cde4d8cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YzDwsvBY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1543452867/ajonp-ajonp-com/1-lesson/Screen_Shot_2018-11-28_at_7.49.10_PM.jpg)

如果你看一下[Firebase hosting quick start](https://firebase.google.com/docs/hosting/)，它也有一个很好的指南。

如果你们都是编程新手，可能还没有安装 [Node 或 NPM](https://nodejs.org/en/) ，只要点击这个链接，安装 LTS 版本。
T3![NodeJs with NPM](img/114b75f07ed126e58cfc167b0a0ddb6d.png)T5】

## 托管部署

现在，您可以选择“开始”来浏览 Firebase 托管的指导提示。
[![Hosting Get Started](img/a68b9bbfe3b50ee725f5cb2b75625a80.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--HWNk_g3F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1543691320/ajonp-ajonp-com/1-lesson/Screen_Shot_2018-12-01_at_2.08.17_PM.jpg)

### 安装 Firebase 工具

```
npm install -g firebase-tools 
```

Enter fullscreen mode Exit fullscreen mode

### 用 firebase 登录

```
firebase login 
```

Enter fullscreen mode Exit fullscreen mode

### 新建一个目录

```
mkdir ~/Downloads/lesson-1-firebase-project
cd ~/Downloads/lesson-1-firebase-project 
```

Enter fullscreen mode Exit fullscreen mode

### 初始化 firebase

```
firebase init 
```

Enter fullscreen mode Exit fullscreen mode

#### 选择

1.  向上/向下键移动到主机
2.  按空格键进行选择
3.  回车继续下一个设置
4.  导航到您新创建的 firebase 项目
5.  按 enter 键接受 public 作为提供文件的位置
6.  键 n，输入不使这成为一个单页应用程序。
7.  Firebase 初始化完成！

 <video controls="" src="https://res.cloudinary.com/ajonp/video/upload/f_auto,fl_lossy,q_auto/v1543783288/ajonp-ajonp-com/1-lesson/lesson-1-firebase-init.mov" title="Firebase Initialize">#### Firebase 项目文件

.firebaserc

```
{  "projects":  {  "default":  "ajonp-lesson-1"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

#### Firebase 托管文件

firebase.json

```
{  "hosting":  {  "public":  "public",  "ignore":  [  "firebase.json",  "**/.*",  "**/node_modules/**"  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 部署火力基地

因为您登录到了 CLI，所以上面的两个文件告诉 firebase 要使用的项目，以及它们将如何在 nginx 上提供服务。现在您只需要运行部署命令。

```
firebase deploy 
```

Enter fullscreen mode Exit fullscreen mode <video src="https://res.cloudinary.com/ajonp/video/upload/v1543784633/ajonp-ajonp-com/1-lesson/firebase-deploy.mov" title="Firebase Deploy">现在回到 firebase 控制台，您应该看到托管已经完成。
[![Firebase Hosting Files](img/b25d80604d445441c30833300a577f65.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--2Yj28gBi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1543784444/ajonp-ajonp-com/1-lesson/hosting_after_deploy.jpg)</video></video>