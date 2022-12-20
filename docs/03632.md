# 辅助项目更新#1

> 原文：<https://dev.to/joro550/side-project-update-1-4o2a>

# 开始我的项目

所以我不得不做了很多研究才走到这一步，但是感觉真好！让我带你看一下到目前为止我已经完成的一些决策和代码。

## 功能设置(此时)

*   我能做一张桌子
*   可以制作分页控件
*   可以替换数据
*   可以传入数据数组

## 我下周的重点是什么？

*   改进分页控制
    *   现在，它将只打印每个数字(如果有 100 页，它将呈现 100 个按钮)你想要的是分页控件，做 1，2，3....100 和 1，10，11，12...100
*   实现获取远程信息的方法

## 来看看一些代码:)

首先，我想谈谈我的期权对象，我知道这听起来很无聊，但让我解释一下:

```
export interface IDataGridOptions {
    dataSource: Array<object>;
    paging?: PagingOptions;
    columns? : Array<string>
}

export interface PagingOptions {
    pageSize: number;
} 
```

在这里，您可以看到我的一组选项，您可以在创建数据网格时将它们传递到数据网格中，这带来的第一个问题是，您可能会有空对象，这意味着您必须不断地检查空值。第二是有些期权依赖于期权的其他部分。让我解释一下我的想法:

### 空值

所以我想做的是有一个类，它有相同的接口，但是对象不能为空，我可以用接口来填充对象。这样我就不用满店检查 null 了！

我不知道你是否知道，但是一个接口不需要在 typescript 中有一个声明！你可以创建一个动态对象来表示接口，举个例子我想时间:

界面:

```
export interface IDataGridOptions {
    dataSource: Array<object>;
    paging?: PagingOptions;
    columns? : Array<string>
}

export interface PagingOptions {
    pageSize: number;
} 
```

我可以创建一个函数，将该接口作为参数:

```
export function myFunction(options : IDataGridOptions) : void {
} 
```

现在，如果我使用 c#这样的语言，我必须创建一个继承自该接口的类来调用该方法，但是在 typescript 中，你可以创建一个表示形状的对象:

```
myFunction({
    dataSource : [],
    paging: {
    },
    columns : []
}); 
```

这是完全有效的代码！所以我不需要处理代码中的空值，我认为有一个这样的类是明智的:

```
 export class DataGridOptions {
    public dataSource : Array<object>;
    public columns : Array<string>;
    public paging : PagingOptions;

    constructor(){
        this.dataSource = new Array<object>();
        this.columns = new Array<string>();
        this.paging = {
            pageSize: 10
        }
    }

    public setColumns(columns : Array<string>) : DataGridOptions {
        this.columns = columns;
        return this;
    }

    public setPaging(paging:PagingOptions) : DataGridOptions {
        this.paging= paging;
        return this;
    }

    public setDataSource(dataSource: Array<object>) : DataGridOptions{
        this.dataSource = dataSource;
        return this;
    }
} 
```

这与接口基本相同，但它没有扩展它，这是为什么呢？因为接口里可以为空的东西在这里不能为空！一旦我将接口映射到类，我就不用再担心空值了，万岁！

所以问题变成了我们如何将一个映射到另一个？

好的，我们有很多方法可以做到这一点，我们可以在代码中使用 if 语句，这将是完全有效的，我开始是这样做的，但它很快变成了 if 语句的迷宫，变得很混乱。所以我转向设计模式

#### 复合设计模式

我决定使用复合设计模式，如果你认为每个对象是一个分支或一片叶子，那种味道就是你必须不断地传递你正在创建的选项和你正在复制的选项，但它确实击败了一大块 If 语句，再次举例:

```
 abstract class Component {
        public add(component: Component): void {
        }

        abstract operation(options: IDataGridOptions, createdOptions: DataGridOptions): DataGridOptions;
    } 
```

这些都是不好的名字我只是还没有想到更好的，也许是 mapper 和 map？我会回来的，坚持，因为这是我的基类！

```
class DefaultOptionsComponent extends Component {
        protected children: Component[] = [];

        public add(component: Component): DefaultOptionsComponent {
            this.children.push(component);
            return this;
        }

        public buildOptions(options: IDataGridOptions): DataGridOptions {
            let dataGridOptions = new DataGridOptions();
            return this.operation(options, dataGridOptions);
        }

        operation(options: IDataGridOptions, createdOptions: DataGridOptions): DataGridOptions {
            for (let i = 0; i < this.children.length; i++) {
                createdOptions = this.children[i].operation(options, createdOptions);
            }
            return createdOptions;
        }
    } 
```

我有点想让`operation`受到保护，这样人们就不会在我们的类之外意外地调用它，但这意味着我不能在我的组件中调用它，这太令人失望了。但无论如何，我希望人们在这里调用`buildOptions`，我创建了一个空的 DataGridOptions 类，然后开始在类的子类之间传递它(我保证我们会用到它)。

```
class DefaultColumnsComponent extends Component {
        operation(options: IDataGridOptions, createdOptions: DataGridOptions): DataGridOptions {
            let columns = new Array<string>();
            if (options.columns == null || options.columns.length == 0) {
                columns = new Array<string>();
                for (const property in options.dataSource[0]) {
                    columns.push(property);
                }
            } else {
                columns = options.columns
            }
            return createdOptions.setColumns(columns);
        }
    } 
```

我的一个复合类的一个简单的例子，这是 columns builder - fi 你没有指定任何你想要呈现的列，那么我需要从其他地方获取它们，其他地方显然是数据源，我从属性中获取键，将它们推到我的数组中，然后在我创建的选项上设置它们。

我听到你问

```
export class DefaultOptionsDirector {
        public static build(options: IDataGridOptions): DataGridOptions {
            const tree = new DefaultOptionsComponent()
                .add(new DataSourceComponent())
                .add(new DefaultColumnsComponent())
                .add(new DefaultPagingComponent());
            return tree.buildOptions(options);
        }
    } 
```

这是很好的一点，你有一个导演，你建立了一个组件树。

*我不知道我以后会不会后悔，但现在这似乎是最干净的方式，如果这会引起问题，我可能会重新考虑这个问题*

### 依赖问题？

您可以在这里看到，列的默认选项依赖于您从数据选项传入的对象，我认为这可能会在我进行远程操作时给我带来问题，当我实现请求/响应时，我无法从数组中准确地获取它们。

我现在想的是，DataSourceComponent 将从接口获取数据源，并创建一个公共对象供我以后使用，如果我是一个数组，那么我可以简单地在内存中进行一些欺骗，如果是一个 web 调用，事情可能会变得有点糟糕，特别是分页是一个后来的组件，我可能必须处理设置的顺序

*另一件我可能会后悔的事*

## 你还做过什么？

天哪，这还不够吗！？我想我还想谈两件事，一件我犯的小错误和一件很酷的事

### 你说失误？

啊，是的，在项目的早期，我决定依赖把手，很好的选择，我把初始模板、标题、数据和页面的所有部分都分开了。将页面作为模板的一部分是我的错误，因为我必须向页面按钮添加点击动作——这意味着我必须创建 html，然后找到它的特定部分(按钮),然后添加点击按钮， 这感觉很不对劲，所以我很早就决定在源代码中删除它，让它认为页面不是模板的一部分，并且简单地在元素上添加节点，一旦初始的数据网格被放入 html，这看起来工作得非常好！

### 那很酷的东西呢？

是的，我对这个项目的一个主要想法是，我不希望人们依赖于多个 js 文件或任何东西，所以将它构建并最小化为一个文件一直是这个项目的目标，我只是不知道它何时会成为一件事，因为我快速浏览了一下，似乎你可以通过 typescript 并在`.tsconfig`中进行一些设置来实现它。遗憾的是，将`outFile`设置添加到 tsconfig 意味着您必须编辑`module`设置，要么将其设置为`system`或其他值。但是这两个设置立刻给我带来了一堆问题。我决定把它作为一个“实现细节”在适当的时候解决(我使用 glo 板来跟踪这样的事情，所以我知道我不会忘记)。

但是后来我想起很久以前有人跟我谈过 webpack，所以我决定去看看，我设法创建了一个非常简约的配置文件:

```
const path = require('path');

module.exports = {
    entry: './src/BearBones.ts',
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                use: 'ts-loader',
                exclude: /node_modules/
            }
        ]
    },
    resolve: {
        extensions: [ '.tsx', '.ts', '.js' ],
        alias: {
            'ejs': 'ejs.min.js'
        }
    },
    output: {
        filename: 'bear-bones.js',
        path: path.resolve(__dirname, 'dist')
    },
    node: {
        fs: "empty"
    }
}; 
```

更改了我的 package.json，现在它可以通过 webpack 编译:

```
{
  "name": "js",
  "version": "1.0.0",
  "description": "",
  "main": "/src/BareBones.ts",
  "scripts": {
    "build": "webpack",
...
  },
  "author": "Mark Davies",
  "license": "MIT",
  "dependencies": {
    "handlebars": "^4.1.2"
  },
  "devDependencies": {
...
  }
} 
```

现在它将自己最小化到我的。/dist 文件夹:D -非常高兴这个！

## 有什么问题需要解决吗？

很多！我认为现在最大的问题是，当我运行 npm 测试时，它失败了，好消息是 web storm 允许我单独或作为一个套件运行测试，所以我一直靠它生活，但我认为如果我想让人们在项目上工作，我需要让它工作！

# 直到下一次

希望我会继续努力，通过这些帖子让人们了解这个项目的最新进展，如果你有任何关于如何使这些更好的建议，请留下评论，我会尝试将它纳入下一组博客帖子中。

点击这里查看项目:[https://github.com/joro550/bear-bones/tree/dev](https://github.com/joro550/bear-bones/tree/dev)