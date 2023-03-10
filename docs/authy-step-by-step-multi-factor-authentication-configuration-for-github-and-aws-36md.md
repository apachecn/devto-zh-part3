# authy:Github 和 AWS 的逐步多因素认证配置

> 原文：<https://dev.to/setevoy/authy-step-by-step-multi-factor-authentication-configuration-for-github-and-aws-36md>

### T2】

我敢肯定，今天使用 MFA(多因素认证)是被遗忘的。

对于 2FA(双因素认证)来说，最常用的方法是[TOTP](https://en.wikipedia.org/wiki/Time-based_One-time_Password_algorithm)–*基于时间的一次性密码*，除此之外，普通登录:密码还需要输入由设备或软件生成的代码。

最著名的实现是 Google Authenticator，但也有很多其他解决方案。

在过去的几天里，我一直在为我们的项目寻找这样一个解决方案，并且找到了一个真正有用的东西。

### Authy vs 谷歌认证器

用几句话概括 Authy 的好处:

1.  它不依赖于具体的设备——可以安装在多部手机上(但仍然会使用原来的电话号码)
2.  拥有适用于 Windows、macOS 和浏览器的桌面应用程序扩展，使访问代码变得更加简单——您将不再依赖手机设备，即使您将手机放在厨房里也能登录
3.  创建帐户备份-当在新设备上安装 Authy 时，它将恢复您的所有记录

在此查看更多[>>>](https://authy.com/blog/authy-vs-google-authenticator/)。

### 安装权限

从 Apple Store 上的 Google Play 安装到手机上:

[![](img/c7fac172bce6951afd9d2300c7f16188.png "Authy: настройка Multi-Factor Authentication для Github и AWS")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416-171916.png)

设置您的手机号码–它还将用于在其他设备和您的电子邮件上进行授权。

使用短信或电话授权。最好使用短信，因为通话将通过 Viber 进行，质量可能很差:

[![](img/f3dd6e3f04fb4be365bb99a6bab65c1a.png "Authy: настройка Multi-Factor Authentication для Github и AWS")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416-172102-1.png)

这也是用于 Arch Linux 的 AUR 包，但是我没有使用它，也不确定它是否有意义，因为它有一个不错的 Chrom 'es 扩展。

通过 Chrome Apps 和扩展为 Chrome 安装一个[应用程序](https://chrome.google.com/webstore/detail/authy-chrome-extension/fhgenkpocbhhddlgkjnfghpjanffonno)，以便快速访问。

没有扩展的应用程序可以通过 [chrome://apps](https://dev.tochrome://apps/) 启动。

对于 Windows 和 macOS 应用程序，可以从[下载页面](https://authy.com/download/)下载。

使用短信再次授权:

[![](img/1ea1652c3daa2a6e8f0329b5c6a233c2.png "Authy: настройка Multi-Factor Authentication для Github и AWS")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416_172339-1.png)

### 配置 Github MFA

文档[此处> > >](https://help.github.com/en/articles/configuring-two-factor-authentication#configuring-two-factor-authentication-using-a-totp-mobile-app) 。

进入*设置>安全*，点击*启用双因素认证*:

[![](img/f10cb40abfe6539b1641e01783a77c54.png "Authy: настройка Multi-Factor Authentication для Github и AWS")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416_180938.png)

点击*使用 app* 设置:

[![](img/7f41ac13d2829286a18a7cdcce3c0546.png "Authy: настройка Multi-Factor Authentication для Github и AWS")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416_181058.png)

在下一页，您将获得恢复访问的代码，以防丢失 MFA 设备。

**救救他们！**

[![](img/5d68e5fa709fba6125a0e129984ba676.png "Authy: настройка Multi-Factor Authentication для Github и AWS")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416_181114.png)

在手机上打开 Authy，点击*添加账号*并设置[备份](https://authy.com/features/backup/)密码:

[![](img/dd64f7e0f62746e58d0ebdb73b3afa1e.png "Authy: настройка Multi-Factor Authentication для Github и AWS")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416-181322.png)

相同的密码将用于从其他授权应用程序访问您的数据，因此请存储并记住它。

Github 将显示 QR-код:

[![](img/5efccc824f37a5af4944f3b340c231af.png)](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416_181223-1.png)

从您的手机扫描它:

[![](img/2ce1ea22d3dab89fb77083ddd5e51405.png "Authy: настройка Multi-Factor Authentication для Github и AWS")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416-181510.png)

选择徽标并保存新帐户:

[![](img/1a7cf7ce6d5fb7a70dcfa7b5998715f3.png "Сканируем QR-код:")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416-181805.png)

创建新帐户后，Authy 将开始显示生成的代码。

在 Github 页面的底部输入它:

[![](img/e627d57a93f776acaac9aa51c026ef18.png "Authy: настройка Multi-Factor Authentication для Github и AWS")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416_182711.png)

完成:

[![](img/788407fbe2f21fb110df71f890ce92a9.png "Authy: настройка Multi-Factor Authentication для Github и AWS")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416_182821.png)

在此页面上，您还可以配置 SMS 以恢复访问–**执行此操作**:

[![](img/e08bc2cf98c4b45787667f083b8d1a4b.png "Authy: настройка Multi-Factor Authentication для Github и AWS")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416_182845.png)

### 配置 AWS MFA

对 AWS 重复上述步骤。

文档资料在[这里有> > >](https://authy.com/guides/amazon/) 但是有点过时了。

转到*安全凭证*:

[![](img/e40b866628fea22bb86077077d497665.png "Authy: настройка Multi-Factor Authentication для Github и AWS")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416_185336.png)

点击*激活 MFA* :

[![](img/ac24c9386eb4fcd3de8a38ec928cd28f.png "Authy: настройка Multi-Factor Authentication для Github и AWS")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416_185406.png)

默认情况下,*虚拟 MFA 设备*将被选中——将其保留并按下*继续*:

[![](img/248de27198e31b5781ce95d219436bb9.png "Authy: настройка Multi-Factor Authentication для Github и AWS")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416_185511.png)

点击*显示二维码*:

[![](img/329df24e6a08812ea4febbeb6fed2635.png "Authy: настройка Multi-Factor Authentication для Github и AWS")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416_185531.png)

扫描它，在 Authy 中创建帐户，并在底部输入来自您的 Authy 应用程序的代码，然后等待它更新为新的代码，并再次输入:

[![](img/d7e114b3c5a79cee24a4c2151f2b0cdf.png "Authy: настройка Multi-Factor Authentication для Github и AWS")](https://rtfm.co.ua/wp-content/uploads/2019/04/Screenshot_20190416_185744.png)

完成了。

### 类似的帖子

*   <small>04/16/2019</small> [Jenkins:一份检查 Github 组织公共存储库列表的工作](https://dev.to/setevoy/jenkins-a-job-to-check-a-github-organization-s-public-repositories-list-129p) <small>(0)</small>
*   <small>04/03/2019</small>[MySQL/Maria db:像 Petya 勒索病毒对于 MySQL 和' root'@'%' access](https://dev.to/setevoy/mysql-mariadb-like-petya-ransomware-for-mysql-and-root-access-11hk) <small>(0)</small>
*   <small>03/28/2017</small>[Arch:Git–github.com errno =连接被拒绝](https://rtfm.co.ua/arch-git-github-com-errnoconnection-refused/) <small>(0)</small>