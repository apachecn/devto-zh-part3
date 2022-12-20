# 需要在 google 云平台上托管 codeigniter 应用程序的帮助/提示吗

> 原文：<https://dev.to/allanjeremy/need-helptips-on-hosting-a-codeigniter-application-on-google-cloud-platform-3fnd>

如题所示，我需要在 Google 云平台上托管 CodeIgniter 应用程序的帮助和提示。使用 app engine 和 PHP 7。

## 当前痛点

*   无法连接到数据库

*   处理文件上传(我想是到云存储，但是从 Codeigniter 应用程序内部)

*   正在访问资产~当前正在使用`base_url('path/to/asset')`，但这不起作用。应用程序部署成功，但我无法访问我的 CSS/JS

*   静态资产何去何从？

## 文件夹结构

```
 - public
    - assets
     - css
     - js
    - uploads (uploads go here)
     - images
     - thumbnails

  - ci
    - application
    - system 
```

Enter fullscreen mode Exit fullscreen mode

希望这有所帮助。

## 又一个痛点

PHP 中使用的数据库的主机名是什么？