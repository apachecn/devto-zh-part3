# 什么是变量

> 原文：<https://dev.to/codetips/what-is-a-variable-46i8>

> 变量是信息的容器。

[![What is a variable](img/d7782f13d9c586540dda13d59e4678ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N9LC5GpW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1493946740644-2d8a1f1a6aff%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

想象一下，你家里的每个人都不是用名字来标识的，而是用一系列字母和数字来标识的。

你弟弟不再叫**詹姆士**，他现在叫 **cfixe18918zt9o7g4nxl** 。每次你想“提到”他的时候，都要努力回忆起来，这真是一件痛苦的事。

有一天，你决定给你哥哥写封信。你不想一直要写他的名字( **cfixe18918zt9o7g4nxl** )，你想给你妈妈( **uqo2v892exahunofrii0** )、爸爸( **0wj3hcy8userefzdiko7** )和伴侣( **4vxucjom1lakup3to71i** )发同样的信。

你可以让这封信很普通，没有任何名字，但是你想让你的家人感觉很特别，认为你每封信都是专门为他们写的。

因此，你可以制作一个电脑程序，用正确的名字输出你的信件。

```
name = cfixe18918zt9o7g4nxl

Dear $name

.... body of text goes here that has a reference to the persons name 10 times .... 
```

当你写完信后，你可以让你的程序生成它。每次看到`$name`，它都会用你在顶部指定的名字替换它(在上面的例子中`Dear $name`变成了`Dear cfixe18918zt9o7g4nxl`)并打印出来——对每个家庭成员完全个性化。

如果你把这封信发给 20 个家庭成员会怎么样？如果你不使用这种可变技术，你将不得不保存 20 份这封信；为每个人量身定制。然后，写完所有的字母，除了名字之外都完全一样，你意识到有 10 个拼写错误。

使用变量技术——你把它固定在一个地方，然后通过改变程序中的名字再次生成每个字母。

如果没有可变技术，你必须手动修改这封信的所有 20 份副本，确保每一份都完全一样，这样你就不会犯更多的拼写或语法错误。

现在想象一下，如果你写给你的整个大家庭——120 个人，你发现了 30 个错误。你会希望你用了一个变量！