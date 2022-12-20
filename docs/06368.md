# Dino 教程 Deno 入门

> 原文：<https://dev.to/wuz/getting-started-with-deno-e1m>

> Deno 已经到了 1.0 版本，关于这个项目是否需要一个明确的 CoC，已经有了一些大的讨论。最初的维护者似乎认为它没有，在他们使用 Deno 之前，我不能推荐你使用 Deno。
> **行为准则是任何开源项目的必要组成部分。**

如果你错过了，Node 的创建者 Ryan Dahl 的新 Javascript 和 Typescript 运行时已经发布了！它有一些非常酷的功能，并已准备好供公众使用！让我们来看一些简洁的特性，并从一个简单的 hello world 开始！

## 什么是 Deno？

Deno 是主要用 Rust 编写的 Typescript(和 Javascript)的新运行时。它有一些[伟大的目标](https://deno.land/manual.html#goals)和一些非常有趣的“非目标”，比如不使用`npm`和没有 package.json

## 安装完毕

安装 deno 就像运行以下命令一样简单:

`curl -fsSL https://deno.land/x/install/install.sh | sh`

然后复制`export`行，添加到你的`~/bashrc`或`~/bash_profile`中。

打开一个新的终端并运行`deno`。您应该会得到一个`>`提示。键入`exit`，让我们深入了解一些特性！

## Deno 中的炫酷功能

### 默认打字稿

Deno 默认集成运行 Typescript 文件。它基本上使 Javascript 中的类型成为一等公民。不再需要通过 Babel 编译来在服务器端 Javascript 中使用 Typescript。

### 从 URL 导入

Deno 允许您从 web 导入，就像在浏览器中一样。只需在通常命名模块的地方添加一个 URL:

```
import { bgBlue, red, bold } from "https://deno.land/std/colors/mod.ts"; 
```

Enter fullscreen mode Exit fullscreen mode

### 一个标准库

此外，Deno 有一个易于导入和使用的标准库。有一些模块做一些不同的事情，比如 HTTP 处理、日期时间工作和文件系统工作。你可以点击查看[。](https://github.com/denoland/deno_std)

### 使用 es 模块

最后，Deno 只支持 ES 模块语法，也就是说不再有`require()`语句，只有好的 ole' `import x from "y"`。

## 你好世界举例

让我们来看一个快速的 Hello World，它突出了其中的一些功能！

将此复制到一个`hello-world.ts`文件中。

```
import { bgBlue, red, bold } from "https://deno.land/std/colors/mod.ts";

const sayHello = (name: string = "world") => {
  console.log(bgBlue(red(bold(`Hello ${name}!`))));
}

sayHello();

sayHello("Conlin"); 
```

Enter fullscreen mode Exit fullscreen mode

你现在可以用`deno hello-world.ts`运行它，它应该会打印出一些东西。

将其中一个`sayHello`调用更改为`sayHello(15);`并重新运行。您应该会看到一个类型错误，因为 15 不是一个字符串！那很酷！

您还会注意到如何从 URL 导入-它从标准库中获取一些控制台颜色的东西！

# 最后的想法

Deno 还没有完全准备好投入生产使用——有几个[bug](https://deno.land/benchmarks.html#req-per-sec)，但是开发进展很快！这绝对是一个很酷的新开源项目，值得关注！