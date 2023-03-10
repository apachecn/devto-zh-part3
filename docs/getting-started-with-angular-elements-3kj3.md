# 角度元素入门

> 原文：<https://dev.to/prestonjlamb/getting-started-with-angular-elements-3kj3>

你做过非棱角的项目吗？它可能是一个 Wordpress 主题，一个 JAM stack 项目(就像这个网站！)，或者一个. NET 核心 app 等等。现在，假设你正在开发一个应用程序，但是需要一些前端功能。你希望它像 Angular 一样工作，但你实际上无法编写 Angular 应用程序(出于这样或那样的原因)。我最近就是这种情况，被指着[角元素](https://angular.io/guide/elements)的方向。

> Angular Elements 提供了一种用 Angular 编写定制元素的方法，可以在任何站点上编译和使用。如果你想了解更多关于定制元素的信息，请访问这个网站。本质上，它是一种定义新 HTML 元素的方法，可以像任何原生 HTML 元素一样使用。

所以我们来看看如何入门角元素！

让我们从一个基本的 header 组件开始，无论您在哪里使用它，它都会显示一条静态消息。也许是一个公司的标志，或者是你不想一遍又一遍重复代码的东西。在您的 Angular 应用程序中，使用 CLI 生成一个组件；`ng g c custom-header`会的。为了便于演示，假设模板很简单，如下:

```
<h1>My Company Header</h1> 
```

同样，这只是一些简单的东西，但我们不想在我们所有不同的网站上重复。那么，我们如何在非角度应用中使用它呢？真的挺简单的！

有 5 个步骤:安装`@angular/elements`包，更改 tsconfig.json 文件中的目标，在`entryComponents`数组中列出您想要的组件并在`ngModule`中注册定制元素，构建项目以获得所需的脚本，最后将它包含在您的非 Angular 项目中。

## 步骤 1 —安装`@angular/elements`

事实证明这一步非常简单。从在终端中运行`ng add @angular/elements`开始。这将为您完成大部分工作。角度 CLI 和示意图是令人难以置信的。从这个意义上来说，Angular 和 CLI 团队是不可思议的。

## 第 2 步—编辑 tsconfig.json

为了使构建能够工作并允许您在另一个项目中使用您的定制元素，您需要更改 tsconfig.json 文件中的 target 属性。创建项目时的默认值是“es5”；将其更改为“es2015 ”,您就可以开始了。

## 第三步— `ngModule`工作

在安装`@angular/elements`并构建您的组件之后，大部分工作都在`ngModule`中完成。第一部分在装饰器中。在装饰器中，添加一个名为`entryComponents`的数组，并将上面的组件(`CompanyHeaderComponent`)添加到该数组:

```
@ngModule({
    ...
    // Make sure to remove the bootstrap attribute
    entryComponents: [CompanyHeaderComponent],
})
export class AppModule {} 
```

这一步的下一部分是在`AppModule`的构造函数中将 Angular 元素注册为自定义元素。使用`@angular/elements package:`
中的函数非常简单

```
export class AppModule {
    constructor(private injector: Injector) {
        const companyHeader = createCustomElement(CompanyHeaderComponent, { injector });

        customElements.define('custom-header', customHeader);
    }

    // This is required so that the component will bootstrap in the project where it's included
    ngDoBootstrap() {}
} 
```

这就是全部内容:将组件添加到头部，定义自定义元素，调用 ngDoBootstrap() {}方法。就这么简单。

## 步骤 4 —构建项目

下一步是构建应用程序并获得结果脚本，以便它可以包含在您的外部项目中。这里有一个您可以使用的构建脚本，但是一定要将`project-name`替换为您的 angular 项目的名称，并将`../path/to/output/`替换为您的磁盘上应该保存输出文件的位置。脚本如下:

```
ng build --prod --output-hashing=none && cat dist/project-name/runtime.js dist/project-name/polyfills.js dist/project-name/scripts.js dist/project-name/main.js > ../path/to/output/ngelements.js 
```

bash 脚本构建项目，从输出中获取所需的文件，并将它们连接成一个文件，然后将它放在您选择的位置。我要指出的是，按照上面的顺序连接是很重要的。如果你不按顺序做，剧本就不会起作用。我是吃了苦头才知道的。但就是这样！现在，您可以在外部项目中使用它了。

## 步骤 5 —实现您的定制元素

要实现定制元素，请将第 3 步中的脚本输出包含在将要使用它的项目的 HTML 文件中。然后，在 HTML 中，以如下方式使用它:

```
<custom-header></custom-header> 
```

很神奇，但这就是全部。这样，您的自定义标题现在将包含在页面上！我知道这听起来好得不像是真的，但这真的就够了。

## 更多选项

静态头很好，但是我们真的需要更多的灵活性。在 Angular 中，这包括`@Input()` s 和`@Output()` s。在您的组件中，您可以像平常一样使用它们。然后注册定制元素，并像上面的第二步和第三步一样构建项目。将结果脚本包含在您的外部项目中，您就可以开始了。

现在，让我们假设您已经在`CustomHeaderComponent`上提供了一个名为`companyName`的`@Input()`。当你把它放到 HTML 中时，它看起来像这样:

```
<custom-header company-name="My Company Name"></custom-header> 
```

您在`company-name`属性中输入值将输出到您在组件中选择的任何位置。

s 也很容易处理。在新的脚本中，您需要获得对`custom-header`元素的引用，并添加一个与`@Output()`同名的事件监听器。然后，当它被触发时，您将能够访问该事件的数据。这里有一个例子:

```
const customHeaderEl = document.querySelector('custom-header');
customHeaderEl.addEventListener('myCustomOutput'. (evt) => {
    console.log(evt);
}); 
```

再说一次，我知道这看起来太简单了，不可能是真的，但它真的很容易。当我学习这个的时候，我惊讶于它是如此的简单。

## 结论和下一步

我真的相信这是 web 开发的未来。构建定制元素并分发它们，供任何平台上的任何人使用，无论他们身在何处。Angular 让这一切变得如此简单。在熟悉的环境中开发它们，轻松构建它们，并轻松实现它们。

我的下一步是弄清楚如何将[内容投影](https://dev.to/blog/angular-template-refs)与定制元素结合使用。自定义元素很棒，但是构建组件的开发人员将决定 UI。如果他们能提供 UI 而你提供功能就好了。这就是内容投影的用途，所以如果我们可以在这里使用它，那就太好了。如果你已经这样做了，请联系我！也让我知道你是否在你的任何项目中使用了自定义元素！