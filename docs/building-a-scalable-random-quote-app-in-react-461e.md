# 在 React 中构建可扩展的随机报价应用程序

> 原文：<https://dev.to/abdulbasit313/building-a-scalable-random-quote-app-in-react-461e>

Side project 和 real-world app 对于程序员来说是非常迷人的术语，但是建立一个 side project 并不是小菜一碟，我们必须先建立一些项目来获得一些特定的专业知识，然后才能开始自己的项目。Freecodecamp 在这方面很有帮助。所以今天我们要用 React 解决 freecodecamp 的随机报价机挑战。

## 先来规划一下 app

我们把这个小 app 分成了两步。第一步，我们将在单个组件中设计整个应用程序。一旦达到目的，我们将进入第二步，将应用程序分成小的独立组件，如果应用程序在未来变得更大，这将是有帮助的。

### 组件设置

在编写任何逻辑之前，我们将设置组件并用引用和作者值初始化状态对象。该值现在将是一个空字符串。

```
import React, { Component } from 'react'

class RandomQuote extends Component {
   constructor(props) {
      super(props)
      this.state = {
         quote: '', //for quote
         author: '' //for author
      }
   }

   render() {
      return (
         <div id='wrapper'>
            <h1 className='title'>Random Quote App</h1>
         </div>
      )
   }
}

export default RandomQuote 
```

Enter fullscreen mode Exit fullscreen mode

### API 请求包

我们将使用`axios`来表示`API`请求。它的承诺使`Api`请求更容易、更短、更干净。

我们将在`componentDidMount`生命周期方法中调用我们的`API`。

你可能会想为什么`componentDidMount`？

所以这里我们要先明确一个概念，有些新手可能没有意识到这一点。

### 概念

在我们基于*类的组件*中，我们有一些预定义的*方法*，每个方法都有特定的特性和执行时间。
*(将写一篇关于 react 生命周期方法的详细文章)*

> **第一次:**构造函数运行
> **第二次:* *渲染运行
> * *第三次:** componentDidMount 运行

我们可以通过控制台登录所有三种方法来确认它，并查看首先运行的是什么...这是样板文件。

```
import React, { Component } from 'react';

class App extends Component {
  constructor(props) {
    super(props)

    console.log('constructor runs')
  }

  componentDidMount() {
    console.log('componentDidMount runs')
  }

  render() {
    console.log('render method runs')
    return (
      <div>
        <h1>Hello</h1>
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

如果你检查，你会看到这在控制台。

> 构造函数运行 App.js:7
> 渲染方法运行 App.js:15
> componentDidMount 运行 App.js:11

### 在 componentDidMount 中调用 API

所以我们已经看到 componentDidMount 在默认的 render 方法之后运行。所以它是 API 调用的最佳位置。

```
componentDidMount() {
      this.getQuote()
   }

   getQuote() {
      let url = 'https://gist.githubusercontent.com/camperbot/5a022b72e96c4c9585c32bf6a75f62d9/raw/e3c6895ce42069f0ee7e991229064f167fe8ccdc/quotes.json'

      axios.get(url)
         .then(res => console.log(res))
   } 
```

Enter fullscreen mode Exit fullscreen mode

如果我们检查，我们可以在控制台中看到 API 数据。这意味着我们已经成功地调用了一个`API.`

现在我们将使用`setState`属性改变状态对象，并使*引用*和*作者*的值等于我们从 *api* 获得的一些数据。

*该写点逻辑了*

## 逻辑 1:从 API 中随机选择一个报价

如果我们能弄清楚如何从数组中获取一个随机元素，我们就可以为此编写逻辑。作为一个元素，我们有一个引用数组，其中还包含引用和作者关键字。

我们知道，为了在 Javascript 中获得随机数，我们使用内置的`Math.random()` *函数*，并从特定长度获得数据，我们将像这样扩展它

```
Math.floor(Math.random() * data.length) 
```

Enter fullscreen mode Exit fullscreen mode

Math.floor() 将一个数字向下舍入到最接近的整数。

这将给我们一个从 0 到数组长度的随机数，我们将它存储在一个变量`quoteNum`中。

如果把`quoteNum`当做一个指标呢？我们将从*报价数组*中获得一个随机元素。

```
import React, { Component } from 'react'
import axios from 'axios'

class RandomQuote extends Component {
   constructor(props) {
      super(props)
      this.state = {
         quote: '',
         author: ''
      }
   }

   componentDidMount() {
      this.getQuote()
   }

   getQuote() {
      let url = 'https://gist.githubusercontent.com/camperbot/5a022b72e96c4c9585c32bf6a75f62d9/raw/e3c6895ce42069f0ee7e991229064f167fe8ccdc/quotes.json'

      axios.get(url)
         .then(res => {
            let data = res.data.quotes
            let quoteNum = Math.floor(Math.random() * data.length) //quote number
            let randomQuote = data[quoteNum] //actual quote

            this.setState({
               quote: randomQuote['quote'],
               author: randomQuote['author']
            })
         })
   }

   getNewQuote = () => {
      this.getQuote()
   }

   render() {
      const { quote, author } = this.state
      return (
         <div id='wrapper'>
            <h1 className='title'>Random Quote App</h1>

            <div id='quote-box'>
               <div id='text'><p>{quote}</p></div>
               <div id='author'><h5>{author}</h5></div>
            </div>
         </div>
      )
   }
}

export default RandomQuote 
```

Enter fullscreen mode Exit fullscreen mode

您会发现，在应用程序运行后的几毫秒内，您看不到数据，因为从 api 获取数据需要时间。
一旦请求成功，它将使用`setState`在`state`中存储新值，并且我们的`DOM`将使用新数据进行更新。

现在我们只剩下*新报价*和*推特*分享功能。

## 逻辑二:在 Twitter 上分享

推特分享图标将作为锚标签，点击*图标*将带我们去某个地方。所以我们必须弄清楚 anchor 标签中的动态`href`。
`[https://twitter.com/intent/tweet?text=](https://twitter.com/intent/tweet?text=)`带我们进入推特账户，打开推文框，我们在这个 *url* 后写的内容会出现在推文框中。
因此，我们希望在推文框中引用和作者数据。我们知道引用和作者的数据处于*状态*，所以通过使用`ES6 template literals`，我们可以将*引用*和*作者*与 *url* 连接起来

这是我们现在完成的应用程序的样子。

```
import React, { Component } from 'react'
import axios from 'axios'

class RandomQuote extends Component {
   constructor(props) {
      super(props)
      this.state = {
         quote: '',
         author: ''
      }
   }

   componentDidMount() {
      this.getQuote()
   }

   getQuote() { 
      let url = 'https://gist.githubusercontent.com/camperbot/5a022b72e96c4c9585c32bf6a75f62d9/raw/e3c6895ce42069f0ee7e991229064f167fe8ccdc/quotes.json'

      axios.get(url)
         .then(res => {
            let data = res.data.quotes
            let quoteNum = Math.floor(Math.random() * data.length)
            let randomQuote = data[quoteNum]

            this.setState({
               quote: randomQuote['quote'],
               author: randomQuote['author']
            })
         })
   }

   getNewQuote = () => { //will be called on clicking the New Quote button
      this.getQuote()
   }

   render() {
      const { quote, author } = this.state //Destructuring
      return (
         <div id='wrapper'>
            <h1 className='title'>Random Quote App</h1>

            <div id='quote-box'>
               <div id='text'><p>{quote}</p></div>
               <div id='author'><h5>{author}</h5></div>

               <div id='buttons'>
                  <a id='tweet-quote' href={`https://twitter.com/intent/tweet?text=${quote}  ${author}`} target='_blank' title="Post this quote on twitter!">
                     <span>
                        <i className="fab fa-twitter twitter-icon" /> //fontawesome twitter icon
                     </span>
                  </a>
                  <button id='new-quote' className='buttons' onClick={this.getNewQuote}>New Quote</button>
               </div>
            </div>
         </div>
      )
   }
}

export default RandomQuote 
```

Enter fullscreen mode Exit fullscreen mode

对于*新的引用*按钮，我们调用`getNewQuote`中的`getQuote`方法并绑定它`onClick props.`

## 造型

我们需要设计三样东西

> 1.设计背景
> 2。设计报价框
> 3。设计按钮

这篇文章不是关于造型的。如果你有什么不明白的，你可以在评论区提问。我增加了媒体查询，让它在小屏幕上也能响应。

```
@import url('https://fonts.googleapis.com/css?family=Josefin+Sans|K2D');

body {
  background: linear-gradient(90deg, lightgreen, lightblue);
  font-family: 'K2D', sans-serif;
  display: flex;
  justify-content: center;
  align-items: center;
  height: calc(100vh - 100px);
  overflow-y: hidden;
}

.title {
  text-align: center;
  font-weight: 500;
}

#quote-box {
  width: 400px;
  margin: 0 auto;
  padding: 1px 15px;
  font-weight: 550;
  font-size: 22px;
  background: linear-gradient(35deg, #CCFFFF, #FFCCCC);
  text-align: center;
  border-radius: 20px;
  box-shadow: 0px 0px 2px 1px gray;
}

#text p {
  margin-block-start: 0.5em;
  margin-block-end: 0.5em;
}

#author h5 {
  margin-block-start: 1em;
  margin-block-end: 1em;
}

#buttons {
  display: flex;
  justify-content: space-between;
}

.twitter-icon {
  color: #1DA1F2
}

.button {
  font-family: 'K2D', sans-serif;
  font-weight: 500;
  font-size: 1rem;
  padding: 5px;
  border-radius: 50em;
  box-shadow: 0px 0px 3px .5px rgb(82, 81, 81);
  border: 0;
  margin-bottom: 10px;
}

.button:focus {
  outline: none;
  border: none;
}

@media only screen and (max-width: 450px) {
  .title {
    font-size: 22px;
  }
  #quote-box {
    width: 270px;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们完成了第一步。

## 先说一下 app 的缩放

记住，我们总是以一种更容易发展、阅读和维护的方式来构建我们的项目。

## 可重复使用的报价框

假设我们希望以后向应用程序添加更多屏幕/路线，我们希望使用相同的报价框，但使用不同的文本/数据。因此，我们将为此制作一个单独的组件 *QuoteBox* 。我们将对*新报价*和*共享按钮做类似的事情。*

```
// Quote Box component
const QuoteBox = ({ quote, author }) => { //destructuring
   return (
      <React.Fragment>
         <div id='text'><p>{quote}</p></div>
         <div id='author'><h5>{author}</h5></div>
      </React.Fragment>
   )
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们通过`props.`从*随机引用组件*获取作者和引用值

## 可重复使用按钮

假设这是一个客户项目，他改变了主意，问你他想要两个按钮，一个用于下一个报价，一个用于上一个报价，而不是一个新报价按钮。

所以最好做一个可重复使用的按钮，我们会在任何需要相同按钮的地方使用*按钮组件*。

```
//Button component
const Button = ({ onClick, title }) => {
   return (
      <button className='button' id='new-quote' onClick={onClick}>{title}</button>
   )
} 
```

Enter fullscreen mode Exit fullscreen mode

## 可重复使用的分享按钮

如果我们以后想添加脸书、Instagram 和 whatsapp share 怎么办？他们会分享相同的风格，但不同的。所以最好把它写在一个单独的文件里，这样更容易维护。

```
// Social Share component
const TwitterShare = ({ quote, author }) => {
   return (
      <React.Fragment>
         <a href={`https://twitter.com/intent/tweet?text= ${quote}  ${author}`} target="_blank" title="Post this quote on twitter!" id='tweet-quote'>
            <i className="fab fa-twitter twitter-icon" />
         </a>
      </React.Fragment>
   )
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们的随机引用类的样子，现在不是更干净了吗？

```
class RandomQuote extends Component {
   constructor(props) {
      super(props)
      this.state = {
         quote: '',
         author: ''
      }
   }

   componentDidMount() {
      this.getQuote()
   }

   getQuote() {
      let url = 'https://gist.githubusercontent.com/camperbot/5a022b72e96c4c9585c32bf6a75f62d9/raw/e3c6895ce42069f0ee7e991229064f167fe8ccdc/quotes.json'

      axios.get(url)
         .then(res => {
            let data = res.data.quotes
            let quoteNum = Math.floor(Math.random() * data.length)
            let randomQuote = data[quoteNum]

            this.setState({
               quote: randomQuote['quote'],
               author: randomQuote['author']
            })
         })
   }

   getNewQuote = () => { //will be called on clicking the New Quote button
      this.getQuote()
   }

   render() {
      const { quote, author } = this.state
      return (
         <div id='wrapper'>
            <h1 className='title'>Random Quote App</h1>

            <div id='quote-box'>
               <QuoteBox quote={quote} author={author} /> //passing data via props to QuoteBox component

               <div id='buttons'>
                  <TwitterShare quote={quote} author={author} />
                  <Button id='new-quote' title='New Quote' onClick={this.getNewQuote} />
               </div>
            </div>
         </div>
      )
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

这篇文章有点长，希望你能跟上并学到一些新东西。

在下一个教程中，我们将使用不同的设计和概念在 *React Native* 中构建相同的应用。

还有一点，我们使用了一个预建的 API，所以我们将使用*节点、express 和 MonogDB* 来设计我们自己的 *rest API* 。

[这里的](https://codepen.io/Basit600/pen/qvwjbg?editors=0011)是项目的 codepen 链接。