# 向后期项目添加开发人员

> 原文：<https://dev.to/roytang/adding-developers-to-a-late-project-23d6>

对于任何不平凡的软件项目，至少中等规模的团队，加入一个新的团队成员会有很大的成本，特别是在后期阶段，当你赶着满足最后期限的时候。

最显著的成本当然是在[神话人月](https://en.wikipedia.org/wiki/The_Mythical_Man-Month)中描述的通信开销。有趣的是，一家公司的首席执行官曾经告诉我，他们将为一个延迟的项目增加更多的开发人员以满足截止日期，当我指出增加的开销时，他对我说这不是问题，因为他们只会将模块分配给那些具有最小依赖性的开发人员，这样他们就不必进行太多的交流。哈！我只是耸耸肩，让他认为这是一个好主意。

根据我的经验，这从来都不是一个好主意，尤其是在游戏的后期。即使您能够以某种方式最小化任何领域开销(我非常怀疑)，也有任何新开发人员都需要经历的不小的技术开销问题。我不仅仅是在谈论学习编程语言或技术堆栈——假设你正在给团队增加一个人，他要么已经熟悉了堆栈，要么你已经考虑到他是一个新手，需要培训时间。

不，我说的是模板、UI 标准、编码标准、devops 流程、配置、使用中的库、数据字典等等。大多数项目都有“偏好的做事方式”，在项目中完成的工作越多，这种“部落知识”就越多，并且你需要麻烦别人来解决这些问题。如果项目进度落后，每个人都在努力赶上进度，人们很可能会忘记告诉你一些事情。

稍微相关的旁注:如果项目的标准或部落知识很少，这可能是一个警告信号。我曾经和一个客户一起工作，他有几个分包商来处理他们系统中的不同模块。每个模块/分包商都或多或少地独立于其他模块/分包商工作，只有很少的文档或标准。结果是一个奇怪的大杂烩系统，一个模块以一种方式做事情，下一个模块以完全不同的方式做事情，等等。

举个例子，有一次我被要求帮助一个项目，这个项目有一个相对较大的代码库和很大的截止日期压力，而我对这个领域一无所知，对正在使用的框架也没有什么经验。我告诉他们，我不确定我能达到目标，因为我必须学习一堆东西，但他们非常需要，所以我们还是继续了。现在，我是一名经验丰富的开发人员，所以让我参与比让一些新手参与更好，因为有很多东西我自己可以弄清楚，但是我不得不直接麻烦其他人:

*   对回购的适当访问权限
*   对所有文件的谷歌驱动器的适当访问权限
*   我使用 windows 克隆回购协议时出现 CRLF 问题
*   所提供的开发虚拟机的数据库凭据
*   由于安装文档中缺少未记录的步骤而遇到错误
*   我应该将新的配置选项放在哪里
*   我应该把我的新模块放在菜单的哪里
*   如何将其他表中的数据映射到我的新表
*   正确的 HTML 标记和 CSS 类需要符合它们布局页面的方式
*   如何像其他屏幕一样实现表单验证
*   显示消息/错误的标准方法
*   如何为我的新屏幕设置角色权限
*   进行第三方 API 调用的首选库
*   使用特定组件时遇到的 JS 错误
*   当页面重定向出错时，动态创建的记录不能正常工作的问题

...可能还有很多我已经记不起来了，这还不包括 QA 后来记录的任何评论，因为我不熟悉其他屏幕通常的行为。

如果您将开发人员添加到一个后期项目中，除了增加的通信开销之外，您还必须考虑这些入职成本。这意味着更早地拉响警报并寻求帮助。如果有一个模块是你的开发人员估计要采用的，比如说，一个 sprint，你不能在需要交付之前一个 sprint 就寻求帮助——你需要提前一两个 sprint 寻求帮助。(当然，最好的方法是根本不要把你的项目画到这个角落，但是我们都知道在这个行业中，有时候事情就是这样发生的！)