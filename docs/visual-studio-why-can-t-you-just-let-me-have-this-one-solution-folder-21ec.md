# Visual Studio，为什么不能只让我有这个(解决方案文件夹)？

> 原文：<https://dev.to/rionmonster/visual-studio-why-can-t-you-just-let-me-have-this-one-solution-folder-21ec>

*这篇文章最初发表在[我的博客](http://rion.io)上。*

如果你曾经在大约一百个项目的大型代码库中工作过，那么你知道组织你的代码几乎和编写代码本身一样重要。组织总是主观的，有时多个项目就足够了，有时它可以帮助将代码组织成解决方案文件夹之类的东西，您可能会认为这很简单——但正如您很快会看到的那样，情况并不总是如此...

## 命名难；命名解决方案文件夹是不可能的。

虽然有充分的文件证明在软件中命名事物是具有挑战性的，但是你通常可以在你的团队中就用什么来命名达成共识。显然，Visual Studio 中的解决方案文件夹是完全不同的动物，并且**旗舰 IDE 不允许你将一个解决方案文件夹命名为一个已经存在于你的项目中的实体:**

[![](img/484ea955b464f625661d42a12100cf50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t_fE84LA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r1x62nityczada7bvnfc.gif)

所以，在四处搜索了一会儿之后——我真的不能找到一个明确的解决方案或变通办法，所以我决定就我遇到的一个问题写一篇短文。基本上，当物理文件夹(或其他实体，如项目文件)共享相同的名称时，Visual Studio 在尝试创建给定名称的逻辑文件夹时会遇到一点麻烦。**这里的技巧是简单地创建一个新的解决方案文件夹，并使用一个临时名称:**

[![](img/37299c01075125bf62974feb50b536d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yWFt7EFa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0jx5dwacavm0xqqeut2l.PNG)

**然后将内容移动到您想要的文件夹中(至少是名称冲突的实体):**

[![](img/88d7e772f217543caba20eb0c1ae6885.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iM0cT-fC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q4tjkbzz1xl30o0tbpcm.PNG)

**最后，安全地将文件夹重命名为您选择的名称:**

[![the world is right](img/a983adc0f5f78e8c14955221b4423c91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t0l0R7SJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/an4z7vebx1vfvedwswlh.PNG)

就是这样！现在，您可以轻松地组织更大的项目和解决方案，而不管您想要的名称是什么，也不管它是否与项目中的其他实体冲突。