# 杰基尔呼叫竖锯

> 原文：<https://dev.to/gonedark/jekyll-to-jigsaw-4017>

所有这些*年回顾*和*新年决心*激励了我。我决定用它来完成我自己今年的[目标之一——重新设计博客。这或多或少是一次即兴的黑客马拉松，我把时间限制在 4 个小时，以确保我不会陷入错综复杂的重新设计一切的兔子洞。](https://dev.to/gonedark/2018-lookbehind-lookahead-2019-4pnn)

这将是一个简单的翻新，以现代化的模板，重点是复制，以及静态网站生成器的转换。我决定将我的博客重构为[拼图](https://jigsaw.tighten.co/)。因此，这不仅是我第一篇为这种转变命名的文章，也是对这一过程的回顾。

## 为什么要竖锯

我的博客已经存在十多年了。我不能告诉你它被转换了多少次。它最初以静态 HTML 开始，然后是 *WordPress* ，然后是 *Jekyll* ，然后是 *GitHub Pages* ，然后是 *Ghost* ，然后回到 *Jekyll* 。现在，当然，竖锯。

从历史上看，我将此作为尝试新技术的机会。现在，由于一些原因，这是相当不切实际的。

1.  博客并没有真正突破技术界限。因此，虽然它可以作为一个介绍，它不会证实它的其他用途。
2.  我的时间最好花在别的地方。如果不是很好的学习机会，就去熟悉的地方。Jigsaw 有一个 PHP 内核，并利用了 Laravel 刀片模板引擎。它还包括一个用 [TailwindCSS](https://tailwindcss.com/) 和一点点 [Vue.js](https://vuejs.org/) 开发的[启动模板](https://jigsaw.tighten.co/docs/starter-templates/)。
3.  这两者结合在一起，让我想到了最后一个方便的原因。写博客已经够难的了，我不想让它变得更难。虽然 Jigsaw 允许我使用 Blade，但我可以继续使用 Markdown。

最后，对我来说，Jigsaw 通过方便使用(PHP + Blade + Markdown)平衡了我的时间，同时还给了我一些轻松的学习机会(TailwindCSS + Vue.js)。

## 转换前端物质

虽然前面的事情很简单，但是这些年来我在所有静态生成器之间转换时创建了很多自定义键。所以我写了一个快速脚本，将这些内容浓缩(并重新格式化)成最少的:`title`、`description`、`date`、`categories`和一些 SEO 关键字。

这是我写的转换脚本的要点。

## 重写网址

这次重新设计的另一个动机是最终切换到 [jasonmccreary.me](https://jasonmccreary.me) 。我不久前购买了这个域名，但是没有把它作为主要域名。因为我无论如何都要改变我每篇文章的链接结构，所以我抓住机会两者都做了。

关键的事情是确保我的网址重定向，这样我就不会失去我的搜索引擎排名。我确保在谷歌搜索控制台和谷歌分析中切换适当的设置，并为每篇文章生成`301`重定向。Jigsaw 配置和模板也做了一些调整，以确保生成的 URL 一致地包含尾随斜杠。

这里有一个这些脚本的[要点，将 Jekyll 帖子的日期文件名转换成 dasherized Jigsaw 帖子文件名，并生成`301`重定向。](https://gist.github.com/jasonmccreary/2eef6508bba04703ce202612f76ececd)

## 调整类别

在很大程度上,“竖锯”是一种直接的转变。唯一需要一分钟的事情是配置类别。文档对这些是如何设置的(可能是开源的贡献…)没有太多说明。同样，由于我熟悉 PHP 和 Blade，挖掘代码并弄清楚它并不太难。

最初，它不会自动生成类别页面(或集合)。最终，在开始模板中有一个`source/_categories`文件夹，我需要在那里为我的每个类别创建降价文件。

不过，还不完全是这样。`config.php`中的`categories`闭包也需要一些调整。尤其是因为我的类别名称是标题，并且包含空格。我有两个选择，我可以在模板中添加代码来生成小写的、屏蔽的 URL，或者我可以在构建时这样做。

为了保持简单的编写体验，我倾向于在构建时正确地生成这些代码。所以我把配置改成了小写，并对前面的分类名称进行了模糊处理，并对文章进行了适当的分组。下面是具体的代码以及完整的`config.php` 的[要点。](https://gist.github.com/jasonmccreary/9890185a5cf9644b5bb1fa5fd772d263) 

```
'posts' => function ($page, $allPosts) {
    return $allPosts->filter(function ($post) use ($page) {
        $category = str_replace('-', ' ', title_case($page->getFilename()));
        return $post->categories ? in_array($category, $post->categories, true) : false;
    });
} 
```

## 添加读取时间

竖锯也有奇特的[辅助方法](https://jigsaw.tighten.co/docs/helper-methods/)。这很好，因为定制这样的东西和 Jekyll 很痛苦——经常需要在 Ruby 中重写或打补丁。使用 Jigsaw，我只需在`config.php`中添加一个闭包，并通过`$page`对象引用它，就像我处理前面的事情一样。

这让我很容易就能创作出我想在文章中添加的东西- *阅读时间*。这是一个很好的 UX 小技巧，可以估算你阅读一篇文章的时间。我从一些[这些计算](https://marketingland.com/estimated-reading-times-increase-engagement-79830)中推导出这个公式。下面是具体的代码，也是完整的`config.php` 的[要点。我只是在我的模板中调用`$post->readTime()`。](https://gist.github.com/jasonmccreary/9890185a5cf9644b5bb1fa5fd772d263) 

```
'readTime' => function($page) {
    return intval(round(str_word_count(strip_tags($page->getContent())) / 200));
} 
```

总的来说，大约花了 4 个小时的转换。2 个小时是按摩前面的事情，另一个小时是战斗的类别配置。因此，公平地说，使用入门模板设置 Jigsaw 并开始定制只花了大约一个小时。

我计划重新审视这个设计，尤其是在阅读了[重构 UI](https://refactoringui.com/) 之后，并为我的各种[产品](https://basecodefieldguide.com)和[服务](https://jasonmccreary.me/articles/mentoring-pair-programming-development-coaching/)整合页面。我喜欢每次写文章的时候做一点点。我会写一个帖子，花 15 分钟做其他调整。所以我不会称之为完成，我会检查我的第一个 2019 年目标的方框，2019 年重新设计我的博客。

***对竖锯有更多疑问？**在这里或在[推特](https://twitter.com/gonedark)上问我。我很高兴回答，并考虑打包我的一些定制或贡献给初学者模板。*