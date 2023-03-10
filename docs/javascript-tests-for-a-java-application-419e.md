# Java 应用程序的 JavaScript 测试

> 原文：<https://dev.to/janux_de/javascript-tests-for-a-java-application-419e>

[K.S.C.H. Workflows](https://ksch-workflows.github.io/) 是为 [Kirpal Sagar 慈善医院](https://kirpal-sagar.org/en/kirpal-charitable-hospital-en/)定制开发的工作流管理系统的原型。这是一个基于 Java 的 web 应用程序，借助于 [Apache Wicket](http://wicket.apache.org/) 在服务器端呈现其 HTML 页面。然而，为了方便用户，浏览器的语言是 JavaScript。这篇博文描述了一名 Java 开发人员试图向项目中添加经过充分测试的 JavaScript 代码。

欢迎建设性的反馈:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 将患者年龄计算添加到登记对话框 #49](https://github.com/ksch-workflows/ksch-workflows/pull/49) 

[![jmewes avatar](img/3a516b4368b403bf9f479d5641eeb4d6.png)](https://github.com/jmewes) **[jmewes](https://github.com/jmewes)** posted on [<time datetime="2019-04-20T16:51:12Z">Apr 20, 2019</time>](https://github.com/ksch-workflows/ksch-workflows/pull/49)[View on GitHub](https://github.com/ksch-workflows/ksch-workflows/pull/49)

# 功能描述

这个项目的第一个重要的 JavaScript 代码将是计算估计的出生日期，前提是在注册时只能提供患者的年龄。此外，输入出生日期后，将自动计算和显示年龄。

[https://www.youtube.com/embed/FHvrTM9u0Aw](https://www.youtube.com/embed/FHvrTM9u0Aw)

# 前端代码结构

Wicket 应用面向组件的策略来创建 HTML 页面。对于每个 HTML 文件，都有一个相应的同名 Java 文件。Spring Boot 确保目录`src/main/resources/static`中的所有内容在运行时可供应用程序使用。

第一个拥有自己的 JavaScript 逻辑的 HTML 组件是目录`ksch/registration`中的 [`PatientFormFields.html`](https://github.com/ksch-workflows/ksch-workflows/pull/49/files#diff-6288739e00cbfed3d18a79bb1d4d0107) 。它的 JavaScript 函数在文件 [`PatientFormFields.js`](https://github.com/ksch-workflows/ksch-workflows/pull/49/files#diff-b3e14899d9fe7e1e9123da7b637602f7) 中定义，该文件位于同一目录中它的旁边。