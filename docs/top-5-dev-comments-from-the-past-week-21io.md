# 上周前 5 名开发者评论

> 原文：<https://dev.to/devteam/top-5-dev-comments-from-the-past-week-21io>

这是你可能已经错过的令人敬畏的开发者评论的每周综述。欢迎并鼓励你使用 **[#bestofdev](https://dev.to/t/bestofdev)** 标签来发表帖子和评论。

**[很抱歉，但这个“满栈”模因让我真的很抓狂/难过](https://dev.to/cubiclebuddha/i-m-sorry-but-this-full-stack-meme-makes-me-really-mad-sad-35hi)** 帖子和讨论值得一探究竟。 [@scotthannen](https://dev.to/scotthannen) 在帖子中提供了最高评论(哦，克里斯·科伊尔刚刚[插话](https://dev.to/chriscoyier/comment/aa7a) ):

[![scotthannen profile image](img/2749bf5eb13837d52df24bf58732c542.png) ](/scotthannen) [ Scott Hannen ](/scotthannen) • [<time datetime="2019-04-17T18:21:32Z" class="date-short-year"> Apr 17 '19 </time>](https://dev.to/scotthannen/comment/a6o9) 

你没有解释你为什么把它当成私人恩怨。我在找你说你或你的一个朋友是全栈开发者的那部分，但是没看到。换句话说，如果有冒犯，被冒犯的一方是谁？

我立刻发现有人知道如何画一匹完整的马，但是他们更擅长画马的一部分而不是另一部分。

我认为这更像是在取笑行业发明了一个无用、无意义的标签，然后迫使开发者认同它。

我最开心的一次是在一个团队中，我开发了从后端数据库和服务到前端渲染和 JavaScript 的所有东西。然后，到了应用 CSS 的时候，我会把它交给真正擅长它的人。我们在前端进行了大量的合作，调整 HTML 和行为。

在前端推进提高了我的技能。我没有成为 JavaScript 摇滚明星，但是我写了很好的、可维护的代码。但是如果我自己做整个事情，它会看起来像那匹马的前面。

在 **[关于迁移到 TypeScript，提高前端开发代码库](https://dev.to/ben/thoughts-on-migrating-to-typescript-and-improving-the-overall-quality-of-the-frontend-dev-codebase-1121)** 整体质量的思考中， [@john_papa](https://dev.to/john_papa) 概述了一个很棒的方法:

[![john_papa profile image](img/ebc88f7b5c1df327884d7f39ba4d8d9f.png) ](/john_papa) [ John Papa ](/john_papa) • [<time datetime="2019-04-17T19:48:45Z" class="date-short-year"> Apr 17 '19 </time>](https://dev.to/john_papa/comment/a6pe) 

我发现进入 TypeScript 的最简单和最舒适的方法之一是保持当前的代码库不变，首先在 VS 代码中将`// @ts-check`添加到 JavaScript 文件的顶部。这有助于编辑器用 typescript 编译器处理您的 javascript。它会引发你可能不知道的问题。这不会改变你的应用程序的运行方式，所以这是一个非常低风险(无风险)的开始方式。

第二...曾经舒适...我建议以 TypeScript 的形式添加新文件。您可能想要创建一个`tsconfig.json`文件和一个`tsc` npm 脚本来编译类型脚本。但是代码本身...我喜欢从我在 JavaScript 中使用的相同编码风格开始。换句话说，如果你正在使用函数和常数和 let/var...继续这样做。只是在进行过程中添加一点点输入和接口。

我看到的 typescript 中最大的障碍是什么？我试图帮助人们的一个大问题是避免所有东西都必须打字的感觉。`any`还行。当你开始的时候，拥抱它。如果有帮助的话，你可以稍后再详细说明。另外，如果类型可以隐式确定，那么就不要显式类型化。这里有一个例子...

```
let name1: string  = 'john papa';

let name2 = 'john papa'; 
```

Enter fullscreen mode Exit fullscreen mode

注意，第二个仍然是一个字符串。我们是这样定义的。为什么要添加更多的代码呢？代码越多，维护起来就越困难，而且会使代码更难阅读。还没买...试试这个...

```
getSpeakers(): Observable<Speaker[]> {
    let speakers$: Observable<Speaker[]>;
    speakers$ = <Observable<Speaker[]>>this.http.get<Speaker[]>(speakersUrl);
    return speakers$;
  } 
```

Enter fullscreen mode Exit fullscreen mode

这样的话可读性会更好...

```
getSpeakers(): Observable<Speaker[]> {
    const speakers$ = this.http.get<Speaker[]>(speakersUrl);
    return speakers$;
  } 
```

Enter fullscreen mode Exit fullscreen mode

感谢放纵我的杂念:)

**[发表意见还是不发表意见？](https://dev.to/lankydandev/to-comment-or-not-to-comment-24el)** ，【就是这个问题】。 [@dbanty](https://dev.to/dbanty) 通过谈论*为什么*与*如何*或*什么* :
来提供答案

[![dbanty profile image](img/9d1d42d1ea3ebe677d851582dea73b05.png) ](/dbanty) [ Dylan Anthony ](/dbanty) • [<time datetime="2019-04-21T15:11:23Z" class="date-short-year"> Apr 21 '19 </time>](https://dev.to/dbanty/comment/a97a) 

我发现评论对于解释为什么比解释如何或什么更有用。你的代码应该足够易读，让人们知道它在做什么，但有时原因并不明显。

就像如果你正在解决你正在使用的库的限制。或者你正在处理一些你现在无法重构的旧代码(技术债务)。是的，我知道理想情况下，你会立即修复代码库中的所有问题，而不是绕过它们，但这并不总是现实的。所以有时候你需要注释来让代码有意义。

回复 **[可以把小项目放到 github 上吗？](https://dev.to/shubham2270/can-i-put-small-projects-on-github-17bh)** ， [@perigk](https://dev.to/perigk) 提供面试官考虑候选人的视角:

[![perigk profile image](img/7d6bdd6c3bbc3537454e4465443f6d60.png) ](/perigk) [ Periklis Gkolias ](/perigk) • [<time datetime="2019-04-20T12:14:44Z" class="date-short-year"> Apr 20 '19 </time>](https://dev.to/perigk/comment/a8h2) 

绝对有。

作为一个参加过面试的人，就面试而言，我会对那些在空闲时间做些小事情的人有积极的感觉。

并不是说我不喜欢看到有影响力的大项目，而是你对工作的热情和关心与规模无关。我更关心频率(即使它本身不是最好的度量)。

当然，这些都是主观的...如果有人不喜欢，不要往心里去。

令人敬畏的 **[重构我写过的最糟糕的代码](https://dev.to/jnschrag/refactoring-the-worst-code-i-ve-ever-written-42c7)** 这篇文章给了 [@jacobmgevans](https://dev.to/jacobmgevans) 一个机会，给了我们一些鼓励，并明确提醒我们所有人都有继续学习和改进的空间:

[![jacobmgevans profile image](img/43a2f7942e81181f88d6867763da91bd.png) ](/jacobmgevans) [ Jacob Evans ](/jacobmgevans) • [<time datetime="2019-04-18T13:37:46Z" class="date-short-year"> Apr 18 '19 </time>](https://dev.to/jacobmgevans/comment/a7bi) 

我记得你毫不掩饰地在 Twitter 上发布这段代码的时候，我超级尊重这段代码。它向人们，尤其是新人展示了每个人都有起点。

这是加倍令人敬畏的，因为它可以向人们展示你将会提高🙂🙂🙂

下周见，更多精彩评论，✌