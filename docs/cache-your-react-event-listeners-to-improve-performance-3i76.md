# 缓存 React 事件侦听器以提高性能。

> 原文：<https://dev.to/charlesstover/cache-your-react-event-listeners-to-improve-performance-3i76>

JavaScript 中一个未被重视的概念是对象和函数如何被*引用*，这将直接影响 React 性能。如果你要创建两个完全相同的函数，它们仍然不相等。自己试试:

```
const functionOne = function() { alert('Hello world!'); };
const functionTwo = function() { alert('Hello world!'); };
functionOne === functionTwo; // false 
```

Enter fullscreen mode Exit fullscreen mode

但是如果你把一个变量赋给一个已经存在的函数，看看有什么不同:

```
const functionThree = function() { alert('Hello world!'); };
const functionFour = functionThree;
functionThree === functionFour; // true 
```

Enter fullscreen mode Exit fullscreen mode

对象以同样的方式工作。

```
const object1 = {};
const object2 = {};
const object3 = object1;
object1 === object2; // false
object1 === object3; // true 
```

Enter fullscreen mode Exit fullscreen mode

如果你有其他语言的经验，你可能会熟悉*指针*。这里发生的事情是，每次你创建一个对象，你在设备上分配一些内存。当我说`object1 = {}`时，我已经在用户的 RAM 中创建了一个字节块，专门用于`object1`。可以把`object1`想象成一个地址，其中包含了它的键值对在 RAM 中的位置。当我说`object2 = {}`时，我在用户的 RAM 中创建了一个*不同的*字节块，专门用于`object2`。`object1`的*地址*是否与`object2`的地址匹配？不。这就是为什么两个变量的等式检查没有通过。它们的键值对可能完全相同，但是它们在内存中的地址是不同的，这就是比较的内容。

当我给`object3 = object1`赋值时，我把`object3`的值赋给了`object1`的地址。它不是*新*的对象。它是内存中相同位置。你可以这样验证:

```
const object1 = { x: true };
const object3 = object1;
object3.x = false;
object1.x; // false 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我在内存中创建了一个对象，并将其分配给`object1`。然后我将`object3`分配给内存中的同一个地址。通过改变`object3`，我改变了内存中该位置的值，这意味着*内存中所有其他对该位置的引用也会改变*。`object1`，仍然指向内存中的那个位置，现在有了一个改变的值。

对于初级开发人员来说，这是一个非常常见的错误，很可能需要一个自己的深入教程；但是本教程是关于 React 性能的，即使是资历更深的开发人员也可能会牺牲性能，因为他们根本没有考虑到变量引用的含义。

这和 React 有什么关系？React 有一种节省处理时间的智能方法来提高性能:如果 PureComponent 的属性和状态没有改变，那么`render`的输出也一定没有改变。显然，如果所有的事情都是平等的，那么什么都没有改变。如果什么都没有改变，`render`必须返回相同的输出，所以我们就不执行它了。这就是让*反应快的*的原因。它只根据需要进行渲染。

React 确定其属性和状态是否与 JavaScript 相同——通过简单地将它们与`==`操作符进行比较。React *不*浅层或深层比较对象以确定它们是否相等。浅层比较是一个术语，用于描述比较对象的每个键值对，与比较内存地址相对。深度比较更进一步，如果键-值对中的任何值也是对象，那么也比较那些键-值对，令人厌烦。React 两者都不做:它只是检查*引用*是否相同。

如果您要将组件的道具从`{ x: 1 }`更改为另一个对象`{ x: 1 }`，React 将重新渲染，因为这两个对象不会引用内存中的相同位置。如果你将一个组件的道具从`object1`(从上面)改为`object3`，React 会不会*而不是*重新渲染，因为那两个对象*是*同一个引用。

在 JavaScript 中，函数的处理方式是一样的。如果 React 接收到具有不同内存地址的相同函数，它将重新呈现。如果 React 接收到相同的函数引用，则不会。

这是我在代码审查中遇到的一个不幸的常见场景:

```
class SomeComponent extends React.PureComponent {

  get instructions() {
    if (this.props.do) {
      return 'Click the button: ';
    }
    return 'Do NOT click the button: ';
  }

  render() {
    return (
      <div>
        {this.instructions}
        <Button onClick={() => alert('!')} />
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常简单的组件。有一个按钮，当它被点击时，它会发出警报。指令告诉你是否应该点击它，由`SomeComponent`的`do={true}`或`do={false}`道具控制。

这里发生的事情是，每次`SomeComponent`被重新渲染(比如`do`从`true`切换到`false` ), `Button`也被重新渲染！尽管`onClick`处理程序完全相同，但是每次`render`调用都是由*创建*。每次渲染时，都会在内存中创建一个新函数(因为它是在渲染函数中创建的)，对内存中新地址的新引用被传递给`<Button />`，并且`Button`组件被重新渲染，尽管其输出中绝对没有任何变化。

## 修罗

如果你的函数不依赖于你的组件(没有`this`上下文)，你可以在组件之外定义它。组件的所有实例将使用相同的函数引用，因为函数在所有情况下都是相同的。

```
const createAlertBox = () => alert('!');

class SomeComponent extends React.PureComponent {

  get instructions() {
    if (this.props.do) {
      return 'Click the button: ';
    }
    return 'Do NOT click the button: ';
  }

  render() {
    return (
      <div>
        {this.instructions}
        <Button onClick={createAlertBox} />
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

与前面的例子相反，`createAlertBox`在每个`render`期间保持对存储器中相同位置的相同引用。`Button`因此*永远*不得不重新渲染。

虽然`Button`可能是一个小的、快速呈现的组件，但是您可能会在大的、复杂的、缓慢呈现的组件上看到这些内联定义，这确实会使您的 React 应用程序陷入困境。最好不要在 render 方法中定义这些函数。

如果你的函数*依赖于你的组件，以至于你不能在组件之外定义它，你可以传递你的组件的一个方法作为事件处理器:* 

```
class SomeComponent extends React.PureComponent {

  createAlertBox = () => {
    alert(this.props.message);
  };

  get instructions() {
    if (this.props.do) {
      return 'Click the button: ';
    }
    return 'Do NOT click the button: ';
  }

  render() {
    return (
      <div>
        {this.instructions}
        <Button onClick={this.createAlertBox} />
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`SomeComponent`的每个实例都有一个不同的警告框。`Button`的点击事件监听器需要是`SomeComponent`独有的。通过传递`createAlertBox`方法，`SomeComponent`是否重新渲染并不重要。就算`message`道具变了也没关系！`createAlertBox`的内存地址不会改变，这意味着`Button`不必重新渲染，您可以节省处理时间并提高应用程序的渲染速度。

但是如果我的函数是动态的呢？

## 修罗(进阶)

***作者注*** :我随口写了下面几个例子，作为在记忆中反复引用同一个函数的方法。这些例子旨在使参考文献的理解变得容易。虽然为了理解参考文献，我建议阅读这一部分，但我在最后提供了一个更好的实现，它是由 Chris Ryan 通过评论慷慨提供的。他的解决方案考虑了缓存失效和 React 的内置内存管理。

有一个非常常见的用例，在单个组件中有许多独特的动态事件侦听器，比如映射数组时。

```
class SomeComponent extends React.PureComponent {
  render() {
    return (
      <ul>
        {this.props.list.map(listItem =>
          <li key={listItem.text}>
            <Button onClick={() => alert(listItem.text)} />
          </li>
        )}
      </ul>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，按钮的数量是可变的，事件侦听器的数量也是可变的，每个事件侦听器都有一个独特的功能，在创建`SomeComponent`时，您不可能知道它是什么。你怎么可能解决这个难题？

输入*记忆化*，或者更容易简称为*缓存*。对于每个唯一值，创建并缓存一个函数；对于将来对该唯一值的所有引用，返回以前缓存的函数。

这就是我如何实现上面的例子。

```
class SomeComponent extends React.PureComponent {

  // Each instance of SomeComponent has a cache of click handlers
  // that are unique to it.
  clickHandlers = {};

  // Generate and/or return a click handler,
  // given a unique identifier.
  getClickHandler(key) {

    // If no click handler exists for this unique identifier, create one.
    if (!Object.prototype.hasOwnProperty.call(this.clickHandlers, key)) {
      this.clickHandlers[key] = () => alert(key);
    }
    return this.clickHandlers[key];
  }

  render() {
    return (
      <ul>
        {this.props.list.map(listItem =>
          <li key={listItem.text}>
            <Button onClick={this.getClickHandler(listItem.text)} />
          </li>
        )}
      </ul>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

数组中的每一项都通过`getClickHandler`方法传递。所述方法将在第一次用一个值调用时，*创建*一个对该值唯一的函数，然后返回它。以后用该值对该方法的所有调用都不会创建新函数；相反，它将返回对内存中先前创建的函数的引用。

因此，重新渲染`SomeComponent`不会导致`Button`重新渲染。类似地，向`list`道具添加项目将为每个按钮动态创建事件监听器。

当它们由多个变量决定时，您可能需要使用自己的聪明才智为每个处理程序生成唯一的标识符，但这并不比简单地为映射结果中的每个 JSX 对象生成唯一的`key` prop 难多少。

使用`index`作为标识符的一个警告:如果列表改变顺序或删除项目，您可能会得到错误的结果。当您的数组从`[ 'soda', 'pizza' ]`更改为仅`[ 'pizza' ]`，并且您已经将您的事件监听器缓存为`listeners[0] = () => alert('soda')`时，您会发现当您的用户单击比萨饼的 now-index-0 按钮时，它会提醒`soda`。这也是 React 建议不要对关键属性使用数组索引的原因。

## 更好的实现

[![](img/5798f2b26e75300bda348c37555a578d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lAEQkRuQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c0snwqrl5r77tcaz4f1j.png)

*由媒体用户克里斯·瑞安提供。*

## 结论

如果你喜欢这篇文章，请随意给它一颗心或一只独角兽。又快又简单，还免费！如果你有任何问题或相关的好建议，请在下面的评论中留下。

要阅读更多我的专栏，你可以在 [LinkedIn](https://www.linkedin.com/in/charles-stover) 、 [Medium](https://medium.com/@Charles_Stover) 和 [Twitter](https://twitter.com/CharlesStover) 上关注我，或者[在 CharlesStover.com](https://charlesstover.com/)上查看我的作品集。