# iOS 版 XCode 简介

> 原文：<https://dev.to/robertchen/introduction-to-xcode-for-ios-3dki>

### 循序渐进教程

<figure>[![](img/c9197f9db07eec7ae35d02c5f76f8b8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JqyhJfMj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ANWfbAtNEvu_8UEH3) 

<figcaption>照片由[尤拉鲜](https://unsplash.com/photos/n31x0hhnzOs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上 [Unsplash](https://unsplash.com/search/photos/ios?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

一直想做个 app？不知道怎么做？我也是，直到我上了一些网络课程，发现这并不是很难(建立一个简单的应用程序)。您也可以按照这个指南来熟悉 XCode。

[![](img/309e4790a2a560e6330e8dd376113469.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3rU6fl4D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/372/0%2AI2pq9MZHbb9T51Xb)

我目前是熨斗学校的学生，参加他们的沉浸式 Web 开发项目，并在周日通过 Udemy 学习 Swift 和 App Brewery，所以我非常清楚学习如何开发可能是令人生畏的。在这个帖子里，不会有任何编码。我们将重点让您设置并熟悉众多的 XCode 面板。

### **让我们把你安顿好！**

1.  在这里 [安装最新版本的 XCode](https://itunes.apple.com/us/app/xcode/id497799835?mt=12.) [。](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)
2.  要使用最新版本的 XCode，您至少需要 macOS 的第二到最新版本。如有必要，在此更新您的 Mac [。](https://www.apple.com/macos/how-to-upgrade/)

### **避免混淆**

下面的步骤是用运行在 macOS Mojave 上的 XCode 10 演示的。根据你的未来有多远，界面可能看起来完全一样，也可能不一样。

[![](img/fff0fcfd2c7e774bf7697d8dea9593d0.png)](https://i.giphy.com/media/RrVzUOXldFe8M/giphy.gif)

### **规则**

*   绿色圆圈表示点击。

*   两个绿色圆圈表示双击。

*   绿色箭头表示单击并拖动。

*   任何用黄色圈起来的东西都意味着看一看。

*   描述文本总是指上面的图像。

### **创建新项目**

[![](img/382b635be863b3604c5079803f158587.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZZwiG8Em--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A1MAu9_jKVnTaHvcE)

[![](img/b3a7684e99aaeac94074fa794e82737e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Nh0NJ7IE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/731/0%2Adaz2umoRDynag73F)

[![](img/c25a5a99b11ec1546cacc6011a988d79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--brabEzhy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/733/0%2Afqpl0oljAfOH3PQI)

对于“组织名称”,您只需填写您的姓名。

对于“组织标识符”,苹果想要一个反向域名标识符。那就是拥有“com”在前面，而不是像我们习惯看到的那样在最后。

你想选择 Swift 是因为 C++在 iOS 开发中逐渐被淘汰。

我们没有为此应用程序做任何测试，因此取消选中所有复选框。

[![](img/05f9a9ac1f01ec21845cf57b8a3c4219.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PzYnSGhx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/799/0%2A1VLhYR7x5ANUuywz)

### **停顿。让我们来看看一些东西。**

[![](img/3dd3b01f3a04d01a411995d4d4b5be50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kE1dmtML--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ATbSLBpwV_xSS37-Z)

以'结尾的文件。“swift”是你的代码文件。

以'结尾的文件。“故事板”是你的设计文件。

“Assets.xcassets”文件夹包含您的所有媒体文件。

[![](img/a62f397c89d3dde24c549dc57104a17e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XHXjDb32--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A0IxcnY_kYGHC2p8f)

[![](img/88961e9b96e20bb9d209b67d66f472b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dgKWTXxG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Aku57AP5IsubIKj6t)

[![](img/c1411a64f930492dac214479e8cf2bca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0VLvFiK7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AMgZSdMk-iUvZnyCJ)

您可以更改预览中显示的设备，这将直接影响您构建的应用程序的尺寸。我把它从 iPhone 8 换成了 iPhone XR，但你可以选择任何设备，并且仍然能够很好地跟随。

[![](img/302d20263d8e889ee16dafa7b5ed26b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2j3n1Nwt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AVojJsTuvIElR-QCN)

让我们关闭视图面板，因为您想要给您自己尽可能多的工作空间。

### **我们现在就开始设计你的应用！**

[![](img/caaf3be3da8f5d7b2ba9a70fa84236ef.png)](https://i.giphy.com/media/QtSBfTT5TI3Go/giphy.gif)

[![](img/67de4ec0b96235d33cfc8dc5e06b86f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_1qw0b8U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AOAfqm2YHq0irv4ok)

[![](img/5e5d687b349689f9d4b62c08b6f62e52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bRiiQSef--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AcXvccaZZFvkTGPKz)

[![](img/23ec5b79d524bf9be9d784a0aaa5e198.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JJHJjMvg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A2VADSWxWx5Y0hEb0)

[![](img/89620af47ae71942aece46580a950374.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tr_eO-N4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A4ul8lriR8AgJsnaM)

[![](img/9fe98fbff6ec6eece9c994d24ffb213c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S75fivAb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AX4unHevz9VXtIDUT)

[![](img/868c84283cd74357e4186ee3a148dd09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jkYhn6NQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ArPdlWdxFcoz4Po8M)

[![](img/8a805d88627c9abd504d80ef668a8559.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NNfC_Xxs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AObO_G6cKCjsD0b61)

选择最吸引你的字体。

[![](img/9b177af7fd5d41e86df0a949778d8ba7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sEE9VmNx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A722-ly-Mfdkbnlla)

[![](img/021c79363dd746ec1645034d43b2991a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wMzzjaKa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AzU0INKBgSSy37uSs)

如果看不到整个设备，请缩小一点。

重新定位你的标签。

重新打开视图面板。

[![](img/abc7aae039e089a15ed93861b352528f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y898WyTi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AvbqZlBE2HNl5GLQC)

[![](img/9ee0fa8618a6be535fab42d11df30cfe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PsSf9fdp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Ah4K9eZBxa3RkzJsw)

选择你的背景颜色。我使用了 Apple 预先制作的颜色选择的彩色铅笔选项，但你可以通过任何方法选择任何颜色。花一分钟四处点击，玩玩颜色面板。

[![](img/b3956976ab17acaecb596cca720a866b.png)](https://i.giphy.com/media/9vJJadIH58iqY/giphy.gif)

### **我们来添加一张图片**

[![](img/f094452f9f5e00fac0c0cb7fceccbf4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kBOD-TtY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A8ZcepcWJtX3Ykx1c)

[![](img/85ec7dbe077901f0ec33fe4c14ea8ee7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mPuJYg-F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AH85mrH1MSPIDCGQU)

不同的设备需要不同尺寸的同一张图片，但是有一个叫做[appicon.co](https://appicon.co/)的网站可以帮你调整图片尺寸。

[![](img/ca2ff1b72e213fe160b345f1574fca37.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GiZkiJLV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AJJnlaoqmBhyA3gxj)

[![](img/ec484f1764b7af826e2c7aa0286723f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1yOVPn9t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AR5-5oM27HboUtxeT)

[![](img/d596975fc92636e9e4a5d423cc700b61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vk-qlo78--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ADIWuEbeGXaf45hRV)

[![](img/94de1bff3647395f7022784df581a61f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X7_Cfghy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A8C-crmMEiCxjtB4e)

[![](img/7bdd3ac12a95d82c762e73ac4370eba7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GvpVO6Zh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AhwlHUfvOa2THvebU)

[![](img/3d896bb092e3d8773c8ece1be4ea885e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4wdYoXy1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Ak8ZUDBheToN8HuP8)

[![](img/71ca7c05b0ea4154c1aa8eae6823b172.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--97DvnWq9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AijUIOG0qc1kx5S4I)

[![](img/55be1a7dc6d9439aff48e606f357ea5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N7RfPO6n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A6zBBC9WtwVa_1mjv)

键入图像名称以定位您的图像。

在“视图”面板中，将图像拖到标签上方，使其位于标签后面。

在预览屏幕中，将图像拖到标签所在的位置。

[![](img/aa149bf762838d5a6c3ab5b00d99aae3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eSDyoUBA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AYHY6LaEExmD2nNMg)

现在我们在你的文本后面有一个黑板，让我们把文本颜色改为白色。

[![](img/c65c911f749b718ad87923084af9f1dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9TARkGpo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AUltHQQu4sLduHEoi)

有时你的图像会被默认扭曲(不是在这种情况下)，你可以通过“内容模式”属性改变你的图像的行为。

[![](img/7b67b2c61cb862648da1d0832075271f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1wrcDcfE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A-SybAXn7ea8S8QjX)

当您需要图像或标签的非常具体的位置(由客户或设计团队提供)时，您可以使用标尺面板输入所需的位置。

### **时间到了。**

我知道你想从 0 到 100，但这是这篇文章的主题。花点时间熟悉一下我们讨论过的所有面板。有很多面板需要查看和记忆，但是…

[![](img/09cc9b458c3cd1f260bf26b74af2b877.png)](https://i.giphy.com/media/IqFH0yMll9wl2/giphy.gif)

恭喜你迈出了成为 iOS 开发者的第一步！