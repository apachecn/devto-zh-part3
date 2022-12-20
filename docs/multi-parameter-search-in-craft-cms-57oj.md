# Craft CMS 中的多参数搜索

> 原文：<https://dev.to/epicosity/multi-parameter-search-in-craft-cms-57oj>

**挑战** —律师搜索功能，可通过以下任意或所有方式进行搜索:

*   律师姓名
*   律师业务领域(类别)
*   关键字

[![](img/203ce15f3ace9b8283282eb2576c1ae9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GkI3rCkj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/330/1%2AF5Qd5Ia0a20szbCgXQbn2w.png)

**填充类别下拉列表**

第一步是构建搜索表单。我们只需要将附加字段添加到典型的 Craft CMS 搜索表单中。在本例中，我们在网站上有一个练习区。每个条目都是一个不同的实践领域，我们可以用每个实践领域条目的标题填充一个下拉字段。

[![](img/95cbfba585e1c0634d96d55031c50301.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CqnWMEMF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Avx3nlus2fF8Z4OtYZm8IIQ.png) 

<figcaption>练习区场地由练习区场地段</figcaption>

填充

关键字字段只是默认的搜索字段，而律师姓名字段是附加的基本文本字段。

[![](img/784a52561ff39b3ce56cd67afe25217b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IVbUVkhq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AxxpCJCIihpNTGEcUDChHZg.png) 

<figcaption>关键字和律师姓名搜索字段</figcaption>

**设置搜索参数**

在典型的方式中，用户输入他们的搜索参数并点击搜索按钮。

实际发生的是，Craft CMS 表单用表单中输入的搜索参数构建了一个 URL，它看起来像这样:[http://domain.com/search?section=attorneys&惯例=家庭+法律&q =家庭& name=smith](http://domain.com/search?section=attorneys&practice=Family+Law&q=family&name=smith)

**从 URL 中提取参数**

[![](img/dfd8332c228f0b0db4c2e2e7c029576d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XxUtnEIm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AStCFMPgitk1duZ-f_jq0aQ.png) 

<figcaption>从 URL</figcaption>

获取所有参数

使用方便的 dandy[craft . app . request . getparam](https://docs.craftcms.com/api/v3/craft-web-twig-variables-request.html#method-getparam)公共方法，我们可以从 URL 查询字符串中获取这些搜索项。

**用这些参数建立一个搜索查询**

[![](img/f2bf7b3f660d5d83d17e86639e66422f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fCa72pBT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AGTnq5bA6xI4QT7MqfbocQA.png) 

<figcaption>构建包含所有搜索参数的数组</figcaption>

在将每个参数合并到主 queryParams 变量之前，我们希望检查每个参数是否都已定义。Craft CMS 默认使用“AND”作为多个搜索参数，因此我们的所有参数都将包含在搜索中。

我们还决定在用户屏幕上显示搜索参数。

[![](img/bdce7900e12fe190c8252874c02ab95f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VLCIwE-J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AafbtMc_opzGkyW0sgZS7Aw.png) 

<figcaption>显示用户的搜索参数</figcaption>

然后我们可以运行搜索，使用 Craft CMS 配置来显示结果。在这个具体的例子中，我们还检查了一个附加参数“sectionHandle ”,该参数只有在我们使用律师搜索功能在律师部分中进行搜索时才会被定义。我们可以在进行常规网站搜索时使用相同的搜索模板。在这种情况下，我们不会定义 sectionHandle，而是搜索整个站点。

[![](img/20f1e995fe3c037008cf15031255c46a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Nnn5hBzD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARyNDSG9qvDZim8eUYEL67A.png) 

<figcaption>使用搜索参数数组，运行搜索(带分页)</figcaption>

**最后显示搜索结果**

现在我们可以用我们选择的任何布局显示搜索结果，类似于默认的 [Craft CMS 搜索](https://docs.craftcms.com/v3/searching.html#searching-for-specific-element-attributes)结果。

[![](img/21b4fcec38dcaacf32a71a5f41721577.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XcaRWVg6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AxJ6CRC3rBrcOZr-fjs6OHA.png) 

<figcaption>显示搜索结果</figcaption>

* * *