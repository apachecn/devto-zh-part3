# 我在第一个 React 应用中犯的错误

> 原文：<https://dev.to/statebait/mistakes-i-made-in-my-first-react-app-5afd>

我决定为所有开始使用新 React 项目的人写这篇文章，希望能以某种方式帮助他们。
为什么反应？很简单，在我人生的这个阶段，我刚刚步入 Web 的世界，而 React 是最流行的前端框架。

[![react-universe](img/0439fa018f55b8d40eccf480b19a7741.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZT-VKBjy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/gmvyt6fpevttxn690qyw.jpg)

我构建的应用程序用于我大学的学生会选举，我花了大约 2 个月的时间来完成它。链接到 GitHub 库:[https://github.com/shadxx7/elections-frontend](https://github.com/shadxx7/elections-frontend)。让我们开始吧:

## 状态管理

如果你已经学习了足够多的 React 和任何优秀的 JS 前端框架，你就会知道状态管理的重要性。良好的状态管理是可伸缩和灵活的应用程序的关键。

React 允许你使用不同的状态管理库，最流行的是 Redux，正如我所料，我也在这个项目中使用了它，但让我澄清一下， **Redux 并不总是答案**。现在不要误会我的意思，Redux 是伟大的，但*是的臭名昭著，但*它并不意味着所有的项目。Redux，正如它自己定义的那样，是一个“可预测的状态容器”，这基本上意味着它使您能够在整个 React 应用程序中维护一个全局状态，并且您可以将这个状态插入到任何您希望的组件中。听起来不错吧？当您意识到要实现这一点需要编写大量开销代码时，问题就来了。有人会说，问题出在哪里？问题是，有时没有必要维护一个全局状态，这当然会随着您的用例而变化，但是我不知道如果我决定以不同的方式做事情，我会节省多少时间。

所以结论；不用 Redux？不，这更像是在决定一个状态库之前，最好是评估应用程序的需求，研究不同的库，比如 MobX，或者甚至使用 React Context API，然后最终确定你想使用什么以及在哪里使用。

## 测试

“测试”,这个词能够激起全世界数百万开发者的仇恨情绪。不管你怎么想，我犯了一个可怕的错误，直到我完成了大部分的申请。起初，为应用程序中添加的每个小 UI/功能编写测试似乎并不明显，但是当您最终确定应用程序并需要执行完整的测试时，手动执行测试是很痛苦的，而且也不可能覆盖所有不同的测试用例，尤其是在 UI 测试方面。这时，您会意识到在编写组件的同时编写测试非常方便。目前，为了编写我的测试，我正在使用 [react-testing-library](https://testing-library.com/docs/react-testing-library/intro) ，到目前为止我发现它非常棒。其他图书馆也存在，像 Airbnb 的[酵素](https://airbnb.io/enzyme/)。

## 类别和功能组件

当开始使用 React 时，每个人通常都更喜欢类组件而不是功能组件。我最初也到处使用类组件。话又说回来，这只对 React 有意义，
生命周期方法只对类组件可用(这是假设钩子还没有释放)，
但是功能组件的使用非常重要。这是因为它们在性能方面比类组件更快。当你的页面
变得组件繁重时，它们会有很大的不同，此外，它们在代码方面看起来更干净，因此增加了代码的可读性。

## 真相

现在有了更小的问题和错误；

[![react-child](img/8be41192f245f51221f58096c957649a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DHLbYzzv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/7l32ip2g8wube9phyqgt.png)

### a .试图渲染空函数

这个错误浪费了我生命中的几个小时。基本上，当你试图像这样渲染一个类函数时:

```
renderFields = () => {
    const currentCommittee = this.props.poll.currentCommittee;
    return <div>{currrentCommittee}</div>;
  };

render {
 return (
        <div>
              {this.renderFields()}
        </div>
      );
} 
```

如果道具没有在第一次渲染时加载，它将返回一个错误，这意味着如果你加载道具有任何类型的延迟(例如，从你的后端),上面的代码会中断。解决这个问题的快速方法是使用 lodash 库的“get”函数，快速检查道具是否已经加载，就像这样:

```
render {
  return (
    <div>{_.get(currentCommittee, "candidates") && this.renderFields()}</div>
) 
```

### b .文件夹结构和命名方案

有一个好的文件夹结构是必要的，但是在上面浪费太多时间是不必要的。简单地决定流行的选择，然后根据你的需要稍微调整一下就可以了。我最后关注的是:

```
/src
  /assets
  /components
  /store
  /style
  /tests
/utils 
```

/图像、图标等资产。/components 用于我的所有组件，/store 用于我的 Redux 存储和操作，/style 用于所有 css 文件，/tests 用于所有测试，/utils 用于一些额外的实用函数文件。

我建议不要把 css 文件放在一个独立的样式文件夹中，而是把它们放在各自的组件文件夹中。

对于组件文件和文件夹的命名，由于我所做的冗余 python 项目，我最初遵循 Snake case (hello_world)命名方法。在 React 项目中使用这种命名方案的问题是，它不符合在 JSX 命名 React 组件时使用的严格的 Pascal 大小写(HelloWorld)。因此，为了避免进一步的混乱和满足我的强迫症，我将所有的组件文件和文件夹切换回 Pascal case。

### c .用现代的反应

React 经常更新新的特性和改进，这不应该是新闻，因为它是目前最流行的 JavaScript 前端框架。尽管 React 核心团队很少引入突破性的变化，但为了拥有性能最佳的应用程序，跟上最新的变化是势在必行的。仅仅在这个项目过程中，React 就发布了 10 多个版本。在这一点上，我所说的现代 React 将是 React 上下文 API、React 悬念、React 挂钩、React 并发等。首先，我觉得我可以使用 React Context API 来替换我使用的大部分 Redux，这样可以节省我很多时间。

### d .本应该使用 Typescript？也许吧。

所以，在写这篇文章的时候，我已经学会了 typescript，我清楚地看到了它的重要性。没有太多我可以直接从项目中解释的，但这里是我不得不说的；Typescript 无疑非常有帮助，尤其是对于较大的项目。是的，它确实增加了一点额外的代码，但是比起与类型相关的一连串错误，你更喜欢编写额外的代码，因为你会浪费时间去调试这些错误。

## 包装完毕

这些都是我犯的错吗？不，当然不是，还有很多其他的，但我认为它们不值得一提，主要是因为它们要么是我的愚蠢造成的，要么是不可避免的。