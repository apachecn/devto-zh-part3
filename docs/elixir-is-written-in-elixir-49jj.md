# 仙丹写在仙丹里

> 原文：<https://dev.to/sushant12/elixir-is-written-in-elixir-49jj>

[![](img/c1f282cc515be91d87c80fdc016ab3be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZfmpJG8N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/herz5rqy77d4bi1uk9h2.png)

好吧，我有点夸张了这个标题，但是大部分仙丹的来源都写在仙丹本身的上面。但是怎么做呢？先有鸡还是先有蛋？

原来，在编译器设计中有一种叫做 Bootstraping 的技术，可以创建自编译编译器。也就是说，你用 X 语言编写 Y 语言的核心编译器，然后用 Y 语言编写 X 编译器的新版本。然后用 X 编译器编译 Y 编译器。去你的。

在 elixir 中，核心编译器是用 Erlang 编写的，工具和库是用 Elixir 本身编写的。

来自一个没有计算机科学学位的 ruby 背景的人，这真的让我措手不及。