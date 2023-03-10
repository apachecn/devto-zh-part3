# 查看链表

> 原文：<https://dev.to/denisepen/looking-at-linked-lists-1277>

链表是一种数据结构，它包括一个“链接的”节点序列。每个节点都包含数据。

在单链表中，每个节点指向链表中的下一个节点:

[![Imgur](img/c865bcf210b56ca8f99010ad2e30a017.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--phfruGp5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/4Y6bLlM.png)

在双向链表中，每个节点既指向前一个节点，又指向下一个节点:

[![Imgur](img/9a26b37484bda23b614b650267100603.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---Q7xzBzU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/sCGHclk.png)

为了访问单个节点，您必须遍历整个链表，直到到达该节点。

例如，要找到节点 4，我们必须从节点 1 开始，移动到节点 2，然后是节点 3，最后是节点 4:

[![Imgur](img/1bacff62b07ae12f0aa8623a48f43001.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L_ZPyVT_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/6ddcC2N.png)

这与数组和字典(hash/object)有很大的不同，数组中的每个值都可以通过索引找到，而字典中的值可以通过键找到。

链表的一个好处是可以使用常数 time (O(1))在链表的开头添加一个节点。这意味着后续节点不需要像在数组中那样被重新索引。最新的节点只是成为链表的“头”。

[![Imgur](img/290dee8564be66425264202ea9fbeb8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v9qH3ekM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/puV3Ehl.png)

你在应用程序中使用过链表吗？

如果是的话，你为什么选择它们而不是其他数据结构？

*最初发表于[http://deniseonaquest.com/looking_at_linked_lists](http://deniseonaquest.com/looking_at_linked_lists)T3】*