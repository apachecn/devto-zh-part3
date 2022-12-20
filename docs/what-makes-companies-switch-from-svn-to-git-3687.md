# 是什么让公司从 SVN 转向 Git？

> 原文：<https://dev.to/fpuffer/what-makes-companies-switch-from-svn-to-git-3687>

Git 和 SVN 的利弊已经讨论了很多年了。那么到底是什么让我问这个问题呢？大多数讨论详细地比较了某些技术方面，但是没有问这些方面实际上有多相关。我喜欢把讨论带到更务实的层面。

让我们面对现实吧。大多数专业开发人员，包括我自己，对版本控制并不真正感兴趣。当然，他们知道这是必要的，但他们希望尽可能少地与它互动，而是专注于他们的实际工作。这是或多或少应该在后台发生的事情。当然，作为雇主，你不希望你的开发人员的宝贵时间花在非生产性的东西上。

当比较 SVN 和 Git 时，可以肯定地说后者要复杂得多。更多的事情会出错。它们会出错，会花钱。

两个例子:

1.  在 SVN 有你的本地数据和一个中央存储库。这很容易理解，而且真理只有一个来源。在 Git 中，有多个存储库。一个本地的和可能多个远程的。但这还不是全部。还有一个集结地和一个藏匿点。虽然所有这些在理论上都有意义，但它给了开发人员很多需要记住的东西。这无疑给她的生活增加了复杂性。

2.  在 SVN，提交有简单的连续数字，而在 Git 有 40 位数的散列。我知道在分布式版本控制系统中，像 SVN 那样的编号方案是不可能的。但是，谁真正需要分布式版本控制呢？如今能够离线工作真的是一件大事吗？

您可以在这个列表中添加更多的项目，例如 Git 的神秘命令行界面或修改历史的可能性。

那么，为什么公司不断从 SVN 转向 Git，而不是相反呢？