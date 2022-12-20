# 用正则表达式🧠解析用户输入的 URL、时间戳和标签

> 原文：<https://dev.to/benjaminadk/parse-user-input-for-urls-timestamps--hashtags-3dh4>

[https://www.youtube.com/embed/Hwj0oR8uINM](https://www.youtube.com/embed/Hwj0oR8uINM)

[视频代码-教程版本](https://www.youtube.com/watch?v=Hwj0oR8uINM)📽

我过去总是不惜一切代价避免使用*正则表达式*又名[正则表达式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)。除了不知道如何使用它，我在我的代码中看不到它的用途。公平地说，我认为 *RegEx* 对初学者并不友好。然而，我现在发现自己在寻找机会使用它。事实是 *RegEx* 可以节省很多开发时间，是一个强大的工具。

最近，我一直专注于重新创建 YouTube 的部分内容，我注意到一些简单而又酷的东西，关于视频描述和评论。用户可以输入网址、时间戳和标签， **YouTube** 将解析输入并将文本转换成链接。URL 成为外部链接，时间戳是将当前视频搜索到特定位置的链接，而标签则成为查找相关内容的搜索词。

[![youtube-description](img/50ada811617ad65e55b00ec03575cebf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HJv1N38o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9isp1sdkdg2rmpl9wstc.png)

有一些很好的工具和网站可以学习这方面的知识。有时只要谷歌一下`regex for <whatever>`就会找到一些好的**栈溢出**。RegExr 真的很酷。您可以创建一个帐户，将表达式保存到您自己的库中。此外，他们还分解了每个角色及其作用，更不用说社区表达的数据库了。[正则表达式信息](https://www.regular-expressions.info/tutorial.html)对与 **RegEx** 相关的几乎所有东西都有更详细的分解。

现在，本教程假设您已经捕获并存储了用户输入。这就是我们正在解析的原始文本。从这里开始，当我们将文本处理成 **HTML** 时，我们需要处理一些事情。

1.  保留文本格式-间距、换行符等
2.  将文本放入一个 **HTML** 元素中
3.  解析文本中的 URL、时间戳( *HH:MM:SS* 格式)和标签
4.  如果需要，用适当的链接、目标和参数替换它们
5.  奖励:设置视频的时间，根据标签搜索

⚠免责声明-所有代码示例将使用 **React** 和/或 **JSX** 语法，因此 **JavaScript**

保存格式非常容易。一个选项是 **HTML** `pre`标签。`pre`我想是预格式化的简称。🤦‍♂️

```
<pre>{description}</pre> 
```

Enter fullscreen mode Exit fullscreen mode

另一个选项是使用设置为`pre`的`white-space` CSS 属性。我们不妨用`pre-wrap`。否则长长的文本行将溢出它们的容器。

```
<div style={{whiteSpace: 'pre-wrap'}}>{description}</div> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要全力以赴🔫。首先，我们需要找到并理解相关的正则表达式。这里有一个非常标准的表达式来查找`http/s`URL。它基本上找的是`http://anything`，但是好像很管用。注意`g`标志——匹配所有事件，以及`i`标志——忽略大小写。它还可以通过在第一个捕获块中使用 OR 操作符来匹配`ftp`和`file`URL。

```
const reUrl = /(\b(https?|ftp|file):\/\/[-A-Z0-9+&@#\/%?=~_|!:,.;]*[-A-Z0-9+&@#\/%=~_|])/gi 
```

Enter fullscreen mode Exit fullscreen mode

时间戳表达式没那么糟糕。请注意，`(?:)?`设置了可选的捕获组。`[0-5]`有道理是因为在对付`HH:MM:SS`的时候你不会看到`01:90:90`，最高分或秒可以是 59。不管怎样，这是为了配合`MM:SS`和`:SS`而设置的，很酷。这允许用户在使用时间链接时有更多的灵活性。

```
const reTime = /\s(?:(?:([01]?\d):)?([0-5]?\d))?:([0-5]?\d)\s/g 
```

Enter fullscreen mode Exit fullscreen mode

好了，让我们来看看函数本身。我们将在`String`原型上利用`replace`方法。[string . prototype . replace](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace)*can*以`RegEx`为第一个自变量，以一个函数为第二个自变量。这个回调函数可以接收许多参数，但首先是匹配的文本本身。这意味着我们可以在替换字符串中使用原始的 URL/time/hash。这个想法是用合适的 HTML**替换我们的匹配。为了简单起见，我们从 URL 开始。这个过程通常被称为`linkify`过程。明白了吗？🧠** 

```
function linkify(text) {
    return text.replace(reUrl, url => `<a href="${url}" target="_blank">${url}</a>`)
} 
```

Enter fullscreen mode Exit fullscreen mode

为了节省空间，我使用了一个箭头函数并返回了一个模板字符串。目标设置为`_blank`确保该页面将在新窗口中打开。[模板字符串](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)你现在应该已经知道了。

处理时间戳稍微高级一点。我们将需要一个助手函数和一些额外的逻辑来使它们有用。假设我们有一个视频播放器，比如这个例子中的 **YouTube** 。我们希望以`HH:MM:SS`格式显示时间戳链接，但我们需要将该值转换为秒，这样我们就可以设置一个搜索参数，并有一个值可以发送给我们的播放器——**HTML**video 元素有一个名为`currentTime`的属性，它获取/设置视频的时间...秒！我们还需要我们网站上玩家页面的 url 值。

```
function HHMMSStoSeconds(str) {
  var p = str.split(':')
  var s = 0
  var m = 1

  while (p.length > 0) {
    s += m * parseInt(p.pop(), 10)
    m *= 60
  }

  return s
}

function linkify(text) {
    const playerUrl = 'http://www.youtube.com/watch'
    return text.replace(reTime, time => {
        const seconds = HHMMSStoSeconds(time)
        return `<a href="${playerUrl}?time=${seconds}">{time}</a>`
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

作为一个旁注，我真的很喜欢字符串秒函数。我已经有一段时间没有使用`while`循环了。🤓

现在，当用户点击一个时间戳链接时，我们可以在我们的 **React** 组件中实现一些复杂的逻辑来查找链接中指定时间的视频。

```
 class Player extends React.Component {

    componentDidMount() {
        const params = new URLSearchParams(window.location.search)
        const time = params.get('time')
        if(time) {
            this.video.currentTime = time
        }
    }

    render() {
        return <video ref={el=>this.video = el} src={src} />
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这可能看起来很奇怪，因为我们习惯于路由库，但它是可行的。了解 [URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams) 。使用`ref`也是这里的关键。React 有一个特性，可以让我们访问底层的 DOM 节点和所有内置的 API。 [React Refs](https://reactjs.org/docs/refs-and-the-dom.html) 和 [HTML 视频/音频 DOM](https://www.w3schools.com/tags/ref_av_dom.asp) ...很有帮助。

标签的工作方式与时间戳非常相似。由开发人员决定如何将它们实现到 UI 中。YouTube 会搜索任何与标签术语相关的内容。匹配 hashtags 的表达式可能如下所示。

```
const reHash = /(?:\s|^)?#[A-Za-z0-9\-\.\_]+(?:\s|$)/g 
```

Enter fullscreen mode Exit fullscreen mode

这个其实差不多可以理解。但是我们可以把它分解如下。

```
(?: // start of non-capture group
\s  // match space character
|   // logical OR
^   // beginning of string
)   // end non-capture group
?   // match 0 or 1 of preceding
#   // match # character
[]  // enclosed character set
A-Z // capital A through Z
a-z // lowercase a through z
0-9 // digits 0 through 9
\-  // \ is an escape character matches -
+   // requires 1 or more match of preceding token
$   // end of string 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以把所有的东西都集中到一个大函数中。当然，每个人的需求是不同的，但以下将是像 YouTube 的东西。这次我传递的是一个`video`对象。这只是一种方法。然而，在我的实现中，如果时间大于视频的持续时间，我看不出制作时间戳链接有什么意义。检查一下`if/else`块，通过将参数返回给回调函数，就好像我们忽略了那个特定的匹配。值得。

```
import HHMMSStoSeconds from './above-this'

const reUrl = /(\b(https?):\/\/[-A-Z0-9+&@#\/%?=~_|!:,.;]*[-A-Z0-9+&@#\/%=~_|])/gi
const reTime = /\s(?:(?:([01]?\d):)?([0-5]?\d))?:([0-5]?\d)\s/g
const reHash = /(?:\s|^)?#[A-Za-z0-9\-\.\_]+(?:\s|$)/g
const frontend = 'https://www.youtube.com'

export default function linkify(video) {
  return (
    video.description
      .replace(reUrl, url => `<a href="${url}" target="_blank">${url}</a>`)
      .replace(reTime, time => {
        const secs = HHMMSStoSeconds(time)
        if (secs > video.duration) {
          return time
        } else {
          return `<a href="${frontend}/watch?id=${video.id}&t=${secs}">${time}</a>`
        }
      })
      .replace(
        reHash,
        hash => `<a href="${frontend}/search?term=${hash.replace('#', '').trim()}">${hash}</a>`
      )
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

所以如果你真的做到了这一步，你肯定学到了一些东西。我想我花了一天的大部分时间来弄清楚所有这些东西，我不得不从各种不同的网站和搜索中寻找。为什么不把它们都放在同一个地方。自然，可能有更有效或更彻底的正则表达式。但是这些似乎对我的用例很有效。

[https://codesandbox.io/embed/4rpovv5pkw?module=/src/linkify.js](https://codesandbox.io/embed/4rpovv5pkw?module=/src/linkify.js)

[解析器教程](https://www.youtube.com/watch?v=Hwj0oR8uINM)

[克隆组件系列](https://www.youtube.com/watch?v=IJD33I0Ixck&list=PLh6ckU65UHatP5H8ujyQ8IlPC6G5n8A3b&index=2)

[我的 YouTube 频道](https://www.youtube.com/channel/UCZ86uFH5o_5yjjBEbi2oFSw)

[为你做这一切的库](http://markdown-it.github.io/linkify-it/)