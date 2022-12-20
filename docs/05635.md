# 我从独自建立第一个网站中学到的 4 件事

> 原文：<https://dev.to/clairemuller/4-things-i-learned-from-building-my-first-site-solo-1e26>

上周，我和我在西雅图熨斗学校的同学们完成了我们的模块 3 项目；任务是使用普通 Javascript 和 Ruby on Rails 后端构建一个单页面应用程序。剩下的就看我们自己了，我决定制作 [Jeoparody](https://clairemuller.github.io/jeoparody/) ！这是我自己建立的第一个网站，我从中学到了很多。所以对于我的博客帖子，我认为写下我学到的最重要的事情会有所帮助。

# CSS 很怪异

进入这个项目，我对自己的 CSS 技能相当有信心。我曾经在 [Codecademy CSS 课程](https://www.codecademy.com/learn/learn-css)上工作过，并在控制台上修修补补，所以我认为它相当简单。当你不知道你所不知道的…

我最大的困难是弄清楚如何在 div 中垂直居中文本。“这有什么难的，”在花了至少一个小时调整填充、边距、高度和宽度后，我问自己。我终于发现，简单地将文本包装在 p 标签中就可以了！除非...文本变长并换行成为多行。还没想出这个。

[![CSS](img/55f90a036b0fe5b6c4850da14ca6120c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QaThI4zn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n9caksq1gd2504ihmyaf.png)

# 种子聪明

当我们谈到我认为我已经做好的事情的时候:播种我的数据库。我发现了这个很棒的 API， [jService](http://jservice.io/) ，它拥有每一季《危险边缘》的所有线索和类别。大致有 185000 条线索！我被抽了！我建立了我的线索和类别模型，找出了迭代 API 每个页面的最佳方式，播种开始了。

十五分钟后，播种失败！我很快认识到，人们不应该认为拥有 100，000 多个条目的 API 是完美的。大量的线索被复制，有空字符串，丢失的值，你能想到的。经过大量的反复试验，解决方案是创建两个 if 语句，在将每个线索添加到数据库之前对其进行检查。第一个确保线索实际上有我想要的关键字:问题、答案和类别。第二个 if 语句确保值不仅仅是空字符串。

```
clues.each do |clue|
  if clue.key?("question") && clue.key?("answer") && clue.key?("category")
    if !clue["question"].empty? && !clue["answer"].empty? && !clue["category"]["title"].empty?
      create_clue(clue)
    end
  end
end 
```

# 事件听众很挑剔

我没有太多使用事件侦听器的经验，但显然掌握它们是很重要的。我在网站上实现的第一个非常简单。当用户第一次来到这个页面时，他们输入用户名并点击提交按钮。事件监听器听到“咔嗒”声，然后做它的事情:

```
document.getElementById('submit').addEventListener("click", function(e){
  e.preventDefault();
  username = document.getElementById('username').value.toLowerCase();
  findUser(username);
}) 
```

没有必要“删除”这个事件侦听器，因为一旦用户登录，它所在的 div 就会隐藏起来。轻松点。我使用的下一个事件监听器是针对每个线索 div 的。我已经想出了如何在用户点击时从 div 中删除美元金额，但是事件监听器仍然存在。我尝试了一些事情，最终寻求一些帮助。我的老师给了我一个非常简单的解决方案，只需在一个被点击的线索 div 中添加一个“clicked”类。这允许我在事件侦听器中编写以下代码:

```
if (clueDiv.classList.contains('clicked')) {
  return;
} 
```

因此，事件侦听器仍然存在于每个 clue div 上，但是如果用户单击他们已经单击过的一个，它将简单地返回。轻松点。我的最后一个事件侦听器是给我带来最多麻烦的一个。一旦用户点击了线索，就会出现一个 div，询问他们的问题是对还是错。

[![Yes or No](img/b5053a5d50b82acaede718436dd74dae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XNs25k9N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bexk0wfb9brb961kxei4.png)

我添加了一个事件监听器，监听‘Y’或‘N’的按键，然后继续我的快乐之路。我很快意识到，用户可以在完成提示后很长时间继续按“Y ”,他们的分数可以永远增加！因此，我最终不得不处理删除事件侦听器的问题。经过多次反复试验，我发现 removeEventListener 很挑剔。到目前为止，我一直在事件侦听器中编写整个函数，而不是引用一个单独的函数。当您试图删除侦听器时，这并不可行！简而言之，这是唯一的解决方案

```
document.body.addEventListener("keydown", yesOrNo);
document.body.removeEventListener("keydown", yesOrNo); 
```

# 注释是必要的

我一直很擅长在代码中写注释，但是直到我开始这个项目，我才意识到它们是多么的必要。在我设置好后端之后，我写了第一个获取请求，它就开始了...我当时在状态。

当我在状态中，编码我的小心脏时，很容易忘记写一个快速评论来帮助我未来的自己。但这太重要了！因为即使我认为我确切地知道我所有的代码在做什么，我最终也会睡着。第二天，当我读我的代码时，我不知道发生了什么。所以别忘了给自己留些纸条，省得以后的你将来头疼。

# 结论

总之，我从建立我的第一个网站中学到了很多。如果你是编码新手，我强烈建议你构建一个单页应用程序。这是很好的实践，帮助我巩固了 HTML、CSS 和 Javascript 的知识。编码快乐！