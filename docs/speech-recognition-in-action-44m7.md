# 语音识别在实践中

> 原文：<https://dev.to/internap627/speech-recognition-in-action-44m7>

我经常听购物播客。这是我的最爱之一。第 299 集佩吉·贝利作为嘉宾发表了关于机器学习的演讲。这个演讲很有启发性，她建议初学者直接使用 API。所以，我就是这么做的。

我的任务是为页面建立一个评论区，并决定让用户发表评论而不是打字。网络语音 API 是这项工作的完美工具。API 有两个部分:语音到文本和文本到语音。我们将使用前者。在写这篇文章的时候，MDN 文档声明这是实验性的技术。这可能意味着它可能无法在大多数浏览器上运行。

<figure>

[![comment section](img/fde63ee774b5929e118dbbbc1559e86f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xz18sUR5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u0p0rkx49g4w2b4i56am.png)

<figcaption>Comment Section</figcaption>

</figure>

### 它是如何走下来的:

我的逻辑发生在 React 组件内部。我首先需要创建一个语音识别对象。

```
const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition
const recognition = new SpeechRecognition() 
```

我现在可以在需要的时候调用这个对象的方法了。因此，为了启动它，我将在按钮上附加一个 onClick 事件。

```
<button onClick={handleClick }>Click to speak</button> 
```

在我的 handleClick 中，我将运行以下方法；

```
recognition.start() 
```

这将启动语音识别服务，并开始侦听传入的音频。我们需要做的下一件事是定义 onresult 方法。顾名思义，它确实如此。当用户结束讲话时，返回一个结果。该值以事件的形式传递给 onresult 方法。然后，要访问字符串形式的演讲，我只需深入几层，直到找到“抄本”。

```
recognition.onresult = (e) => {
            const current = e.resultIndex
            const transcript = e.results[current][0].transcript
            const upperCase = transcript.charAt(0).toUpperCase() + transcript.substring(1);
            postComment(upperCase)
            fetchComments()
        } 
```

有了字符串之后，我现在可以在将句子发送到我的 postComment 方法之前将句子大写，post comment 方法将句子发送到 Firebase DB。

```
const postComment = (comment) => {
        //Create document object
        let doc = {
            title: comment
        }
        //Send object to Firebase DB
        db.collection('comments').add(doc).then(doc => {
            console.log(`${comment} added successfully to the database`)
        })
    } 
```

我们差不多完成了。我们需要做的最后一件事是确保用户可以在记录后看到他们的评论。我们会让他们不必刷新页面。为此，我调用设置状态的 fetchComments 函数。正如我们所知，如果状态发生变化，就会触发重新渲染。

```
const fetchComments = () => {
        //Get all comments from Firebase DB
        db.collection('comments').get().then(snapshot => {
        const dbComments =    snapshot.docs.map(item => item.data())
        setComments([...dbComments])
        })
    } 
```

### 我遇到的问题:

在 JS 中工作，您可能已经习惯了大多数变量名都是大小写字母。注意上面的语音识别方法不是。这让我困惑了一段时间，不知道为什么它不起作用。所以，一定要按照文档中的样子来写。

[Github 回购](https://github.com/internap627/tribute/)

尝试发表评论...
[哈比比致敬](https://khabibtribute.netlify.com)

### 快乐黑客！！！