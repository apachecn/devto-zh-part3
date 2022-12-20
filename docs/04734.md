# IMAP:自上次检查以来的新邮件

> 原文：<https://dev.to/kehers/imap-new-messages-since-last-check-44gm>

在过去的几个月里，我一直在做 SharedBox。想法很简单:允许团队在空闲时间收发电子邮件。我不得不允许通过 IMAP 连接电子邮件，这样做意味着每次检查都只能收到新邮件，这很重要。

#### 消息 id 和其他故事

让我们从对消息 id——序列号和 uid——的一些基本理解开始。这很重要。这些数字是如何在邮箱中识别消息的 <sup id="fnref1">[1](#fn1)</sup> 。(注意，*邮箱*指的是一个信息文件夹——收件箱、已发邮件、草稿……而不是整个邮箱)。

邮件序列号是邮箱中邮件的顺序编号。这意味着邮箱中的第一封邮件得到 1，第二封邮件得到 2，依此类推。如果消息被删除，号码将被重新分配。例如，给定一个包含 10 封邮件的邮箱，如果删除了邮件 7，则序号为 8 的邮件现在变成 7，9 变成 8，10 变成新的 9。所以这个[1，2，3，4，5，6，7，8，9，10]就变成了这个[1，2，3，4，5，6，7，8，9]。这说明这个数字不是唯一的，并且总是可以改变的。

UID 类似于序列号，但“更”独特。它不受消息删除的影响。在上面的例子中，即使序列号为 8 的消息现在是 7，它的 UID 仍然是 8。新的消息 UID 数组将是[1，2，3，4，5，6，8，9，10]。这是否意味着 uid 永远不会改变？不会。服务器上可能会发生重置消息 uid 的情况。就像邮箱被重建一样。不过好消息是，当 uid 改变时，有一种方法可以知道。输入 UIDVALIDITY。

UIDVALIDITY 是选择邮箱时返回的一个附加值。每次消息 uid 唯一时，该值都应该相同。如果在任何邮箱选择(会话)中，该值与上次会话中的值不同，请注意您的消息 uid 已经更改。因此，在每个会话中存储这个值非常重要，这样您就能够在下一次检查时进行比较，以了解消息 uid 是否已经更改。

那么拉消息的时候序列号和 uid 是从哪里来的呢？我们使用它们来指定要提取的消息或消息范围。为了提取一系列消息，我们需要指定一个消息 id (uid 或序列号)的序列集。这可以采用以下格式:

*   `4`(仅 id 为 4 的消息)
*   `2,4,9`(消息 2、4 和 9)
*   `4:7`(消息 4 至 7，即 4、5、6、7)
*   `12:*`(邮箱中第 12 条消息到最后一条消息。如果邮箱里最后一条信息是 14，那就是 12，13，14。然而，如果盒子中的最后一条消息小于 12，比如说 10，那么它就是 12 的数字，即 10，11，12)
*   `2,4:7,9,12:*`(以上格式的组合)

既然我们对什么是消息 id 有了一个基本的概念，就很容易开始提取电子邮件了。我将展示一些使用 Javascript 库 [emailjs-imap-client](https://github.com/emailjs/emailjs-imap-client) 的例子。然后我们将进入困难的部分，提取新的消息(在最后一次检查之后)。

(该库可以在浏览器中使用，但我将在 Node.js 中使用它。所以作为第一步，安装:`npm install —save emailjs-imap-client`)

让我们从连接到 IMAP 服务器和盒子选择开始。看看选择动作的响应是什么样的。

```
(async function (){
  // Connect to the imap server
  const imap = new ImapClient.default('imap.mail.yahoo.com', 993, {
          auth: {
            user: 'awesomeme@yahoo.com',
            pass: 'ninjaninja'
          }
      });
  await imap.connect();

  // Select the "mailbox" you want to "interact" with
  const box = await imap.selectMailbox('INBOX');
  console.log(box);
})() 
```

这应该会给你这样的回应:

```
{  "readOnly":  false,  "exists":  1,  "flags":  [  "\\Answered",  "\\Flagged",  "\\Draft",  "\\Deleted",  "\\Seen",  "$NotPhishing",  "$Phishing"  ],  "permanentFlags":  [  "\\Answered",  "\\Flagged",  "\\Draft",  "\\Deleted",  "\\Seen",  "$NotPhishing",  "$Phishing",  "\\*"  ],  "uidValidity":  1,  "uidNext":  686,  "highestModseq":  "108661"  } 
```

注意`uidValidity`和`uidNext`字段。还要注意`highestModseq`。我们会找到的。另一个你可能感兴趣的参数是`exists`。它返回邮箱中当前可用的电子邮件数量。即使邮箱可能收到了许多电子邮件，但当前只有一封留在邮箱中。

让我们扩展我们的例子来拉序列号为 1 的消息:

```
(async function (){
  // ...
  const messages = await imap.listMessages('INBOX', '1', ['body[]']);
})() 
```

我们还可以拉取 UID 为 686 的消息:

```
(async function (){
  // ...
  const messages = await imap.listMessages('INBOX', '686', ['body[]'], {byUid: true});
})() 
```

从邮箱中取出所有邮件很容易。您所需要做的就是指定一个消息序列`1:*`。(这可能不是一个好主意，因为邮箱中的邮件数量可能会阻塞您的应用程序。但是你总是可以拆分进程`1:500`、`500:1000`等等)。当您只想从服务器上提取新邮件(上次提取后的邮件)时，棘手的问题就来了。如果你认为单向同步很棘手，那就等着尝试双向同步吧。

#### HighestModseq 和 ChangedSince

`highestModseq`如上图所示，选中邮箱时返回的是邮箱中所有消息的最高序列号值。一旦您选择了一个邮箱，并且该数量大于上次检查时的数量，您就可以认为该邮箱已经发生了更改。然后，您可以使用最后一个值来提取所有新消息。

假设我们第一次检查用户邮箱时，`highestModseq`是 100。下一次是 120。这告诉我们邮箱发生了变化。然后，我们可以从我们的`highestModseq`为 100 时获取新消息。

```
(async function (){
  // ...
  const messages = await imap.listMessages('INBOX', '1:*', ['body[]'], {changedSince: '100'});
})() 
```

这很简单也很有效。不过，只有一个问题。不是所有的服务器都支持`highestModseq`。

#### \最近？\见过？

有一个`recent`标志可以用来从服务器获取“最近”的消息。但问题是服务器对“最近”的定义是相对的。我的意思是:

*   你在晚上 9:00 与服务器断开连接
*   晚上 9:02 有两条新消息进来。服务器用最近标志标记这些邮件。
*   您在晚上 9:05 再次连接，使用最近标志检查新邮件，您会收到 2 条新邮件。
*   不久后您断开连接，服务器会删除邮件上的最近标志
*   晚上 9:07 有一条新消息，标记为最近
*   另一个邮件客户端，不是你，连接到服务器拉邮件
*   从邮件中删除最近标志
*   您使用 remove 标志在晚上 9:10 连接。即使自上次检查以来有一条新消息，您也没有收到任何消息。

旗是相似的，但也经历了同样的命运。如果另一个客户端打开该邮件，该标志将被删除。在另一个客户端已经“看到”消息之后，试图获取“看不见的”消息将不会返回任何内容。

#### 搜索自

我们可以将 IMAP 的搜索功能与一个`since`参数结合起来，以获取自上次检查以来的新邮件。这将是一个很好的解决方案——存储我们最后一次检查的内容，并使用它来获取此后的新消息。但是 IMAP 有一个限制。`since`参数只接受日期，不接受时间。

#### uidValidity + uidNext

我们可以使用下一个 UID 将会是什么的知识(考虑到`uidValidity`是否已经改变)来做这件事吗？绝对的。如果在第一次拉取时，uidValidity 为 1，uidNext 为 686，那么我们可以拉取自上次拉取以来的新消息，序列集:`686:*`如果 uidValidity 仍然为 1。

```
(async function (){
  // ...
  const messages = await imap.listMessages('INBOX', '686:*', ['body[]'], {byUid: true});
})() 
```

uidValidity 变了怎么办？那么我们可以假设邮箱发生了重大变化——它已经被重新创建了。我们只需要假设我们再次开始同步—我们存储新的 uidValidity，并使用新的 uidNext 作为我们的序列集。

* * *

1.  [https://tools.ietf.org/html/rfc3501#section-2.3.1](https://tools.ietf.org/html/rfc3501#section-2.3.1)↩