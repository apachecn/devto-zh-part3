# Django 生态系统中的一些内容管理系统。

> 原文：<https://dev.to/simo97/djangos-world-cms-1aec>

在网络开发领域，我们有很多东西需要用来建立网站(django，symfony 等)。但是有时由于缺乏知识，一些 django 开发者更喜欢使用 Wordpress 或 Drupal 来构建网站。但是在 Django 的世界里有很多好的内容管理系统，这里是最明显的一个，我已经使用了其中的一些。

他们中的每一个都提供了真正不同的管理内容的方式，开发者都是他们，我的意思是他们真的做到了。开发不是由插件和特别是主题的行为驱动的。

# Django CMS

也许是第一个，这是为了适应 django it seft 的开发过程，所以也很容易上手，特别是他的 WYSIWYG 特性。

[![Django CMS](img/a354c251a04e14713492cddecd2c1002.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cXxl3K0M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://django-cms-2016-69800ad.aldryn-media.io/filer_public_thumbnails/filer_public/cc/3e/cc3eda7e-3ccb-4448-be9a-4e88347502eb/preview-1.png__1170x0_q90_subsampling-2.png)

### 检查表

*   Github star : 6569
*   发行日期:–2007 年 5 月
*   主要维护者:迪维奥，[https://www.divio.com/](https://www.divio.com/)
*   插件:是的，插件市场
*   内容管理方法:所见即所得，前端拖放
*   管理:是的，django admin 的定制版本易于操作，它像一个普通的 django 应用程序一样工作，具有所见即所得的特性。
*   发布工作流:否

# 玳瑁 CMS

最近有很多人说 Django CMS 做得很好，但是很强大，我个人同意这一点；例如，有可能管理一个 CMS 的页面上下文，只是**魔术**。可以用作无头 CMS。请注意，它是数据驱动的。(像 odoo ERP)

[![Wagtail CMS](img/03b565413e0ce55d5bca9ca413aa330d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pKRIMj-1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.wagtail.img/explorer1_HK2WdeO.width-1000.jpg)

### 检查表

*   Github star : 6772
*   上映日期:2014 年 2 月
*   主要维护者:火炬箱，[https://wagtail.io/](https://wagtail.io/)
*   插件:是的，github 上有一个很棒的插件列表
*   内容管理方法:每个页面被定义为一个 django 模型，page 类的每个实例代表站点中的一个页面。内容在管理中定义。
*   管理:是的，一个全新的从零开始建立，以满足一些要求。
*   出版工作流程:是的，有一个直接嵌入 CMS 的编辑、编辑和出版评论的逻辑。
*   松弛通道:[https://wagtailcms.slack.com](https://wagtailcms.slack.com)
*   因为它提供了完整的 REST API 来访问页面和内容。

# 夹层

没什么好说的，我没怎么用它，但是我知道它和 django admin 是非常集成的。

[![Mezanine](img/c2c7965176808fca255cd7c964ad7c68.png)](https://camo.githubusercontent.com/2ad3a4037109bbf18b2a7ed57beb63da23fb88b4/687474703a2f2f6d657a7a616e696e652e6a75706f2e6f72672f646f63732f5f696d616765732f64617368626f6172642e706e67)

### 支票簿

*   Github star: 3718
*   发布日期:2012 年
*   维护者:斯蒂芬·麦克唐纳(@stephenmcd，github)
*   主题:有一个主题市场[https://mezzathe.me/](https://mezzathe.me/)
*   行政:Django 行政
*   内容管理方法:直接进入 Django admin UI

# Coderedcms(基于 Wagtail)

一个版本的 wagtail 更侧重于搜索引擎优化。

[![Coderedcms CMS](img/03b565413e0ce55d5bca9ca413aa330d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pKRIMj-1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.wagtail.img/explorer1_HK2WdeO.width-1000.jpg)

### 检查表

*   Github star : 69
*   发布日期:不适用
*   维护者:CoderedCorp，[https://www.coderedcorp.com/](https://www.coderedcorp.com/)
*   更加注重营销内容
*   大部分 Wagtail 特征

# 姜戈-维德吉

[![Django-widy](img/d3c186ae5d69e2a76367f625c5a9e1a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pSPDtuwA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wid.gy/static/image/feature-drag-drop.jpg)

### 检查表

*   github star : 310
*   发布日期:不适用
*   维护者:fusionbax，[https://wid.gy/](https://wid.gy/)
*   内容管理方法:django 管理中的所见即所得
*   管理:django 管理
*   主题化:普通 django 模板系统

实际上在 django 的世界里有很多 CMS 解决方案，有些比其他的更成熟，所以一个的使用也取决于开发者的实力。为了这个故事，Wagtail CMS 已经被 Torchbox 用来代替 Drupal。

你可以在这里查看 CMS 相关的 Django 包网格:[https://djangopackages.org/grids/g/cms/](https://djangopackages.org/grids/g/cms/)

感谢你阅读这篇文章，你可以。留下评论，在列表中添加一个 CMS，甚至是一个想法，我们可以找到一个新的独角兽。