# Node.js 中带有 TypeScript 的路径别名

> 原文：<https://dev.to/larswaechter/path-aliases-with-typescript-in-nodejs-4353>

这篇文章最初发表在我的博客上。

* * *

几天前，我在我的 TypeScript Node.js 项目中包含了路径别名。因为在我看来，它们使代码看起来更整洁，所以我想向你展示如何在项目中设置这些。

## 问题

在 Node.js(或一般的 TS/JS)中，您可以将单个模块导入到代码中。
这可能看起来如下:

```
import { User } from '../../user/model';
import { Article } from '../../article/model';

import { Cache } from '../../../../cache';
import { MongoDB } from '../../../../mongodb'; 
```

Enter fullscreen mode Exit fullscreen mode

注意到这些点(’../')来访问上层模块？

我们这里的问题是，你的项目树越深，你就越../'是访问更高层模块所必需的。实际上，说实话，这看起来并不漂亮。幸运的是，我们可以改变这种情况。

解决方案:**路径别名**

## 什么是路径别名？

在 TypeScript 中，您可以借助路径别名来避免这些看起来“糟糕”的导入。使用路径别名，您可以声明映射到应用程序中某个绝对路径的别名。

这里举个简单的例子:

```
import { User } from '@modules/user/model';
import { Article } from '@modules/article/model';

import { Cache } from '@services/cache';
import { MongoDB } from '@services/mongodb'; 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们的两个别名是

*   @映射到'的模块。/src/rest/modules '
*   @映射到的服务。/src/services '

## 设置

让我们深入了解一下，并设置一些路径别名。注意，我不会解释如何在 Node.js 中设置一个 TypeScript 项目，我假设您已经这样做了。

假设我们有如下的项目结构:

```
folder structure
└───src
   │
   └───rest
   │   │
   │   └───modules
   │   │   │
   │   │   └───article
   │   │   │
   │   │   └───user
   │   │
   │   │   server.ts
   │
   │
   └───services
   │   │    cache.ts
   │   │    mongodb.ts
   │    
   │   index.ts 
```

Enter fullscreen mode Exit fullscreen mode

### 第一步:更新 tsconfig.json

首先，我们必须在 tsconfig 文件
中声明路径别名

```
"baseUrl":  "./src",  "paths":  {  "@modules/*":  ["rest/modules/*"],  "@services/*":  ["services/*"]  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以在应用程序中为模块导入使用新的路径别名。在您的 IDE 中(在我的例子中是 VSC)或者在您编译代码时出现任何错误。

然而，我们还没有完成。当你试着把 TS 代码编译成 JS 时，你不会看到任何错误。但是，一旦运行编译后的 JS 代码，就会出现一个错误:

例如:

```
Error: Cannot find module '@modules/user' 
```

Enter fullscreen mode Exit fullscreen mode

这是因为 JS 不能为声明的路径别名解析模块。

### 第二步:安装模块别名包

接下来，我们将安装一个名为[模块的 npm 包，别名](https://www.npmjs.com/package/module-alias)

```
npm i --save module-alias 
```

Enter fullscreen mode Exit fullscreen mode

这个模块在编译的 JS 文件中注册路径别名。因此我们需要对我们的包做一些修改

```
"_moduleAliases":  {  "@modules":  "dist/rest/modules",  "@services":  "dist/services"  } 
```

Enter fullscreen mode Exit fullscreen mode

注意‘dist’是编译后的 JS 文件所在的文件夹。

最后但同样重要的是，我们必须在应用程序中注册路径别名。在启动文件的顶部添加下面一行:

```
import 'module-alias/register'; 
```

Enter fullscreen mode Exit fullscreen mode

最后，当您编译和执行代码时，您不应该看到任何导入错误。

在这里你可以在我目前正在做的一个项目中找到一些路径别名的例子。