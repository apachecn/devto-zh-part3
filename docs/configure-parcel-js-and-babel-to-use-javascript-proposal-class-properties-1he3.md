# 配置宗地 JS 和 Babel 以使用 JavaScript 建议类属性

> 原文：<https://dev.to/kendalmintcode/configure-parcel-js-and-babel-to-use-javascript-proposal-class-properties-1he3>

[![Article hero image including title Babel and Parcel using proposal class properties](img/2830dc79c876e44d8bb13138bf280ec5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HJPdVq8B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://robkendal.co.uk/static/92bbee56921389c1818bf5b9b0c4774c/02744/using-proposal-class-properties-blog-post.png)

继最近关于[使用 Parcel 作为 Webpack 替代品](https://dev.to/kendalmintcode/using-parcel-bundler-as-a-webpack-alternative-503d-temp-slug-9845603)和[使用 React with Parcel JS](https://dev.to/kendalmintcode/using-parcel-js-bundler-with-react-262h-temp-slug-8110950) 的帖子之后，我一直在思考使用现代 JavaScript 开发方法的问题。其中一种方法是使用[闪亮的新类属性提议](https://tc39.github.io/proposal-class-public-fields/)，特别是公共字段声明部分。

如果您最近一直在使用 React，尤其是通过超级有用的 [Create React 应用程序](https://facebook.github.io/create-react-app/)使用新的建议类属性语法几乎是您的第二天性，默认情况下，该应用程序已经安装并打开了它们。

## 阶级属性的美

有很多很好的理由开始使用这些新的类属性和新的初始化器语法，但是主要的两个是为了简化类属性(方法和字段)和`this`的绑定

作为一个例子，这就是当前你如何创建一个 JS 类:

```
class MyClass {
    someFunction() {
        console.log(this.bigNumber);
    }

    constructor() {
        this.someFunction = this.someFunction.bind(this); // bind our method to 'this'
        this.bigNumber = 123456789; // a property
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

不是什么高级课程，也不是特别难看或者难读。然而，想象一下你有很多这样的方法和属性。由于构造函数中大量笨拙的`this`绑定，这个类很快变得难以阅读。

然而，通过使用新的声明语法，您会得到这样的结果:

```
class MyClass {
    bigNumber = 123456789;

    someFunction = () => {
        console.log(this.bigNumber);
    }

    constructor() {
    // other constructor stuff
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上没有太多的东西，但是这个类现在更加自文档化了，仅仅在这个例子中，我们已经完全去掉了对构造函数的需求。

## 现在让我们开始使用建议类属性吧！

如果您已经发现了上一篇关于使用 package bundler 作为 Webpack 替代方案的文章，那么我们已经基本完成了这项工作的设置。你可以[在这里分叉代码库，并使用我们稍后将会谈到的巴别塔插件实现你自己的解决方案](https://codesandbox.io/embed/8z4vzk10p8)。

因为新的职业属性还在提议阶段，我们需要 Babel JS magic 的帮助来开始使用它们。

如果你去官方的巴别塔 JS 插件页面，你可以读到所有的内容。记住这一点，让我们开始一个新的项目。

### 初始设置

我已经使用 Parcel 和 CodeSandbox 上的 Babel plugin-proposal-class-properties 插件创建了一个[全功能解决方案。](https://codesandbox.io/embed/zqjr30zznm)

这是一个简单的项目，涉及 3 个感兴趣的文件:

1.  启动一切的主项目文件
2.  这是我们的 JS 类，使用了新的类属性
3.  启用 Babel 核心功能以及配置类属性转换功能的 Babel JS 配置文件

**注意:** *我们使用的是 [CodeSandbox.io](https://codesandbox.io) 的普通模板，默认包含包裹。如果您在本地机器上从头开始设置，您可能需要添加自己的 Babel 核心依赖项。*

如果我们运行这个项目，我们会遇到这个非常严重的错误:

[![Syntax error showing because Babel JS is not configured correctly](img/943226bf2988b2ad4cffec0e650f3d3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ObkX8UwM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://robkendal.co.uk/static/c6343edcb232aa20fcf9587c7131f7b0/30cfa/screenshot_2019-05-10-codesandbox.png)

呃...

首先，我们需要安装 Babel 插件*[@ Babel/plugin-proposal-class-properties](https://babeljs.io/docs/en/babel-plugin-proposal-class-properties)*，现在就开始:

```
yarn add @babel/plugin-proposal-class-properties --dev 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要在项目根目录下添加一个. babelrc 文件(如果还没有的话),并添加以下内容:

```
{  "presets":  [  "env"  ],  "plugins":  [  "transform-runtime",  "@babel/plugin-proposal-class-properties"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

带有'[@ babel](https://dev.to/babel)/plugin-proposal-class-properties '部分的行是这里的关键，它是我们启用 Babel 插件的地方。

### 处理文件

从`index.js`开始:

```
import "./styles.css";
import ListMaker from "./listmaker";

// let's make some silly lists
const myLists = {
  vegetables: ["cucumber", "turnip", "potato", "carrot", "spinach"],
  dogsNames: ["Rover", "Bosley", "Finn", "Digby", "Doggy McDogface"]
};

// instantiate our ListMaker class
const listMaker = new ListMaker(myLists);

// kick off the list html generation
listMaker.generateLists();

// output the html to the browser
document.getElementById("app").innerHTML = `
<h1>Let's make lists!</h1>
<div> ${listMaker.listsOutput} </div>`; 
```

Enter fullscreen mode Exit fullscreen mode

这里没有太可怕或陌生的东西。我们导入我们的类 listmaker.js，在实例化 listmaker 类和生成我们的列表之前，创建一个包含一些数组的列表对象。我们使用 JavaScript 字符串符号语法将列表的输出添加到页面的最终 HTML 中。

接下来，在 listmaker.js 里面:

```
class ListMaker {
  listHtml = "<div><ul>{0}</ul></div>";
  listItemHtml = "<li>{0}</li>";
  lists = {};
  listsOutput = "";

  constructor(listsObj = {}) {
    this.lists = listsObj;
  }

  makeList = list => {
    let listItemsOutput = "";

    list.forEach(
      listItem =>
        (listItemsOutput += this.listItemHtml.replace("{0}", listItem))
    );

    return this.listHtml.replace("{0}", listItemsOutput);
  };

  generateLists = () => {
    // loop through our list arrays from our initial lists object
    // passed in via props
    Object.entries(this.lists).forEach(([key, value]) => {
      if (Array.isArray(value) && value.length) {
        this.listsOutput += this.makeList(value);
      }
    });
  };
}

export default ListMaker; 
```

Enter fullscreen mode Exit fullscreen mode

马上，在类的顶部，我们定义了一些类变量，比如输出 HTML 和默认列表对象。

当调用构造函数时，我们使用提供的 list 对象更新一个类变量。

回到`index.js`中，我们调用实例化的列表生成器类'`generateLists()`函数，在这里我们使用了另一个很好的 JS 特性，即`Object.entries()`方法。在这里，我们遍历类`lists`属性中的对象，获取每个对象的字符串数组，然后将它们的字符串值填充到将返回给函数调用方的最终 HTML 字符串中。

如您所见，启用了 Babel proposal 类属性转换的输出如下所示:

[![Final list output from out listmaker.js class. It shows two lists built from our arrays](img/6798395c5af863857381a2c9769aa04a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DajVo6nl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://robkendal.co.uk/static/c99bbbae1ca527965f26aae17eeda38b/02744/screenshot_2019-05-10-codesandbox-1-.png)

现在你知道了！背面没有任何花哨的东西，正面也没有任何花哨的东西，但它说明了您的类可以变得多么简单和自文档化。

## 在最终项目中翻找

如果你想看看已经完成的项目作为参考，你可以[在我的 CodeSandbox 账户](https://codesandbox.io/embed/zqjr30zznm)上跳转到这个项目。

我在这里也包括了它:

[https://codesandbox.io/embed/zqjr30zznm](https://codesandbox.io/embed/zqjr30zznm)