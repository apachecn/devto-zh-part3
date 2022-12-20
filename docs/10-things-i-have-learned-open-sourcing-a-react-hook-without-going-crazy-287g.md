# 我从开源 React Hook 中学到的 10 件事

> 原文：<https://dev.to/rohovdmytro/10-things-i-have-learned-open-sourcing-a-react-hook-without-going-crazy-287g>

这是我如何花费大量时间开源 100 LOC 的 10 个问题。我的态度是用足够的动力来交换挫折，让自己变得更好。同时与世界分享一些价值(通过这篇文章和一个开源包)。

所以。

我想通过 hooks 在我的博客上添加一个 MailChimp 订阅表单。我认为把它隔离到一个开源包中会很好。一个钩子用 60 LOC，另一个草率测试用 40 LOC，花费了惊人的大量时间。

这篇文章是我个人旅程的一部分[你可以免费加入并从我的错误中学习](https://blog.swingpulse.com/)。

# 意图

*   拒绝完善每一步来增加实用性，让自己更快地前进。
*   带着适度的挫败感克服日益增加的复杂性。
*   记录我的发现。

# 结果

这是一个 [react-use-mailchimp](https://github.com/rg4real/react-use-mailchimp) hook 的使用示例，用于将 mailchimp 表单嵌入 react 应用:

```
export const Form = () => {
  const url = 'URL_YOU_CAN_OBRAIN_FROM_MAILCHIMP_UI'
  const [{ loading, error, data }, subscribe, reset] = useMailchimp({ url })
  const [email, setEmail] = useState('')

  return (
    <form
      onSubmit={e => {
        e.preventDefault()
        subscribe({ EMAIL: email })
      }}
    >
      <input onChange={e => setEmail(e.target.value)} onFocus={reset} />
      <button type={'submit'}>Submit</button>
      <div>
        {!!loading
          ? 'Loading...'
          : error
          ? 'Error during subscription'
          : data && data.result === 'success'
          ? 'Subscribed!'
          : null}
      </div>
    </form>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

# 我明白了

以下是我在开发过程中遇到的问题和收获。

## #1。配置 Jest

从一开始，我就决定要做一些测试，至少是中等质量的测试。没有想太多，我已经签出了开源代码，看看人们如何做他们的测试。我找到了一个适合我的配置:

`jest.config.js`

```
module.exports = {
  testEnvironment: 'jsdom',
  transform: {
    '^.+\\.jsx$': 'babel-jest',
    '^.+\\.js$': 'babel-jest',
  },
  setupFiles: ['<rootDir>/jest.init.js'],
} 
```

Enter fullscreen mode Exit fullscreen mode

`jest.init.js`

```
import '@babel/polyfill' 
```

Enter fullscreen mode Exit fullscreen mode

这很快让我至少在一段时间内跳过文档，继续做事情。

## #2。用 react-hooks 测试-测试-库

首先我安装了[反应测试库](https://github.com/testing-library/react-testing-library)。但是很快就发现了另一个测试 react 钩子的选项——[react-hooks-testing-library](https://github.com/mpeyper/react-hooks-testing-library)。

用法示例:

```
import { renderHook, act } from 'react-hooks-testing-library'
import useCounter from './useCounter'

test('should increment counter', () => {
  const { result } = renderHook(() => useCounter())
  act(() => result.current.increment())
  expect(result.current.count).toBe(1)
}) 
```

Enter fullscreen mode Exit fullscreen mode

不需要额外的部件来手动包装挂钩。整洁！

关于 react-hook-testing-library 的另一件大事是，它允许在 react hook 中处理异步特性。有一个小小的警告。稍后会详细介绍。

## #3。与`npm link`战斗

这是相当...烦人的那个。`npm link`命令可用于在本地开发中测试您的软件包，而无需将其发布到 npm 注册表。甜蜜，方便，对我来说不是现成的。

React 抛出了关于在同一个应用程序中有两个 React 实例的错误。原因是 npm 链接中的一些 woodoo 魔法。

解决方案简单、丑陋而且必要。

> 当您使用 npm link 或等效软件时，也会出现此问题。在这种情况下，您的捆绑器可能会“看到”两个反应，一个在应用程序文件夹中，一个在资源库文件夹中。

假设 myapp 和 mylib 是兄弟文件夹，一个可能的解决方法是运行 npm 链接..mylib 中的/myapp/node_modules/react。这将使库使用应用程序的 React 副本。

我认为这个问题会在 npm / react 的未来版本中得到解决。

## #4。更好的`npm publish»`

更好的 npm 发布。这个标题在一段时间前一直伴随着我。我从来没有检查过它，但是做一个快速的谷歌搜索发现了一个叫做 np 的[工具来自动化包发布过程。](https://github.com/sindresorhus/np)

[![](img/9203d2c90043dd283c25997272bfeb6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dpsGhsdE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oav1ojgwiv3ygkorw336.gif)

`package.json`

```
{  "scripts":  {  "publish":  "np"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

使用这个工具增加了一些安全性，但没有增加太多的复杂性。太棒了。

## #5。自己与~~战斗~~烦人的臭虫

为了诚实，我需要说这个 bug 是编写 100 LOC 时痛苦的一个重要部分。只是因为一个愚蠢的错误成功地躲过了我的注意。一两个小时，或者...

下面是一行有 bug 的代码:

```
jsonp(url, opts, callback) 
```

Enter fullscreen mode Exit fullscreen mode

是的，这一行简单的代码。但是`url`是一个真实的 URL，但不是我需要的那个。起名很重要，睡眠充足也很重要。

## #6。对抗 react 挂钩的异步特性

如果你的钩子中发生了一些异步的事情，你可能想知道如何测试。有一个简单的方法。

下面是测试的一部分:

```
act(() =>
  /* this one makes a http request */
  result.current[1]({
    EMAIL: EMAIL,
    NAME: '',
  })
)
/* checks loading before request */
expect(result.current[0].loading).toBe(true)
/*
        sweet part.
        this one «waits» until there state of a hook will change.
    */
await act(async () => {
  await waitForNextUpdate()
})
/* checks loading after request */
expect(result.current[0].loading).toBe(false) 
```

Enter fullscreen mode Exit fullscreen mode

但是为了遵循这种方式，我不得不花两个小时意识到我需要使用 React 的 alpha 版本。

`package.json`

```
{  "peerDependencies":  {  "react":  "^16.8.6"  },  "devDependencies":  {  "react":  "16.9.0-alpha.0",  "react-dom":  "16.9.0-alpha.0",  "react-test-renderer":  "16.9.0-alpha.0"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

在开发期间，为了让测试工作，您需要 react 的 apha 版本。但是为了使用它，你可以把^16.8.6 作为附属国。

## **#7 让我们从 react-apollo 偷一个 API**

起初，我保存数据的`state`是这样的:

```
const [{ status, message }, subscribe] = useMailchimp({ url }) 
```

Enter fullscreen mode Exit fullscreen mode

然后我想起 react 有一个很好的 API 来处理请求。他们得出了这样的结论:

```
const = () => (
  <Query query={GET_DOGS}>
    {({ loading, error, data }) => {
        /* ... */
    }}
  </Query> ) 
```

Enter fullscreen mode Exit fullscreen mode

我觉得这样更好。我的钩子的 API 类似于野外的东西。我也不会暴露字符串变量。

所以我把一个 API 转换成:

```
const [{ loading, error, data }, subscribe] = useMailchimp({ url }) 
```

Enter fullscreen mode Exit fullscreen mode

奖金:`data`持有来自 MailChimp 的 API 响应的原始 JSON 表示。

## #8。我需要一个 reset()操作

我需要决定我的钩子公开什么 API。通过自己使用这个钩子，我意识到我确实需要一个钩子的`reset`功能。

搞定了。

```
const [state, subsctibe, reset] = useMailchimp({ url }) 
```

Enter fullscreen mode Exit fullscreen mode

## #9。零配置，多版本

挖掘我偶然发现的开源库`microbundle`。

> 用于微型模块的零配置捆扎机，由 Rollup 提供动力。

`package.json`

```
{  "scripts":  {  "build":  "microbundle -o dist/ --sourcemap false --compress false"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

哦，这种感觉真好，零配置意味着你只需付出最少的努力！

## #10。曝光你的作品教会你

最后一课。

虽然任务看起来很容易，但它却能消耗惊人的时间。在这种情况下，我试图记住，这部分是因为我，部分是因为现实的复杂性。:)这种心态给我留下了足够的压力去改进，但不会让我不知所措或过于沮丧。

如你所见，你可以通过做开源工作学到很多东西。你也可以跳过一些对保持个人动力和完成工作有好处的东西。

# 开源

所有这些都打包到我们可以在任何 react 应用程序中使用的 [react-use-mailchimp](https://github.com/rg4real/react-use-mailchimp) 包中。

如果有任何价值，你想得到更多——去看看我的博客。好东西[在等你](https://blog.swingpulse.com/)！