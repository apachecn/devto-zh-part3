# 为 Plone 附加组件使用 Svelve

> 原文：<https://dev.to/joaomarcusc/using-svelve-for-plone-add-ons-536h>

> Zope/Plone 抨击紧随其后。如果你喜欢 Zope/Plone，没关系，只要跳过第一段，我保证你会读到没有抨击！

我不喜欢 Plone。好吧，其实我不喜欢 Zope/ZODB。它过于复杂，非常低效，难以维护，非常脆弱。Plone 本身就是一个奇迹。背后的人应该被封为圣人，被尊为天才。他们设法用 Zope 的混乱做一些实际上有用的事情。如果我要谈论 Plone，为什么我要抨击 Zope？嗯，我有工作。我的工作不仅仅是使用我喜欢的技术。我的工作是使用我必须使用的技术。

> Zope/Plone 抨击结束，继续！

# 问题

我需要开发一个附加组件，其中包括一个视图，调用内部服务器来搜索特定的结果，并根据上下文以不同的方式呈现它们。除了 UI 之外，没什么特别的，它并不太复杂，但复杂到我不喜欢使用普通的 JS、jQuery 或简单的 ZPT 模板。我第一次尝试 React，因为它 Plone 6 [将为](https://plone.org/roadmap/2019-plone-roadmap)配备基于 [React 的前端](https://github.com/plone/volto)。我设法让它与 Plone 5 一起工作，因为那时我有一个问题:我仍然需要支持 Plone 4，然后我发现我的解决方案太容易出错。

# 一解:苗条

我心想:“我需要的是完全不需要任何外在依赖的东西”。然后我想起读过一些关于 [Svelte](https://svelte.dev/) 的东西，因为，嗯，它有望成为一个“消失”的库，只输出我只需要包含在我的附加组件中的普通 Javascript。有用吗？事实证明确实如此！这里有一个例子:

```
<script>
    export let name;
</script>

<style>
    h1 {
        color: purple;
    }
</style>

<h1>Hello {name}!</h1> 
```

当您构建它时，输出是一个 Javascript 文件，它不需要 Svelte 本身:它是一个自包含的文件，可以很容易地在一个页面中用一个简单的 JS 条目来使用。CSS 也被分割成一个单独的文件。

```
<link rel="stylesheet" href="my-built-app.css" />
<script type="text/javascript" src="my-built-app.js"></script>
<div id="appContainer"></div>
<script>
    // target is the element where the component will be rendered
    // props are the properties that the components need
    const app = new App({
        target: document.getElementById("appContainer"),
        props: {
            name: "Elvis Presley"
        }
    });
</script> 
```

# 动手:在一个附加组件内使用苗条

对于此次实践，您需要:

*   最新的 Node.js 版本。我强烈推荐使用 [NVM](https://github.com/nvm-sh/nvm)
*   包含一个`develop.cfg`配置的 Plone 安装
*   关于如何使用 Mrbob 开发附加组件的知识

## 创建一个样品附加产品

首先，在 plone 实例的`src`目录中使用 mrbob 创建一个示例附加产品:

```
mrbob -O svelte.sample collective.mrbob:addon 
```

然后，将其添加到`develop.cfg`文件:

```
...
[sources]
svelte.sample = fs svelte.sample
...

eggs +=
    ...
    svelte.sample 
```

## 添加初始结构

现在，让我们在`src/svelte/sample/browser/static/sample.js`中添加一个文件，它将输出一些东西到控制台，这样我们就可以知道现在一切正常。我们稍后会用组件替换它:

```
console.log("svelte.sample Javascript included"); 
```

## 添加一个视图

这个苗条的样本将在一个简单的视图中运行，所以，让我们先把它添加到`configure.zcml` :

```
 ...
  <browser:page
      name="svelte-sample"
      for="*"
      permission="zope2.View"
      class=".views.SampleView"
      />
  ... 
```

视图在视图模块中，所以，让我们在`src/svelte/sample/views.py` :
中创建它

```
from Products.Five.browser.pagetemplatefile import ViewPageTemplateFile

class SampleView(BrowserView):
    index = ViewPageTemplateFile('templates/sample.pt')

    def render(self):
        return self.index()

    def __call__(self):
        return self.render() 
```

我们现在需要模板，所以，创建文件`src/svelte/sample/templates/sample.pt` :

```
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:metal="http://xml.zope.org/namespaces/metal"
      xmlns:tal="http://xml.zope.org/namespaces/tal"
     metal:use-macro="context/main_template/macros/master">

<metal:block fill-slot="javascript_head_slot">
  <script type="text/javascript" src="++plone++svelte.sample/sample.js" />
</metal:block> 
<metal:block fill-slot="content-core">
  Nothing here yet
</metal:block> 
</html> 
```

现在我们有了一个可以工作的附加组件。如果您运行 Plone 实例，安装它并打开`@@svelte-sample`视图，它将显示一个包含“这里还没有”的页面，控制台将显示“selvte . sample Javascript included”。附加组件本身不依赖于其他任何东西，但我们显然需要创建苗条的组件。因此，我们需要创建一个苗条的项目。

## 打造苗条项目

创建项目本身很容易。为了这次实践，我们将在我们的附加源代码中创建 JS 项目。在项目的根目录中，运行以下命令:

```
npx degit sveltejs/template component
cd component
npm install 
```

现在，修改文件`component/src/App.svelte` :

```
<script>
    export let name;

    $: uppercaseName = (name || "").toUpperCase();
</script>

<h1>Hello {uppercaseName}!</h1> 
```

在我们构建项目之前，我们需要告诉构建器只导出我们构建的组件，通过修改`component/src/main.js`

```
import App from './App.svelte';

export default {
  App
} 
```

然后，让我们构建项目并复制生成的 bundle.js 文件:

```
npm run build
cp public/bundle.js ../src/svelte/sample/browser/static/sample.js 
```

## 给视图添加苗条的组件

好了，现在当我们将 sample.js 文件包含在 Plone 的视图页面中时，App 组件就可以使用了。将`src/svelte/component/templates/sample.pt`文件更改为:

```
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:metal="http://xml.zope.org/namespaces/metal"
      xmlns:tal="http://xml.zope.org/namespaces/tal"
     metal:use-macro="context/main_template/macros/master">

<metal:block fill-slot="javascript_head_slot">
  <script type="text/javascript" src="++plone++svelte.sample/sample.js" />
</metal:block> 
<metal:block fill-slot="content-core">
  <div id="sampleContainer"></div>
  <script type="text/javascript">
    const sample = new app.App({
      target: document.getElementById("sampleContainer"),
      props: {
        name: "Person"
      }
    });
  </script>
</metal:block>

</html> 
```

现在，如果你打开`@@svelte-sample`视图，它应该显示一个大的`Hello PERSON!`。不需要外部包含。

# 底线

我并不是说你应该对每一个附加产品都使用苗条的身材。如果你的目标是 Plone5+，你可以使用 React。然而，如果你计划支持 Plone 4 和/或不想太依赖特定的依赖项，选择 Svelte 可能是个好主意。请记住，Svelte 所做的是*而不是*在生成的 JS 构建中嵌入其他依赖项。所以，我的建议是，如果你可以没有其他依赖而生活，就选择苗条，而选择对任何其他事情做出反应。