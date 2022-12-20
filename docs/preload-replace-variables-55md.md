# 预加载替换变量

> 原文：<https://dev.to/eecms/preload-replace-variables-55md>

你知道 ExpressionEngine 的[预加载替换](https://docs.expressionengine.com/latest/templates/globals/preload-replacement.html#creating-and-using-an-preload-replacements)标签支持前缀吗？预加载替换变量非常简单。您设置了一个新变量，并赋予它一个文本值，然后您可以在模板中访问该值。例如，如果创建一个新的预加载替换变量`{preload_replace:foobar="bazz"}`，变量名`{foobar}`没有上下文或名称空间。所以乍一看，变量的来源可能并不明显。您可以为所有的 preload_replace 变量添加前缀，例如`{preload_replace:pre:foobar="bazz"}`，当稍后在模板中访问它时，语法将是`{pre:foobar}`。

使用预加载替换变量非常简单，但是在某些情况下顺序很重要。例如，根据`segment_2` `{pre:variable}`的值，下面的工作将打印`bar`或`bazz`。

```
{if segment_2 == "foo"}
    {preload_replace:pre:variable="bar"}
{if:else}
    {preload_replace:pre:variable="bazz"}
{/if} 
```

Enter fullscreen mode Exit fullscreen mode

您也可以设置没有`{if:else}` :
的默认值

```
{if segment_2 == "foo"}
    {preload_replace:pre:variable="bar"}
{/if}

{preload_replace:pre:variable="bazz"} 
```

Enter fullscreen mode Exit fullscreen mode

然而，你不能在条件之前设置默认值*。如果`segment_2`等于`foo`，`{pre:variable}`仍会打印`bazz`。* 

```
{preload_replace:pre:variable="bazz"}

{if segment_2 == "foo"}
    {preload_replace:pre:variable="bar"}
{/if} 
```

Enter fullscreen mode Exit fullscreen mode

顺序很重要，因为 ExpressionEngine 的模板解析器只解析预加载替换变量的第*次*出现。如果上述条件评估为真，则相同预载替换变量的第二次出现将被忽略。在大多数编程语言中，首先用默认值定义变量，然后根据条件的求值方式覆盖它。

以下是使用预加载替换的实际示例。在这种情况下，`{segment:child_category}`变量是用一个定制的扩展创建的，它可以包含一个 ID 字符串，比如`677|678|700`，我们使用一个正则表达式来检查它是否包含一个特定的 ID。

```
{if segment:child_category ~ "/(677)/"}
    {preload_replace:pre:full_width="yes"}
{/if}

{preload_replace:pre:full_width="no"}

{special_events_sections category="{segment:child_category}" limit="1"}
    <div class="slab slab--flex theme--aqua-haze">
        <section class="slab__cell--{if '{pre:full_width}' == 'yes'}100{if:else}66{/if}">
            <div class="slab--flex__container">
                <div class="cell__content">
                    <!-- content -->
                </div>
            </div>
        </section>
        {if "{pre:full_width}" != "yes"}
            <section class="slab__cell--33">
                <div class="slab--flex__container">
                    <div class="cell__content">
                        <!-- content -->
                    </div>
                </div>
            </section>
        {/if}
    </div>
{/special_events_sections} 
```

Enter fullscreen mode Exit fullscreen mode

您还可以使用预加载替换变量来定义分部。ExpressionEngine 的[模板片段](https://docs.expressionengine.com/latest/templates/partials.html#template-partials)不支持嵌入参数，例如`{embed="group/template" foo="bar"}`，但是你可以通过预加载替换来模仿该行为。下面是一个虚构的例子，但是它说明了如何使用 preload replace 来创建一个可以根据使用方式和位置进行配置的分部。

```
<article class="heading heading--{pre:section}">
    <{pre:heading_tag}>{title}</{pre:heading_tag}>
</article> 
```

Enter fullscreen mode Exit fullscreen mode

使用该分部变量时，您可以在分部变量之前定义预加载替换变量:

```
{preload_replace:pre:heading_tag="h2"}
{preload_replace:pre:section="products"}
{article_heading_partial} 
```

Enter fullscreen mode Exit fullscreen mode

-
最初由 [Brian Litzinger](https://u.expressionengine.com/author/brian-litzinger) 在[u.expressionengine.com](https://u.expressionengine.com/article/preload-replace-variables)发表