# 如何使用实时预览

> 原文：<https://dev.to/eecms/how-to-use-live-preview-3jmh>

从 ExpressionEngine 4.1 开始，您可以为内容编辑者、客户和您自己提供一种在适当的前端模板环境中实时预览内容的方法。

设置起来非常简单。要开始，您需要以下内容:

*   频道(新的或现有的)。
*   显示上述频道内容的模板。
*   一些供预览的内容。

## 第一步

确保您有一个在前端显示您的频道内容的模板。一个基本的模板应该是这样的:

```
{exp:channel:entries channel='blog' limit='1' require_entry='yes'}
    <h1>How to use <i>Live Preview in ExpressionEngine</i></h1>
    <p>September 2018, by {author}</p>
    {blog_content}
{/exp:channel:entries} 
```

Enter fullscreen mode Exit fullscreen mode

该模板不需要任何特殊的东西来用作实时预览模板，它只需要能够显示频道的数据。

## 第二步

设置您的频道以使用实时预览。在频道管理器中，单击要编辑的频道名称，然后单击设置选项卡，在“URL 和路径设置”下找到“预览 URL”字段。输入要用于实时预览的模板的路径或路线。

[![](img/966c230739b05f57369873988e22f69b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Afri0eNh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://u.expressionengine.com/asset/article-images/preview-url.png)

## 第三步

在我们刚刚设置的频道中创建或编辑一个条目。在发布表单的顶部和底部，您会看到一个名为“预览”的新蓝色按钮。点击它！

现在，您将在实时预览中找到自己。您对发布表单所做的任何编辑都将实时反映在右侧的实时预览窗格中，如果是从移动设备预览，则显示在表单上方。

[![](img/841ea58f49bef8d4adbd90c9d662a1b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zIb0oUr_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://u.expressionengine.com/asset/article-images/live-preview.png)

不再猜测或切换标签/上下文。只是编辑和预览。

## 第四步

节省时间，取悦您的客户！

-

最初发表于 u.expressionengine.com[的](https://u.expressionengine.com/article/how-to-use-live-preview)[詹姆斯·马蒂亚斯](https://u.expressionengine.com/author/james-mathias)