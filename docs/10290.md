# 自动发布 Flutter 应用程序

> 原文：<https://dev.to/rodydavis/automate-flutter-app-releases-4lk6>

**TLDR** 这里可以找到剧本[。](https://gist.github.com/rodydavis/774b36e32d7efa882cca8dd16da6e74c)

让您的生活更轻松，并自动构建测试版和生产版！

## 你需要什么 [#](#what-you-need)

*   [浪子](https://fastlane.tools/)设置在各个目录下
*   [build_runner](https://pub.dartlang.org/packages/build_runner) 作为依赖项
*   Git 项目

## 初始设置 [#](#initial-setup)

*   下载[这个文件](https://gist.github.com/rodydavis/774b36e32d7efa882cca8dd16da6e74c)
*   把它放在你的 flutter 项目的根层
*   打开终端并导航到您的项目位置
*   输入这个命令:chmod +x [release.sh](http://release.sh/)

## 用法 [#](#usage)

现在可以调用这个脚本了！

*   对于测试版:`./release.sh beta`
*   用于生产:`./release.sh release`

## 概述 [#](#overview)

*   如果您使用的是 pubspec.yaml 中的版本，请删除版本号
*   发布浪子的应用程序
*   格式化所有 Dart 文件
*   清洁项目
*   重建类
*   添加提交消息
*   更新可可豆荚![](img/e72ef3701b40f95e812eb95760ddfb55.png)