# 如何修复 npm 软件包

> 原文：<https://dev.to/archiewald/how-to-fix-an-npm-package-509i>

> 最初发布于 [www.kozubek.dev](https://www.kozubek.dev/2019/02/23/fixing-npm-package.html)

您想要使用的 npm 包可能会有一些错误。你应该等待维护人员来修复它们吗？当然不是😉。可以自己消除 bug 和主机包。然后创建一个拉请求，并有可能成为一个开源贡献者💪。

## bug 发生了🐛

最近，我使用了一个 [react-ga](https://www.npmjs.com/package/react-ga) npm 包将基于 react 的单页应用程序连接到谷歌分析服务。

开始时，我遵循一个通常的程序，包括在`package.json`文件中的`react-ga`。`npm install`顺利，然后上`npm start` :

```
Failed to compile.

/Users/arturkozubek/projects/orbicobeauty-web/node_modules/react-ga/types/index.d.ts
(87,20): A rest parameter must be of an array type. 
```

🙀这是不寻常的...很明显，这个包中有一个与 TypeScript 相关的错误...查看上述`index.d.ts`文件，第 87 行:

```
export function ga(...args: any): void; 
```

嗯，我想我只是按照 TypeScript 编译器的消息，使:

```
export function ga(...args: any[]): void; 
```

所以我将更改保存在本地并进行测试。成功了！但是你不能仅仅依靠修改一个节点模块的源文件...您可能会忽略 repo 中的`node_modules`文件，这是一个很好的惯例。任何时候你(或你的同事)重新安装软件包，它将再次从原始来源下载。

## 分叉并修复🍴

通常(如果不总是，不确定)包源代码是在 github 上提供的。更重要的是，你可以在`package.json`中指向一个 github 库，从那里下载一个包。你能看到它通向哪里吗😉？

1.  我叉开包裹，
2.  克隆到我的机器上，这样我就可以编辑源代码`git clone https://github.com/archiewald/react-ga.git`，
3.  创建修复分支，提交修复，
4.  推到我的分叉回购上。

在`package.json`中，我没有指向版本号，而是指向了我刚刚派生并创建的 repo，使用 fix commit hash 来明确我引用的代码:

```
{  ...  "react-ga":  "git+https://github.com/archiewald/react-ga.git#e9d592e940260017f23815bcee9703a2a4866705",  ...  } 
```

之后，我可以享受实现这个伟大的包！

## 投稿💪

不要害怕与社区分享你的解决方案！提出[一个问题](https://github.com/react-ga/react-ga/issues/342)并用您的更改创建[一个拉动式请求](https://github.com/react-ga/react-ga/pull/343)。一旦你在分叉式回购中实现了它们，事情就简单了！

<figure>

[![pull request](img/af11bbd75b7a4801233089c162d8f7a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fHy9FMuP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kozubek.dev/asseimg/1-pull-request.png)

<figcaption>Fix => contribute</figcaption>

</figure>