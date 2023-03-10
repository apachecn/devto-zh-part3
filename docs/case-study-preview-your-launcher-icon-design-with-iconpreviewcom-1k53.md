# 案例研究:使用 icon.limhenry.xyz 预览您的启动器图标设计

> 原文：<https://dev.to/henrylim96/case-study-preview-your-launcher-icon-design-with-iconpreviewcom-1k53>

[![](img/94bfd24e6ae1d6568d65b8b16d0be6ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n5u7DrZ6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2ALqAbo6NHpkFC8mrS.jpg)

### *“启动器图标预览器允许你在移动设备上预览你的启动器图标设计。”*

[启动器图标预览器](https://icon.limhenry.xyz/)是我和[泰勒·凌](https://medium.com/@taylorling)共同创建的一个项目，泰勒·凌是神话般的应用程序的联合创始人，他也是谷歌产品设计专家。

你可以在这里了解更多关于启动器图标预览器:**[Icon . lim Henry . XYZ](https://icon.limhenry.xyz/)**

* * *

*本文最初发表于[medium.com](https://medium.com/@limhenry/case-study-launcher-icon-previewer-b5a1fc959db2)。*

* * *

### 技术

我们决定使用所有最伟大和最新的技术来开发启动器图标预览器，其中包括:

*   [Firebase 实时数据库](https://firebase.google.com/docs/database/)
*   [Firebase 托管](https://firebase.google.com/docs/hosting/)
*   【Firebase 云存储
*   [火情云函数](https://firebase.google.com/docs/functions/)
*   [添加到主屏幕](https://developers.google.com/web/fundamentals/app-install-banners/)、 [Web App 清单](https://developers.google.com/web/fundamentals/web-app-manifest/)和[服务人员](https://developers.google.com/web/fundamentals/primers/service-workers/)
*   [快车](https://expressjs.com/)和 [EJS](http://ejs.co/)

* * *

#### 首页&上传页面

<figure>

[![](img/499386d030b9011c135b71ce8fb60423.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xk7pXTGT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1400/1%2ANpqNEGV84BluNC109RtAMQ.jpeg)

<figcaption>"Home & Upload Page" (Select Icon page, Preview Icon page, and Uploading page)</figcaption>

</figure>

“主页&上传页面”由三部分组成:*选择图标*、*预览图标*和*上传*。

在“预览图标*”页面上，一旦用户点击蓝色的“上传图标”按钮，图标就会上传到云存储中。图标上传后，它将返回图标的下载 URL。

之后，我们会将图标的 URL，以及其他信息，如图标的尺寸，图标的文件类型，应用程序名称，时间戳存储到 Firebase 实时数据库。