# 什么是好的提交消息？

> 原文：<https://dev.to/cvortmann/what-makes-a-good-commit-message-181i>

# 什么是好的提交消息？

通信可以说是极限编程(XP)五个价值观中最重要的。

随着分布式团队的兴起，保持各种形式的良好沟通习惯极其重要。鉴于时区的差异，书面交流变得非常重要。提交消息是团队成员之间以及团队和项目生命周期中重要的交流方式，因为它们包含了创建它们的上下文。通过检查项目历史，我们可以找出*为什么*一些决策是在做出的*。*

在写这篇文章的过程中，我发现了两种信息:

1.  无数**无信息**提交**消息**
2.  通过指向存储库之外，以一种模糊了变更的重要内容的格式编写的消息。让我们称它们为**看别处消息**。

我将讨论每一种类型，我们如何改进消息以传达更多的意义和信息，最后，给出一些好的提交消息的例子。

> 团队软件开发中最重要的是沟通。(肯特·贝克，极限编程解释:拥抱变化)

## 无信息消息

无信息的消息是那些在你阅读时没有传达任何信息的消息。例如:

*   `add cli new`
*   `fixes`
*   `fix code review comments`
*   `no message`
*   `description`
*   `wip`
*   `hackz`

在我们可以利用 Git 历史来帮助我们发现问题信息的压力情况下，它们尤其糟糕。

想象一下下面的情况:你刚到办公室，有一个重大的 bug，你的公司正在亏损。因为它似乎与你习惯使用的代码库部分无关，所以你开始使用`git log --grep`和一些关键字进行调查。你什么也没找到。然后你在一个你认为可能与问题有关的文件中使用`git log -- file/path`。您发现一个看起来可疑的提交，可能该提交引入了您看到的错误消息。不幸的是，提交消息没有告诉你*为什么*代码是那样的。然后你看着作者的名字，希望你可以向那个人寻求帮助。不走运，那个人早就离开公司了。

如果提交消息对你有帮助，不是很好吗？在这些信息中，这一切都是不可能的。正如我所说的，它们没有向读者传达任何信息。我们应该避开他们。

## 看别处消息

在这一类别中，消息描述中包含 Github 问题编号或吉拉标识。他们倾向于不包含更多的信息。例如:

*   `Fix issue #123`
*   `[JIRA-1234] Fix a problem with the route system when...`

这些消息有两个问题:

1.  偏爱代码库之外的信息
2.  标识占用了提交消息的宝贵空间

### 偏爱代码库之外的信息

对于信息来说，尽可能接近代码库是很重要的，因为代码库是真理的唯一来源。[吉拉](https://www.atlassian.com/software/jira)或 [Github](https://www.github.com) 或任何从 Git 分离出来的工具都是二级工具。万一它们中的任何一个被烧毁或者我们决定更换提供商，我们仍然可以访问 Git 库。如果所有信息都存在于 Git 存储库之外的其他地方，我们的处境将会非常糟糕。因此，我们应该努力在提交消息中写入尽可能多的信息。那些额外的思考时间向下一位*解释为什么*做出改变(而不是*什么*，因为`git show`会告诉你)是值得的。

### 标识占用了提交消息的宝贵空间

具有 10 个字符的吉拉机票标识占主题行的建议 50 个字符的 20%。有趣的是，我们的眼睛似乎很自然地跳过了提交信息的这一部分。发生这种情况是因为通常我们在 Git 历史中寻找一些我们仍然不知道它与哪个票证相关的东西。类似地，如果你去 Github 项目搜索一个错误消息，所有的提交消息都以一个错误跟踪号开始，你很快就会注意到你的眼睛跳过了那些部分。你的眼睛告诉你，它们不是你要寻找的信息中最重要的部分。

这样，我们就没有多少空间来写真正重要的东西了。当查看 Git 历史树时，我们想知道代码是如何发展的，为什么会有这样的变化。我们几乎没有在寻找一个标识，因为在调查缓存角色问题时，我们还不知道`JIRA-1234`与缓存角色有关。

这并不是说吉拉标识没有用，而是说它们不应该占用大多数重要信息的空间。简单地说，它们不应该首先出现，而应该出现在其他地方。一个建议是把它们作为信息的最后一部分，写在信息正文之后。为此，我们可以使用`---`作为分隔符，并用编号或项目符号列出有用的信息。我们也可以在整个信息中使用`[1]`标签，类似于科学论文中参考文献的写法。

有了这些建议，我们在做`git log`的时候仍然可以瞥见信息。如果我们实际上在寻找与`JIRA-1234`相关的提交，我们也可以找到带有`git log --oneline --grep=JIRA-1234`的提交。这样，所有信息都保留在 Git 存储库中，然后指向其他有用的工具。

## 好的提交消息

下面是我认为好的提交消息的例子，给出了我的论点和其他人提出的指导方针(查看链接部分)。它们取自开源项目(经过一些修改)。我已经将作者和项目名称放在消息本身。

```
Make the cached role coherrent with the actual one

When observing the leader running a master role, set the cached role stored in the state_handler to master as well. Failure to do so resulted in the manually promoted node to continue running with a cached 'replica' role. This led to the failure to create replication slots for the new replicas.

We could do it conditionally, but both reading and writing the role require the same lock, and the unconditional approach makes the unit tests simpler. 
```

```
Fix rewind behavior when paused

* Check if we can rewind when deciding to call Postgresql.follow during pause.

Without this the following sequence of events occurs:

1\. Manual failover from node, demotion sets need_rewind flag. Rewind is not done because can_rewind is False.
2\. Cluster is put into paused mode. Rewind is not attempted because recovery.conf matches.
3\. PostgreSQL goes down (pg_ctl stop).
4\. Because need_rewind is set PostgreSQL is restarted.
5\. Sysadmin is unhappy because Patroni is doing stuff behind his back during pause.

* Allow superuser user to be missing from config for rewind.

In other places a missing superuser authentication section is allowed and we default to libpq's use default OS user with no password. This makes rewind work with a missing superuser configuration.

---

Issue: [#365](https://link/to/issue/365) 
```

```
Make Source.indexOf(ByteString) significantly faster

Previously the algorithm that did this was extremely inefficient, and had worst case runtime of O(N * S * S) for N is size of the bytestring and S is the number of segments.

The new code runs in O(N * S). It accomplishes this by not starting each search at the first segment, which could occur many times when called by RealBufferedSource. 
```

```
Convert specs to RSpec 3.1.7 syntax with Transpec

This conversion is done by Transpec 2.3.8 with the following command:

    transpec

* 46 conversions
    from: it { should ... }
      to: it { is_expected.to ... }

* 7 conversions
    from: it { should_not ... }
      to: it { is_expected.not_to ... }

* 2 conversions
    from: it { should have(n).items }
      to: it 'has n dependencies' do expect(subject.size).to eq(n) end

* 2 conversions
    from: obj.should
      to: expect(obj).to

* 2 conversions
    from: obj.stub(:message)
      to: allow(obj).to receive(:message)

* 1 conversion
    from: == expected
      to: eq(expected)

* 1 conversion
    from: it { should have(n).items }
      to: it 'has n feature' do expect(subject.size).to eq(n) end

* 1 conversion
    from: it { should have(n).items }
      to: it 'has n features' do expect(subject.size).to eq(n) end

* 1 conversion
    from: it { should have(n).items }
      to: it 'has n files' do expect(subject.size).to eq(n) end

For more details: https://github.com/yujinakayama/transpec#supported-conversions 
```

## 格式化

除了以上几点，读一些写得好的东西总是很好的。当有人注意到适当的格式和间距时，我们会感觉更好。这样，我们可以将提交消息视为带有主题和文本的电子邮件。事实上，它们是在通过电子邮件创建和发送补丁时使用的。或者甚至是一封信，因为在世界的一些地方(🇩🇪).)仍然大量使用所以，记住:

1.  用大写字母开始你的句子
2.  不要用圆点结束主题行

### 消息由 Git 生成

还建议我们不要编辑生成的提交消息，因为这会误导 Git 用户。当我们看到以 **Revert** 开始的提交消息时，我们立即认为它是通过调用`git revert`生成的。同样的建议也适用于**合并**消息或 Git 生成的任何其他消息。

## 链接

*   [关于 Git 提交消息的说明](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)
*   [更好提交消息的 5 个有用提示](https://thoughtbot.com/blog/5-useful-tips-for-a-better-commit-message)
*   [如何编写 Git 提交消息](https://chris.beams.io/posts/git-commit/)
*   [GitCommitMessages-open stack](https://wiki.openstack.org/wiki/GitCommitMessages)

* * *

从原始版本修改和更新

[![César H. Vortmann](img/93804275ae8c4c84535cd95056181398.png)](https://medium.com/@cvortmann/what-makes-a-good-commit-message-995d23687ad) [## 什么是好的提交消息？|塞萨尔·h·沃特曼|中型

### 塞萨尔·h·沃特曼<time datetime="2019-08-21T12:12:40.224Z">2019 年 8 月 21 日</time>6 分钟读T3】中](https://medium.com/@cvortmann/what-makes-a-good-commit-message-995d23687ad)