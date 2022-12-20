# 你在技术面试中犯的 5 个错误&如何避免它们

> 原文：<https://dev.to/emmabostian/5-mistakes-youre-making-in-your-technical-interviews--how-to-avoid-them-465e>

技术面试是任何工程师职业生涯中最伤脑筋的经历。通常，你太担心给面试官留下深刻印象，以至于忘记了放松和做自己。

所以今天让我们深入探讨你在面试中可能犯的五个错误，以及如何避免它们！

# 1。你不要问任何问题

您遇到这种情况的频率如何:

你坐在面试官对面的椅子上。他们要求你解决一个算法编码问题。你完全不知道从哪里开始。

可悲的是，这种情况并不少见；我们都经历过这种情况。但你可能犯的最大错误是什么问题都不问。

如果你没有彻底理解这个问题，或者你不确定是否存在特定的限制...问！

大多数面试官都支持你；他们希望你成功！通过就问题提问，你不仅表明你考虑了问题的所有方面，也给了面试官一个帮助你的机会。如果你问了正确的问题，面试官通常会给你提示，或者帮助指导你。

让我们看另一个场景:

你的面试即将结束。你认为它很棒！然后面试官会问你是否有问题要问他们。你说不，然后继续你的快乐之路。

永远不要没有问面试官一个问题就离开面试。每次面试时，我通常会准备一到三个问题。

当你没有问任何问题就离开面试的时候，这给面试官一个信号，要么你对这个职位不感兴趣，要么你太自信了，以至于觉得没有必要做任何调查。

公司喜欢看到你花时间去研究他们(即使你只花了五分钟去搜索)。)

这里有一些你可能想问的问题:

*   我看到贵公司最近收购了 X；这将如何影响 Y？
*   什么是工作/生活平衡？
*   你提供什么样的机会来提高你的技能和教育水平？即书籍、会议津贴、访问在线教育平台。
*   你典型的一天是什么样子的？
*   你为什么选择为公司工作？
*   在这里工作你最喜欢的是什么？

# 2。你首先求解最优解

许多候选人假设他们必须为算法编码问题提供最优的、性能最好的解决方案。虽然这是真的，但在一定程度上，它不一定是你的第一个解决方案。

从一个强力的解决方案开始，然后努力达到一个优化的解决方案，这是完全可以的。

我们举个例子。

假设您被要求返回一个数组中字母对的数量。

既然您不知道从哪里开始，让我们继续创建一个强力解决方案。

[![Coding example 1](img/a146541592d8c762c56d7d6ce40078ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--icGMLhWo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/90c1gt2sxpj6xv3gdv8j.png)

这个例子在做什么？

*   我们创建了一个名为 alphabetDictionary 的对象，它保存了字母表中的每个字母和一个数字，该数字初始化为 0，表示我们在数组中遇到这个字母的次数。
*   接下来，我们遍历数组的长度，对于每个字母，递增字母表字典中的值。
*   第三步是遍历字母表字典，看看每个字母被找到了多少次。如果这个字母被发现的次数是偶数，那么将它除以二(以获得正确的配对数)并将其添加到 numPairs 变量中。如果该字母被找到两次以上，但不是偶数，则从总数中减去 1(使其为偶数)，然后除以 2(获得对的数量)，并将其添加到 numPairs 变量中。
*   然后，只需返回 numPairs。

这是极其冗长的，但它是有效的！让我们看看我们是否能得到一个更优雅的解决方案。

由于我们不关心哪些字母是成对出现的，我们可以将 alphabetDictionary 初始化为一个空对象。因此，如果找到了字母，我们只添加一个值。

[![Code example 2](img/6fd0aea81ac4cbbd193ab695027efce6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MIItVBsR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fcdbysmq4kp8c46h9ghi.png)

值得注意的是，这两种算法都有完全相同的运行时间 O(n ),但是我们能够使第二个代码片段更优雅一些。

您可以使用增强您的蛮力方法的技术来提出最佳的编码解决方案。

# 3。你在头脑中解决问题

当你在为一个编码例子推导一个解决方案的时候，你必须通过你的思考过程来说话。面试官不可能读懂你的想法；由你来传达你的想法。

即使你不确定解决方案，也要把所有你知道的事情都说出来。你错过了什么？

通过讨论这些要点，你更有可能找到一个可行的解决方案，面试官也会对你如何解决问题有所了解。

面试官更关心你解决问题的能力，而不是你能否 100%正确地解决问题。

# 4。你对自己在某些技术上的经历不诚实

许多候选人在阅读要求特定技术或语言知识的工作申请时，会积累他们的技术经验。

永远不要列出你无法回答的技术、框架或语言。

如果面试官问你“你和 React 一起工作过吗？”你说“是的，我有”，你最好能够回答关于图书馆的问题。

没有比列举一项你基本了解的技术的熟练程度更快的搬起石头砸自己的脚的方法了。

相反，你可以说“我上过一些 React 入门课程，但是没有在任何专业环境中使用过它。这是我真正想学的东西。”

这表明你愿意学习新技能，同时对你目前的技能持现实态度。面试官会欣赏你的自我意识。

# 5。你更喜欢敷衍了事，而不是承认你不知道答案

如果你不知道一个理论问题的答案，不要瞎猜。向面试官表达尊重的最快方法之一就是考虑他们的时间。如果你不知道答案，承认也没关系。

与其假装知道答案，我更喜欢这样回答:“老实说，我不确定，但是如果我必须做出一个有根据的猜测，我会说……”这向面试官表明你是值得信赖和诚实的。

诚实永远是最好的策略。

* * *

技术面试不一定要吓人。如果你做好准备并全力以赴，你就会成功。一个一个地回答每个问题，不要不知所措。记住深呼吸，从把它分解成可管理的小块开始，你就能找到开发人员的工作了！