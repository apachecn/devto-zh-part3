# 面向角度应用的前端架构

> 原文：<https://dev.to/cavezza/front-end-architecture-for-angular-applications-g4i>

我目前在 FINCURA 担任产品工程总监，我经常思考我们的前端架构。对于我们的前端应用，我们使用 Angular。

角度架构归结为三个主要角色:服务、容器组件和表示组件。

[![](img/04cb9ad9a8cb81378ca5d1273135bfde.png)](https://ibuildmvps.com////wp-content/uploads/2019/05/Screen-Shot-2019-05-06-at-5.37.42-PM.png)

我将分别解释其中的每一部分，但为了快速了解发生了什么，请看下图:

[![](img/e76de4fb5b41d57aa0290a55b71b2083.png)](https://ibuildmvps.com////wp-content/uploads/2019/05/angular-component-based-architecture.jpg)

[图片由克劳多·克拉维霍从他在[https://www.slideshare.net/paucls/angular-and-redux](https://www.slideshare.net/paucls/angular-and-redux)的幻灯片中拍摄]

**服务**

服务将为您的应用程序完成繁重的工作。它们应该包含应用程序中的所有业务逻辑，因为业务逻辑通常会在系统范围内使用。为了确保最大的可重用性，请确保您可以轻松地访问业务规则。Angular 对服务的依赖注入使得在站点范围内重用服务方法变得很简单。

如果您没有使用 Redux，那么您会希望将系统范围的状态存储在服务中。一个常见的例子是将当前登录的用户作为变量保存在`UserService`中。另一种选择是将公共状态存储在作为主父级的系统范围的容器组件中。然而，如果你这样做，在你的应用程序中会有这样的情况，你在很多地方向下传递一个`user`变量。你可以预期大多数组件在不同的时间需要`user`变量。我称之为“Input() Hell ”,它与“callback”Hell 没有什么不同。

服务没有任何可视化输出。它们为组件处理数据，然后组件成为显示信息的基础结构的一部分。

根据 [Angular 的官方文档](https://angular.io/guide/testing#service-tests)，“([服务是最容易进行单元测试的文件](https://angular.io/guide/testing#testing-services-with-the-testbed)”。通常，当您测试服务时，您不需要担心有角度的测试床。服务只不过是普通的 javascript 类。我测试服务的首选方法是实例化服务类，用 jasmine 的“createSpyObj”方法模拟它们的依赖关系。一旦我这样做了，我就可以像处理一个普通的 javascript 类一样编写测试了。这里有一个我几周前写的测试的简单例子:

```
describe('PortfolioService', () =\> {
  let portfolioService: PortfolioService
  const apiServiceSpy = jasmine.createSpyObj('ApiService', {'send': of( {   response: { objects: [] }} ) } );

  beforeEach(() =\> {
    portfolioService = new PortfolioService(apiServiceSpy);
  });

  describe('#cssColorNumber', () =\> {
    beforeEach(() =\> {
      const portfolios = [
        { name: 'Portfolio1', id: 0 },
        { name: 'Portfolio2', id: 1 },
        { name: 'Portfolio3', id: 2 },
        { name: 'Portfolio4', id: 13 },
        { name: 'Portfolio5', id: 14 },
      ]; 
      portfolioService.portfolios = portfolios;
    }); 

    it('returns id index +1 of id found', () =\> {
      const expectedResult = 5;
      const result = portfolioService.cssColorNumber({name: 'Portfolio5', id: 14});
      expect(result).toEqual(expectedResult);
    }); 
  });
}); 
```

在上面的测试中，我们测试了一个方法的输出，该方法返回通过 id 在数组中找到的对象的索引+1。在每次测试之前，我们用一个模拟的依赖注入来实例化服务类。我们将样本数据应用于我们正在测试的服务。然后，我们可以用一个简单的期望来测试它。不需要测试平台，也不需要测试平台通常附带的复杂设置。

总之，服务应该包含应用程序的所有业务逻辑，包含应用程序不同部分的共享状态，执行所有 api 调用，并且没有可视输出。它们也比表示组件更容易测试。

**容器组件**

容器组件之所以得名，是因为它们包含表示组件，并且总是将数据向下传递到这些表示组件层。它们有时被称为智能组件。在许多情况下，您将拥有嵌套的容器组件，这些组件将数据向下传递给其他容器，这些容器最终将数据向下传递给表示组件。

如果没有例子，很难真正理解这是什么样子，所以让我们看看它在实践中是如何工作的。

来看看我的 LinkedIn 主页。这是我登录 LinkedIn 时看到的。

[![](img/a0522900fb93a2fd7a3c371274d47e87.png)](https://ibuildmvps.com/wp-content/uploads/2019/08/linkedin_homepage_for_angular_post-1024x511.png) 【点击图像放大】

基于我对 LinkedIn 的容器组件和表示组件(在其前端架构中)的假设，这里有一个尝试来打破这一点。注意:我模拟容器组件和表示组件的大部分能力来自于阅读 React 中的[思维。](https://reactjs.org/docs/thinking-in-react.html)

[![](img/677cdd8c41c9c16ffe933c95909aba7d.png)](https://ibuildmvps.com////wp-content/uploads/2019/05/Screen-Shot-2019-05-06-at-6.37.27-PM.png)

不同的颜色代表容器组件的层次结构。本例中绘制组件的主要焦点是左侧导航栏及其所有父组件(也是容器组件)。整个页面被红线包围。在应用程序中，这通常被称为“应用程序组件”。层次结构的下一个黄色层将顶部导航栏与主包装组件分开，主包装组件将代表页面的主体。粉色层是侧边栏的容器。在那之后，橘子打破了每一个明显分开的面板。然后，面板被进一步细分为单独的子组件。红色、黄色、紫色和橙色都是容器组件。一旦我们克服了这一点，它就会变得更加模糊，这取决于应用程序到底在做什么，以及 LinkedIn 是如何构建其表示组件的。一旦进入绿色层，您现在看到的是表示组件。根据外观的相似程度，很容易判断顶部的两个绿色矩形和底部的两个绿色矩形可能是重用的表示组件。

在我们的角度应用中，我们利用了 React 著名的技术“提升状态”([https://reactjs.org/docs/lifting-state-up.html)](https://reactjs.org/docs/lifting-state-up.html)。这意味着我们将许多不同组件使用的状态存储在它们的最低共享父组件中。

让我们在这个例子中也使用 LinkedIn 的主页。在我们当前的工作示例中，页面上有一个用户头像的实例。一位产品经理找到我们，告诉我们她想在包含用户头像的顶部导航组件中添加一个新项目。她给我们的模型是这样的:

[![](img/e8087f2e8c2a5808818d19aa14be2f57.png)](https://ibuildmvps.com/wp-content/uploads/2019/05/mockup_pm_gave_us.png)

我们的总理给我们的模型。点击放大]

如果我们仔细观察用组件结构标记的页面，我们可以看到页面上已经有了头像。一种选择是作为导航组件的一部分再次调用我们的用户服务，但是 http 调用是昂贵的。相反，我们希望将存储用户头像信息的地方移到侧面导航栏和顶部导航标题的公共父级。这个共同的父组件就是我们用红色轮廓突出显示的组件——“app-component”。

如果我们当前在橙色面板容器组件中调用 UserService.getUser 方法，我们现在将希望在 app 组件(红色包装)中调用该方法。然后，我们将把头像 url 作为“@Input()”向下传递到黄色，向下传递到栗色，然后向下传递到橙色，这是它开始存在的地方。我们还将把相同的值传递给黄色的导航组件及其子组件，新的 html 元素将驻留在这些子组件中，并在导航栏上显示头像。

容器并不为可视组件做很多繁重的工作，但是它们确实有一个关键的表示责任。这个责任就是放置表示组件。一个好的经验法则是，你不希望表示组件有空白 css 属性。表示组件的定位通常应该由它们的父容器组件控制。

测试容器组件通常是我们在本文中讨论的三种文件类型中最难的。其主要原因是状态的处理。需要测试状态变化和角度生命周期方法。这增加了测试的复杂性。将容器组件和表示组件分开的一个关键原因是，您可以少测试容器组件，多测试表示组件。

**演示组件**

表示组件有时被称为“哑”组件，主要用于显示信息。演示组件将从父组件继承所有数据和事件(@Input()s 和@Output()s)。它们完全是视觉输出组件。

继续我们的 LinkedIn 示例，我们可以看到一个用绿色包装的演示:

[![](img/677cdd8c41c9c16ffe933c95909aba7d.png)](https://ibuildmvps.com////wp-content/uploads/2019/05/Screen-Shot-2019-05-06-at-6.37.27-PM.png)

这个表示组件在这个页面的两个不同的地方使用了四次。您可以合理地猜测这个表示组件的输入和输出是什么。

[![](img/0f8f8ed61c5c2991a6d88239007ed417.png)](https://ibuildmvps.com/wp-content/uploads/2019/05/who_viewed_your_profile.png)

这个组件的输入和输出可能看起来像这样:

```
@Input() label  
@Input() value
@Output() onClickLabel
@Output() onClickValue 
```

这些输入和输出由它们的父母传入。

为什么要将容器和表示组件分开？

原因有三:(1)可重用性，(2)可测试性，(3)HTML/CSS 设计者的易用性。

*(1)复用性*

如果您将组件分解成越来越小的部分，您将能够在代码的不同区域重用不同的组件。你可以在 LinkedIn 的例子中看到这一点，工具条上有绿色的轮廓。这都是关于“干”(不要重复自己)的原则。如果您没有将这些绿色轮廓分解成表示组件，您将会对相同的内容进行两次编码。如果您后来得到指示要更改那里的某些内容，您将不得不在多个地方进行更改。这样做通常会导致 bug。

*(2)测试性*

表示组件比容器组件更容易测试。当您将状态和生命周期方法作为关注点移除时，测试就容易多了。是的，您确实需要测试容器组件。这是编写好的、可维护的代码的一部分。然而，在表示组件中你能做的测试越多，你的情况就越好。

*(3) HTML/CSS 开发者*

许多软件团队拥有不同程度的技能。团队中有人非常了解 HTML 和 CSS，但对 javascript 知之甚少，这种情况并不少见。当你分离表示组件和容器组件时，HTML/CSS 专家现在可以更容易地调整与设计相关的代码。使用像 [Storybook](https://storybook.js.org/) 这样的工具可以让 HTML/CSS 开发者控制设计元素。

**结论**

这种架构对我们来说效果很好。将逻辑分成服务、容器组件和表示组件使得测试更加容易。拥有一个经过良好测试的代码库让我们拥有一个更加稳定的应用程序。

将组件分解成表示组件增加了我们代码的可重用性。代码可重用性使我们能够更快地交付特性，并引入更少的错误。

**阅读更多尽在我的博客**【http://ibuildmvps.com】

**接我**
[http://twitter.com/cavezza](http://twitter.com/cavezza)
[http://linkedin.com/in/cavezza](http://linkedin.com/in/cavezza)