# 如何限制对第三方工具和库的依赖

> 原文：<https://dev.to/joelsaupe/how-to-limit-dependency-on-3rd-party-tools-and-libraries-59np>

NPM 和其他软件包经理使得集成第三方库比以往任何时候都更容易。

需要搭建一个 UI？有一个包可以解决这个问题。

需要格式化时间？有一个包可以解决这个问题。

需要添加认证吗？有一个包可以解决这个问题。

如今，开源代码几乎可以做任何事情。只需点击它，你就可以上路了。很简单，对吧？

但是，当您安装的软件包停止维护或您发现有安全漏洞时，会发生什么呢？当一个更新、更快、更小的软件包发布时，你会怎么做？

很容易允许第三方代码编织并集成到你的应用程序中，只有当你需要替换或删除它时，你才会意识到它已经变得越来越无用了。

在任何长期项目中，重构和改变库最终都是不可避免的。那么，您能做些什么来简化迁移过程呢？

# 减少依赖

为了减少对第三方库的依赖，您可以创建一个访问第三方库的单一入口点。然后，在整个应用程序中，您可以访问本地入口点，而不是直接访问包。当您决定迁移时，您所要做的就是迁移入口点，而不是使用包的每个实例。

以这种方式包装第三方库还可以让您在需要调整时更容易地添加或修改功能。

如果新的库有一个不同的 API，或者如果您一开始就不喜欢这个 API，这就给了您创建自己的自定义 API 的机会，同时获得了使用现有库的所有好处。

## 举个例子

假设我们想要添加一个名为`text-upper`的包，它将文本大写。

我们不是直接访问包，而是首先在比如说`utils/caseChanger`中创建一个入口点。通常最好用通用的或描述性的名称来命名它，而不是仅仅将入口点命名为与包相同的名称。

入口点内部可以是简单的东西:

```
 import TextUpper from 'text-upper'

export default TextUpper 
```

Enter fullscreen mode Exit fullscreen mode

然后，任何时候我们需要大写文本，我们只需通过我们的入口点访问库，就像这样:

```
import caseChanger from './utils/caseChanger'

upperCase.makeUpperCase('hello world') 
```

Enter fullscreen mode Exit fullscreen mode

后来，我们发现了一个更好的包，叫做`textMcChanger`，它不仅使文本大写，还使文本小写。我们*肯定*希望如此。

所以现在我们要做的就是修改我们的入口点来使用这个新的包。即使新的包有一个稍微不同的 API 来大写文本，我们只需要修改我们的入口点，以确保我们已经大写文本的地方也不需要重构。

我们在`utils/caseChanger`中的迁移可能会像这样结束:

```
import betterPackage from 'textMcChanger'

// Modify API for backwards compatibility
betterPackage.makeUpperCase = text => betterPackage.upper(text, true)

// Modify API for consistency with old API
betterPackage.makeLowerCase = text => betterPackage.lower(text, true)

export default betterPackage 
```

Enter fullscreen mode Exit fullscreen mode

很简单，对吧？我们所要做的就是改变这一个文件，我们的应用程序立即获得新包的好处，而不需要所有额外的重构。

# 结论

显然这样包装每一个包裹是没有意义的。试图为 React 添加一个包装器作为你的 UI 库，然后试图将 Vue 转换成 React 那样工作，这是很愚蠢的。但是我想你可以！

对于大多数其他事情，这是一个很好的实践，一旦你找到了完美的替代者，你会感谢自己，你不必害怕迁移。