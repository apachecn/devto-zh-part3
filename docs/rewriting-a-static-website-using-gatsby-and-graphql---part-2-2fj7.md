# 使用 Gatsby 和 GraphQL 重写静态网站-第 2 部分

> 原文：<https://dev.to/laurieontech/rewriting-a-static-website-using-gatsby-and-graphql---part-2-2fj7>

**最初发布在[十里广场的博客上。](https://tenmilesquare.com/blog/)T3】**

这是关于决定使用 [GatsbyJS](https://www.gatsbyjs.org/) 和 [GraphQL](https://graphql.org/) 重写我的[个人网站](https://laurieontech.com)的 3 部分系列的第 2 部分。在我的上一篇文章中，我讨论了如何查询静态 Yaml 文件以显示我的数据；比如演讲约会、博客帖子等。在这个过程中，我回顾了一些概念，如 GraphQL 查询、GatsbyJS 插件和 JSX 注意事项(以及错误，很多错误)。如果你想按顺序跟进，先去读那篇[帖子](https://tenmilesquare.com/rewriting-a-static-website-using-gatsby-and-graphql/)。

### 去除液体模板

现在我已经从我的 Jekyll 站点复制了数据并很好地处理了 Gatsby，我必须解决一些我以前在 html 中使用的不兼容的标记。在我的 Jekyll 站点中，我使用 [Liquid](https://shopify.github.io/liquid/) 将一些简单的逻辑嵌入到我的页面中；诸如 if/else 语句、循环等等。

### 环

首先，我要替换所有我正在使用的循环，就像这个。

```
{% for speaking in site.data.speaking reversed %}
        <div class="conference">
             <p class="date">{{ speaking.date }}</p>
        </div>
{% endfor %}

```

我可以通过用 JSX 地图函数替换循环来做到这一点，所以它看起来像这样。

```
{data.allSpeakingYaml.edges.map(({ node }) => (
       <div key={node.date} className="conference"> 
          <p className="date"> {node.date} </p>
       </div>
))} 
```

Enter fullscreen mode Exit fullscreen mode

注意，前面的循环反向遍历了列表。我以后再担心那一块，现在这个工作。

### If 语句

现在我已经替换了所有的循环，我将把注意力转向 if 语句。我使用的大多数 if/else 语句都是针对样式和显示的。不幸的是，由于液体在 JSX 不起作用，我需要替代方法来触发这些情况。

所以在第一个例子中，我根据一个元素是否是列表中的第一个元素来分配样式。

```
<li class="wrapper 
{% if forloop.first %} style2 {% "else" %} style1 {% endif %}
">

```

幸运的是，这相对简单。这是因为我可以通过元素的索引来判断它是否是第一个元素。所以我可以在类赋值中嵌入一个三元运算符，并检查索引是否为零。

```
<li className = {index === 0 ? 'wrapper style2' : 'wrapper style1'}> 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。三元运算符非常有用。我的下一个例子稍微复杂一些，因为我不是在控制类，而是在控制元素本身的存在。不仅如此，我不是检查元素的存在，而是检查元素的属性的存在。

```
{% if talk.video %}
    ( <a target="_blank" href="{{ talk.video }}">Video</a> ) 
{% endif %}

```

我们在[上一篇博客](https://tenmilesquare.com/rewriting-a-static-website-using-gatsby-and-graphql/)中写的代码的好处之一是，第二个考虑不再相关。在之前的帖子中，我们决定现在引用标题和视频作为循环中的顶级对象，而不是 talk.title 或 talk.video 。答案还是三元运算符。

```
<a className={video ? "" : "emptyvideo"}target="_blank" href={video}>
      Video 
</a> 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码确定了视频的存在，并给 div 分配了一个类。剩下的就是用那个类来隐藏 div 中的内容，就像这样。

```
.emptyvideo {
     display: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 定向环路

我需要删除的最后一个液体引用实际上与我们已经看到的内容有关。还记得我在上面的循环中使用的 `reversed` 符号吗？

```
{% for speaking in site.data.speaking reversed %}
     <div class="conference">
         <p class="date">{{ speaking.date }}</p>
     </div>
{% endfor %}

```

现在是处理该功能的时候了。以前，我按照在 Yaml 文件中列出数据的顺序遍历该文件。这意味着我可以在 Yaml 文件的底部添加新的演讲或博客文章，它会首先显示这些内容。我仍然想这样做，这样我就不会因为在文件顶部添加新内容而不小心弄乱文件。

不幸的是，我不能逆转我们现在在 JSX 使用的地图功能。我需要想出另一种方法。事实证明，回到我的 GraphQL 查询就是答案。然而，您不能在 GraphQL 中对资源的隐式排序进行排序，在本例中，排序是我在 Yaml 文件中列出数据的顺序。所以，我需要数据中的一些东西来排序。幸运的是，我的口语数据在属性中包含了日期。

因此，使用“反向”年表意味着更新我的查询，使用这些日期获取数据，首先是最近的日期。

```
{  allSpeakingYaml  (sort:  {fields:  [date],  order:  DESC})  {  edges  {  node  {  conference  year  url  talks  {  title  video  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，我的地图按时间倒序显示数据，因为数据是按时间倒序显示的。尽管我有一个日期字段可以用在这种情况下，但这并不是绝对必要的。我可以很容易地添加一个 id 或索引或其他任何可以给我排序的东西。注意，GraphQL 用来排序的字段不一定要成为查询返回的属性之一。

做完这一切后，我终于有了用 JSX 写的功能完整的页面，并能访问我的 Yaml 数据。然而，我目前并没有利用 Gatsby 最好的部分之一，图像处理。我将在下一篇文章中解决这个问题。