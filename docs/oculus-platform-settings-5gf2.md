# Oculus 平台设置

> 原文：<https://dev.to/yuma1217_ja/oculus-platform-settings-5gf2>

# 摘要

在这篇文章中，我将讲述如何在 Unity 中查看平台设置。
本帖是[我的博客](https://xrjapanesedeveloper.wordpress.com/2018/12/23/oculusplatformsettings/)的转载。

# 设置

## 眼睛仪表板

1 : [前往 Oculus DashBoard](https://dashboard.oculus.com/my-apps/)
2:创建新应用。
可以获取 app id，所以记下来。

[![](img/fdc4c1086fb2b48197d81a35db051bc8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jBFaYTGs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Ypixal0.png)

(↑非常抱歉，所有照片都是日文。应用程序 id 16 位数。)

* * *

从这里开始，如果你在 OculusPlatform 中测试 mutiplay 特性，请阅读。
添加测试用户。

3:在左侧栏中选择管理>用户名>设置>测试用户。

[![](img/d235dbaeb7c8591ac7c48f0de7f17cf7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e9I8JpE2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/GyPREJo.png)

4:添加测试用户。填写密码和 pin 码。发送。你应该把它们写下来。我们稍后使用密码。

[![](img/25053865cc7d8cf202eb69802fe7af7a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c8ssP2df--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/3IbGttR.png)

5:添加了测试用户，请写下 Oculus ID 和邮件地址。

[![](img/c46da4afd14239d2a8ac83144c70b76d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mFePDC5L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/apQfedv.png)

6:(重要)将我们创建的测试用户注册为开发人员。
选择设置>成员，然后选择添加开发者。

[![](img/cd87859d6c2e216d10dbb65804b72e18.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t0Q12XU---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ZMfgjrU.png)

7:键入我们创建的测试用户的 Oculus ID。选择添加。

[![](img/ce446ddd70f2802e3203297c329bae4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A4D7rbTk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/MqS4UlG.png)

现在，测试用户已注册。

[![](img/0d239ce2e5156e309599fef460d8e7f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u5h1NPCq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/0dfHCw5.png)

OculusPlatform 中的设置完成！

* * *

## 单位设置

1.  在 Unity 的 AssetStore 中，键入 Oculus Integration 并导入这个。
2.  在顶部菜单中，选择 OculuPlatform >编辑设置。
    [![](img/b07d06982a1281ab0ded66bd4f81192b.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--ASepo1jW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/KJl4BjS.png)

3.  键入应用程序 id。这是我们不久前得到的 OculusDashBoard 设置中的 16 位数字。
    [![](img/e2711275d1d5357e6759688ca98c857b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N14jzUo_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/M2yVBds.png) 
    如果你在 OculusDashBoard 中创建了 OculusRift 应用，在 OculusRiftAppId 行输入 appID。
    如果您在 OculusDashBoard 中创建了 GearVR/OculusGo 应用，请在 GearVR 应用 Id 行中键入。

4.  接下来，打开 appId 设置下的 UnityEditorSettings 切换。键入测试用户地址和密码，点击登录。
    [![](img/7e64569121c9fa7ef5a7b03ac871b443.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_IsqZtSR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/gsEEe7e.png) 
    如果你不使用测试用户，在 Oculus 中使用你的邮件地址和密码。或者取消选中`Use Stand Platform`复选框。如果您取消选中，Unity find OculusApplication 将使用已登录 Oculus 用户的信息。

* * *

设置完成。

接下来我会写如何使用 OculusPlatformAPI。