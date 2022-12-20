# 如何为列表中的第一个条目设置不同的样式

> 原文：<https://dev.to/eecms/how-to-style-the-first-entry-in-a-list-differently-594p>

假设您有一个新闻故事的列表，但是您希望第一个条目的样式不同。这是一种很常见的模式，我们都需要在某些时候处理。幸运的是，ExpressionEngine 允许您以多种方式做到这一点。

# 清点条目

EE 有一个`count`变量，它实际上提供了列表中条目的计数。第一个值为“1”，第二个值为“2”，依此类推。

在这个例子中，我们的条目列表将具有相同的样式:

```
{exp:channel:entries channel="news" limit='10'}
<article>
    <h1>{title}</h1>
    {summary}
</article>
{/exp:channel:entries} 
```

Enter fullscreen mode Exit fullscreen mode

假设我们想使用一个带有计数的条件标签将一个 CSS 类应用到第一个`article`标签上，这样我们就可以用不同的方式设计它:

```
{exp:channel:entries channel="news" limit='10'}
<article {if count == 1}class="first"{/if}>
    <h1>{title}</h1>
    {summary}
</article>
{/exp:channel:entries} 
```

Enter fullscreen mode Exit fullscreen mode

那不是很容易吗？

如果我们想为第一个条目使用不同的 HTML 标题标签会怎样？让我们调整我们的标签，使之发生:

```
{exp:channel:entries channel="news" limit='10'}
<article>
{if count == 1}
  <h1>{title}</h1>
{if:else}
  <h2>{title}</h2>
{/if}
  {summary}
</article>
{/exp:channel:entries} 
```

Enter fullscreen mode Exit fullscreen mode

看到我们在这里做了什么吗？如果条目计数为“1”，则使用`h1`标签作为标题，否则使用`h2`。

让我们再次调整一下我们的例子，我们只对第一个条目使用不同的 HTML:

```
{exp:channel:entries channel="news" limit='10'}
{if count == 1}
  <article>
      <h1>{title}</h1>
      {summary}
      {body}
  </article>
{if:else}
  <section>
    <h2>{title}</h2>
    {summary}
  </section>
{/if}
{/exp:channel:entries} 
```

Enter fullscreen mode Exit fullscreen mode

现在发生了什么，第一个条目被包装在一个`article`标签中，随后的条目被包装在一个`section`标签中。

让我们进一步延伸一下。假设我们希望第一个和第二个条目的样式不同:

```
{exp:channel:entries channel="news" limit='10'}
{if count == 1}
  <article class="first">
      <h1>{title}</h1>
      {summary}
      {body}
  </article>
{if:elseif count == 2}
  <article class="second">
    <h2>{title}</h2>
    {summary}
  </article>
{if:else}
  <section>
    <h2>{title}</h2>
    {summary}
  </section>
{/if}
{/exp:channel:entries} 
```

Enter fullscreen mode Exit fullscreen mode

看看我们如何使用扩展条件来遍历条目，并为前两个条目提供不同的 CSS 类。

最后，假设您希望对最后一个条目采用不同的样式。ExpressionEngine 有一个`total_results`变量可以用来做这件事。

```
{exp:channel:entries channel="news" limit='10'}
<article{if count == total_results} class="last"{/if}>
    <h1>{title}</h1>
    {summary}
    {if count == total_results}
        This is the last entry
    {/if}
</article>
{/exp:channel:entries} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里做的是，如果条目是最后一个，我们将 CSS 类名应用到`article`标签，并提供消息“这是最后一个条目”。

## 使用两个单独的标签

有时使用两个通道条目标签可能是必要的，比如说为了获得想要的文档结构。

在这个例子中，我们使用 first channel entries 标签来引入第一个使用`article`标签的条目，然后在`aside`标签中列出后续的条目。

```
{exp:channel:entries channel="news" limit="1"}
<article>
  <h1>{title}</h1>
  {summary}
  {body}
</article>
{/exp:channel:entries}

<aside>
{exp:channel:entries channel="news" limit="9" offset='1'}
  <h2>{title}</h2>
  {summary}
{/exp:channel:entries}
</aside> 
```

Enter fullscreen mode Exit fullscreen mode

注意，第二个标记限于九个条目，还有一个`offset="1"`参数。offset 告诉 ExpressionEngine 获取除第一个新闻条目之外的最新的九个新闻条目。

-

最初由 u.expressionengine.com[的](https://u.expressionengine.com/article/how-to-style-the-first-entry-in-a-list-differently)[罗布·艾伦](https://u.expressionengine.com/author/rob-allen)发布