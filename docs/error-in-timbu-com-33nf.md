# timbu.com 的错误

> 原文：<https://dev.to/rapulu/error-in-timbu-com-33nf>

**简介**

我正在参加一个尼日利亚远程实习项目(附注:我是尼日利亚人)，还记得之前的帖子吗？

[![rapulu image](img/b493577028b53688c230cf42e4ff77fb.png)](/rapulu) [## 构建我的开发者组合现实博客。

### rapulu Apr 2 ' 191 分钟读取

#beginners #career #codenewbie #personalnews](/rapulu/building-my-developer-portfolio-reality-blog-1dof)

其中一项任务是在 [Timbu](https://timbu.com/) 网站上找到一个 bug，然后写博客，以便有资格进入下一阶段，现在开始！

**发现错误**
所列货币中的一种格式不正确，点击后不会变为正确的货币符号，因为锚点标签中遗漏了一个数据值属性。

下面是错误截图:

[![error image](img/744739a980dba39fbe77ae62862b07d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g7g0e1ms--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ke2zinnf4c3ebgfxic5m.png)

下面是 chrome inspect 的 bug 截图:

[![bug caused by](img/8548839cf28adebd4b41c691aa29654b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Px1K5eEc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tsrprdglasxc2vq5kdc1.png)

**解决方案**

解决方案是添加锚标记中缺少的数据值属性及其相应的货币缩写值。