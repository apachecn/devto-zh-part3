# 关系字段:比萨饼店示例

> 原文：<https://dev.to/eecms/relationship-fields-the-pizza-shop-example-acl>

[关系字段](https://docs.expressionengine.com/latest/fieldtypes/relationships.html)允许您在一个条目和一个或多个其他条目之间创建关系。当您显示一个条目的信息时，您也可以显示与该条目相关的条目的所有信息。

让我们从一个非常简单的例子开始。你的任务是为当地一家小型披萨连锁店建立一个网站。妈妈罗宾有各种美味的披萨:

[![Pizzas](img/9b606d710af6375ff5a1ebbe042840ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QAszFVT6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://u.expressionengine.com/asset/article-images/overview-pizzas.png)

有 3 个位置的妈妈罗宾餐厅:战场大街，南街和山顶街。创建两个通道很简单，一个保存关于比萨饼的信息，另一个保存关于商店的信息。由于这是一个模板写作教程，我们将继续为您布局渠道:

```
Stores
  title       Text Input
  url_title   Text Input
  address     Textarea
  phone       Text Input

Pizzas
  title       Text Input
  url_title   Text Input
  description Textarea
  ingredients Checkbox 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们把事情复杂化一点。虽然比萨饼店共享特色比萨饼菜单，但每个单独的商店经理可以决定在任何特定的一周提供哪些比萨饼。你需要能够展示每家餐厅提供的披萨。同样，对于每一个比萨饼，您需要能够显示哪些商店目前正在销售该比萨饼。

关系字段允许您链接商店和比萨饼，而无需两次输入任何信息。

为此，我们将在商店渠道`specialty_pizzas`中再添加一个字段。设置我们的关系字段，以便只有 pizza 频道中的条目可以相关，并允许多个关系(因为一家餐厅可以有不止一个 Pizza)。

设置好通道和字段结构后，我们可以转向模板标签。

#### 子条目:显示商店及其菜单

重要的事情先来。我们需要写一个页面来列出每个商店和他们必须提供的比萨饼。

[![Stores](img/729b064e7133285f5af81b778bdd9ce8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f_ZxbX1Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://u.expressionengine.com/asset/article-images/stores-pizza.png)

下面是模板标签:

```
{exp:channel:entries channel="stores"}
  <h1>{title}</h1>
  <div class="right">
    Phone: {phone}</p>
    <p>Address: {address}</p>
  </div>
  <h2>Specialty Pizzas</h2>
  {specialty_pizzas}
    <h3>{specialty_pizzas:title}</h3>
    <p>{specialty_pizzas:ingredients}</p>
  {/specialty_pizzas}
{/exp:channel:entries} 
```

Enter fullscreen mode Exit fullscreen mode

我们从标准的`{exp:channel:entries}`标签开始，从商店渠道获取。对于每个商店条目，我们显示商店名称、商店电话号码及其地址。然后，我们会展示哪些特色披萨可供选择:

```
<h2>Specialty Pizzas</h2>
{specialty_pizzas}
  <h3>{specialty_pizzas:title}</h3>
  <p>{specialty_pizzas:ingredients}</p>
{/specialty_pizzas} 
```

Enter fullscreen mode Exit fullscreen mode

`{specialty_pizzas}`标签是一个关系变量标签。因为它是一个可以接受多个条目的关系，所以它是一个循环标记对。因此，包含在对中的这两条线将被循环:

```
<h3>{specialty_pizzas:title}</h3>
<p>{specialty_pizzas:ingredients}</p> 
```

Enter fullscreen mode Exit fullscreen mode

对于附加到当前商店条目的每个比萨饼条目，这些变量将被替换。在这些行中,`{specialty_pizzas:title}`将被当前比萨饼条目的标题所取代，而`{specialty_pizzas:ingredients}`将被逗号分隔的配料列表所取代。

请注意，我们在这里所做的是用关系字段的名称作为 Pizza 频道中变量名称的前缀，该关系字段将商店频道与 Pizza 频道相关联。我们称之为命名空间，这是一个非常强大的工具。这使我们能够访问相关条目的变量，即使它们可能与父条目的变量相同。

在`{specialty_pizzas}`标记对中，您可以使用`{title}`显示当前商店条目的标题，使用`{specialty_pizzas:title}`显示当前比萨饼条目的标题。这意味着我们可以随心所欲地嵌套关系，而不必太担心命名冲突。

#### 父条目:哪些店有哪些披萨？

您可能想要制作的另一个模板是每种比萨饼的页面，在该页面中，您可以给出比萨饼的描述，列出其成分，并显示当前哪些商店有这种比萨饼。

[![Pizzas](img/07237baab22962b93693e5c96102b697.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0p3P3vZj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://u.expressionengine.com/asset/article-images/pizzas-pizza.png)

`{parents}`标签输出当前所有特色比萨饼的餐馆。像这样:

```
{exp:channel:entries channel="pizzas"}
  <h2>{title}</h2>
  <p>{description}</p>
  <p>{ingredients}</p>
  <h3>Where can I find this pizza?</h3>
  {parents field="specialty_pizzas"}
    <strong>{parents:title}</strong>: <br />
    {parents:phone} <br />
    <p>{parents:address}</p>
  {/parents}
{/exp:channel:entries} 
```

Enter fullscreen mode Exit fullscreen mode

在这个模板中，我们列出了披萨频道的变量- `{title}`、`{description}`和`{ingredients}`。然后，我们有一个部分显示这种比萨饼目前在哪些商店有售。为此，我们使用了`{parents}`标签。

`{parents}`标签将通过您指定的字段从`{exp:channel:entries}`标签中提取当前条目作为子条目。如果在多个通道中使用同一个字段组，可能还需要指定通道。在本例中，我们将它传递给`specialty_pizzas`字段。它将通过`specialty_pizzas`字段查找所有附加到任何频道的条目，这些条目将当前的 Pizza 条目作为子条目。在我们的例子中，`specialty_pizzas`仅用于商店渠道，其结果是找到目前有这种比萨饼的所有商店。

`{parents}`标签是一个循环标签对。因此，对于它找到的每个商店，它将循环遍历包含在对中的模板部分:

```
<strong>{parents:title}</strong>: <br />
{parents:phone} <br />
<p>{parents:address}</p> 
```

Enter fullscreen mode Exit fullscreen mode

它将替换该部分的变量，并将其附加到最终输出中。这里，我们再次使用命名空间来访问父存储的变量。我们使用`parents:title`、`parents:phone`和`parents:address`访问它的标题、电话和地址。

## # [eecms](/t/eecms)

## # [表情引擎](/t/expressionengine)