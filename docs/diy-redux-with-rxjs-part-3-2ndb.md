# 使用 RxJS 的 DIY Redux:第 3 部分

> 原文：<https://dev.to/onerzafer/diy-redux-with-rxjs-part-3-2ndb>

<figure>[![](img/131b498c317f1787c78d0c2f9e6b4a09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tqdchS1j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ArBO29FpCIE8eeZ_hhBhVSA.png) 

<figcaption>照片由[史蒂夫·哈拉马](https://unsplash.com/photos/iVGevPcaJzk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上传 [Unsplash](https://unsplash.com/search/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

*到目前为止，在之前的两篇帖子中，我已经涵盖了“如何用 RxJS 创建 Redux 库”和“如何编写 Redux 中间件”这两个主题。在开始这个系列的第三部分也是最后一部分之前，我建议你检查一下下面的前两部分:*

*   [用 RxJS =>RxDx](https://dev.to/onerzafer/diy-redux-with-rxjs--rxdx-1oa1)DIY Redux
*   [用 RxJS DIY Redux:第二部](https://dev.to/onerzafer/diy-redux-with-rxjs-part-2-2lpg)

在这一部分，我将创建一个 **HOC** (高阶组件)来连接 **RxDx** 和 **React。组件**。我假设你已经知道反应。组件，所以我不会再解释了。但是我需要解释一下，这是我将在下面演示的核心。

### 这些高阶组件到底是什么？

基本上，HOC 是一个类**装饰器**。但是什么是班级装饰者呢？有时当我们编码时，我们开始觉得我们创建的所有类都有相同的基本属性。在那一点上，我们意识到我们在重复自己，这完全违背了**干**法则。所以我们开始试图找到一种方法来分离重复的部分。或者当我们编码时，我们需要在不改变实现的情况下向类中添加一个特性。在这两种情况下，我们的目的是实现类之间的代码共享。所以对我们上面描述的问题最好的回答是一种**类工厂函数**。

类工厂函数是返回类的函数。(欢迎来到 javascript 世界的怪异之处)。

```
// the most primitive description of a class factory function
function **giveMeClass** (someArguments) {
   return class **SomeDynamicallyGeneratedClass** {}
} 
```

原始的类工厂函数还不是类装饰器。作为一个类装饰器，类工厂函数需要一个类作为参数，返回/产生的类将是输入类的扩展版本:

```
//enhance decorator
function **ehance** (targetClass) {
   return class **EnhancedClass** extends **targetClass** {
      constructor() {
         this.isEnhanced = true;
      }
}

//usage in enviroment where experimental decorators allowed
@enhace
class **Normal** {}

console.log(new Normal().isEnhanced) // true

//usage in enviroment where experimental decorators not allowed
class **Normal** {}

const **Enhanced** = enhance(Normal);

console.log(new Enhanced().isEnhanced) // true 
```

如前所述， **HOC** 也是装饰器，其工作方式与类装饰器完全相同。一个 **HOC** 接受一个 **React** 组件——这是一个类——并返回另一个 **React** 组件。有关特设的详细文档，您可以点击下面的链接，但如果您有心情 TL；我将继续对它进行总结:

[高阶组件-反应](https://reactjs.org/docs/higher-order-components.html)

现在是时候扩展上面的装饰器示例了，通过用一个 **React 组件**替换类输入和输出，使其成为一个**特设的**。我必须提到，使用 React 组件作为输入/输出给了我们几个机会，但是我将继续使用推荐的方法，用另一个组件包装原始组件:

```
// a primitive HOC which makes A component great nothing :)
function **makeGreat** (WrappedComponent) {
   return class **EnhancedComponent** extends **React.Component** {
      constructor() {
         this.state = {isGreat: true}
      }

      render() {
         return < **WrappedComponent** {...this.props, ...this.state} />
      }
   }
}

// usage
class **LameComponent** extends **React.Component** {
   render() {
      return <div>{this.props.isGreat? 'Great': 'Lame'}</div>
   }
}

const **GreatComponent** = **makeGreat** (LameComponent);

< **LameComponent** /> // this will render <div>Lame</div>
< **GreatComponent** /> // this will render <div>Great</div>

// alternative usage if experimental decorators are allowed
@makeGreat
class **LameComponent** extends **React.Component** {
   render() {
      return <div>{this.props.isGreat? 'Great': 'Lame'}</div>
   }
}

< **LameComponent** /> // this will render <div>Great</div> 
```

我相信我们现在需要更深入，所以系好安全带。在这一点上，你仍然不知道关于 **RxDx** 你可以从这个[链接](https://github.com/onerzafer/rxdx)来检查它，因为我将解释如何连接 **RxDx** 到 **React。组件**。

### 用 React 组件缝合 RxDx:HOC 的真实例子

还记得 [Part 2](https://dev.to/onerzafer/diy-redux-with-rxjs-part-2-2lpg) 和 **store 的**选择器**和 [Part 1](https://dev.to/onerzafer/diy-redux-with-rxjs--rxdx-1oa1) 的 select** 功能吗？这是它们派上用场的时刻，因为我们将使用它们作为 **RxDx** 和 **React 之间的粘合剂。组件**。获取一个选择器或一系列字符串，并返回一个可观察值，以允许我们在状态更新时得到通知。我们可以在 **componentDidMount** 中订阅所有这些可观测量，并且在每次更新时，我们可以使用 **forceUpdate** 重新呈现组件，为了防止内存泄漏，我们必须取消订阅**component will unmount:**
中的所有订阅

```
class **SomeUglyComponent** extends **Component** {
**componentDidMount** () {
      this.subscription = store
          .select(someSelector)
          .subscribe((update) => {
             this.setState({data: update});
             this.forceUpdate();
   }
**componentWillUnmount** () {
      this.subscription.unsubscribe();
   }
**render** () {
      return (<div>{ this.state.data }</div>);
   }
} 
```

好吧，我不得不承认这是可行的，但是**一些难看的组件**并不难看，因为我们只有一个。让我们想象一下，我们必须实现这样的组件几十次，几百次。现在看起来更丑了吧？这个重复任务需要一个通用的解决方案，并且**组件**应该尽可能的简单，这样我们就可以把它叫做 **SimpleAndNiceComponent** 。期望的 **SimpleAndNiceComponent** 将如下:

```
const **SimpleAndNiceComponent** = **connect** ({
   data: store.select(someSlector)
})(({data}) => <div> {data} </div>);

//or
@ **connect** ({
   data: store.select(someSlector),
})
class **SimpleAndNiceComponent** extends **Component** {
**render** () {
      const {data} = this.props;
      return <div> {data} </div>;
   }
} 
```

那么，我们如何实现这种简单性，以及如何将可观察物与道具联系起来呢？答案就藏在 **HOC** s 里面，我们需要比简单的 **HOC** 更进一步，我们需要的是 **HOC** 工厂。我将实现一个 **connect** 函数，它接受观察值并返回一个 **HOC** ，后者接受 **React。组件**并返回一个增强/包装的 **React。组件:**