# 如何创建一个灵活的英雄块

> 原文：<https://dev.to/eecms/how-to-create-a-flexible-hero-block-23n7>

所以你想让你的作者能够为你的条目指定一个“英雄”风格的标题，但是当他们不想要的时候有一个后备。

为了这个例子的目的，我们假设你的英雄块有一个图像，标题和描述。

## 让我们设置一些字段

| 字段名 | 字段简称 | 文件类型 |
| --- | --- | --- |
| 英雄形象 | `hero_image` | 文件 |
| 英雄航向 | `hero_heading` | 文本 |
| 英雄描述 | `hero_description` | 文本 |

## 现在添加一些代码

```
{exp:channel:entries channel="my_channel" limit="1"}

{if hero_image}
  <div class="hero" style="background-image:url({hero_image});">
      <h1>{hero_heading}</h1>
      <p>{hero_description}</p>
  </div>
{if:else}
    <h1>{hero_heading}</h1>
    <p>{hero_description}</p>
{/if}

...

{/exp:channel:entries} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里做的是使用一个条件`{if hero_image}`来检查图像是否存在。如果是，那么我们使用`div`容器中的`style=`参数将图像作为背景图像输出。如果它不存在，那么我们只输出我们的普通标题和描述。

就是这样！你需要做的就是增加一些风格！

-
最初由[罗布·艾伦](https://u.expressionengine.com/author/rob-allen)在[u.expressionengine.com](https://u.expressionengine.com//article/how-to-create-a-flexible-hero-block)发表