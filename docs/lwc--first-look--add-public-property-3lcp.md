# Look 第一眼-添加公共财产

> 原文：<https://dev.to/brettmn/lwc--first-look--add-public-property-3lcp>

[https://www.youtube.com/embed/2zdJTWdO9HI](https://www.youtube.com/embed/2zdJTWdO9HI)

你好，我是布雷特和 WIPDeveloper.com。我们有了第一个组件。我们还使用 Salesforce Lightning 设计系统和自定义 CSS 添加了样式。

现在我们不能真正重用我们的组件，因为它只说“WIPDeveloper.com 太棒了”。虽然我可能认为这是一个真实的陈述，但是你可能不希望它在你的页面上看起来是这样的。

## 创建属性

所以我们要给组件添加一个属性。因此，我们可以通过 Salesforce 中的社区构建器分配一个值。

为此，我们必须进入组件和属性。所以我们也可以设置一个默认值。

这样万一我们不设置它，它就会突出来。现在，从理论上来说，我们应该可以用这个，作为我们的财产。

我们现在不应该使用我们的财产和展示。让我们拯救它，试一试。

在我们这样做之前，我们可能应该让它有点不同。所以我们知道它来自标签属性。我们走吧。现在，让我们刷新页面。如果我们看到一个感叹号。我们走吧。我们有三个感叹号。现在，如果我们进入编辑页面并选择我们的组件。

#### 更新`firstComponent.js`

```
import { LightningElement, api } from 'lwc';

export default class FirstComponent extends LightningElement {
  @api label = 'WIPDeveloper.com !!!';
} 
```

## 暴露财产

我们没有在这里设置标签属性的选项。为了做到这一点，我们必须进入第一个组件元数据 XML 文件。

之前，我们将元数据设置为 exposed true。因此我们可以在应用程序构建器中看到它。我们给了它目标页面应用页面，记录页面和主页。现在我们需要做的是和`targetconfigs`来公开我们刚刚创建的标签属性。

所以有了`targetconfigs`，这允许我们为上面指定的目标暴露事物。所以我们将为所有三种类型配置一个`targetconfig`。

现在，这将需要一个目标的属性。

现在我可以把这些都复制粘贴到下面，这是一个逗号分隔的列表。或者，我可以从我的另一个窗口复制并粘贴它，这就是我要做的。但是你可以看到我有 lightning 记录页面，我有 lightning 应用程序页面和 lightning 主页。

现在，我想添加一个属性，它将指定我们正在公开该属性

`label`。

这必须匹配我们在 JavaScript 组件中指定的名称，我们必须指定类型，它是一个`String`，然后我们可以提供一个默认值，这样我们就知道它总是被设置的。

现在边注，我有点讨厌，但它不自动格式。所以，我想尽快得到的是 XML 工具，是的，XML 工具想安装，只需一种格式，看起来很好。

好了，一切都变好了。我将在下面的博文中提供 XML 工具的链接。现在我们已经公开了 label 的属性，我们应该能够在 Lightning 应用程序构建器中看到这一点。一旦我们到了 scratch org。

所以它说我们得到了一个错误，标签属性在组件上不存在。那是因为我忘了使用`@api`装饰器。因此，从内部传递语句，我们必须添加一个我们必须有一个 API 的导入。

在这里，我们实际上会使用 at ( `@`)符号。

#### 更新`firstComponent.js-meta.xml`

```
<?xml version="1.0" encoding="UTF-8"?>
<LightningComponentBundle  fqn="firstComponent">
  <apiVersion>45.0</apiVersion>
  <isExposed>true</isExposed>
  <targets>
    <target>lightning__AppPage</target>
    <target>lightning__RecordPage</target>
    <target>lightning__HomePage</target>
  </targets>
  <targetConfigs>
    <targetConfig targets="lightning __RecordPage,lightning__ AppPage,lightning__HomePage">
      <property name="label" type="String" default="WIPDeveloper.com"></property>
    </targetConfig>
  </targetConfigs>
</LightningComponentBundle> 
```

现在我们已经用 API 正确地装饰了我们的属性。让我们按下这个按钮，然后我们可以回到我们的 Lightning 应用程序生成器。

我们来刷新一下。

这里有一个`label`所以我们可以给它一个名字

这一次，让我们给它一个稍微不同的标签，这样我们可以确保它使用我们在应用程序构建器中分配的标签。

你看，WIPDeveloper.com 感叹感叹感叹，一个感叹。所以我们开始吧。我们已经向 lightning 应用程序构建器公开了自定义 lightning web 组件的一个属性，以便我们可以对其进行配置。

## 链接

*   [XML 工具](https://marketplace.visualstudio.com/items?itemName=DotJoshJohnson.xml)

## 现在就这样。

记得报名参加 **[每周一次的单口相声！你可以得到我们关于 WIPDeveloper.com 的任何最新信息。](https://wipdeveloper.com/newsletter/)**

帖子[WIPDeveloper.com最先出现的是【LWC——第一眼——增加公物](https://wipdeveloper.com/lwc-first-look-add-public-property/)。