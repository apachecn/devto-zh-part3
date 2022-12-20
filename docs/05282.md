# NPM 包装创建指南

> 原文：<https://dev.to/agmm/npm-package-creation-guide-5fa9>

*给自己的提示。*

### 如何发布

1.  创建一个 NPM 帐户
2.  创建 GitHub repo
3.  用`git clone <url>`克隆回购
4.  回购运行内部:`npm init`
5.  填写必填字段
6.  推送至 Github: `git push origin master`
7.  公开发布:`npm publish`

### 基本命令

*   发布包的新版本:`npm version 1.0.0 && npm publish`
*   要查看当前配置文件:`npm profile get`
*   要更改属性:`npm profile set <property> <value>`
*   获取当前本地版本:`npm version`
*   要更新特定的软件包:`npm install package@latest`
*   如果版本变化较小，则进行更新:`npm update package`

### 将特定目录列入白名单

这有助于通过不在 repo 中包含不必要的文件来最小化包的大小。

```
 {
      "name": "package-name",
      "main": "./lib/index.js",
      "files": [
        "/allowed-directory"
      ]
    } 
```