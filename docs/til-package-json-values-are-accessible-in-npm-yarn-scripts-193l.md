# TIL: Package.json 值可以在 npm/yarn 脚本中访问

> 原文：<https://dev.to/stefanjudis/til-package-json-values-are-accessible-in-npm-yarn-scripts-193l>

我偶然看到[杰斯·特尔福德](https://twitter.com/JessTelford/status/1108531278846918657)发的这条推文。他分享说你可以在你的 npm/yarn 脚本中引用你的`package.json`中定义的值。

让我们看一个例子。

```
{  "name":  "my-package",  "scripts":  {  "lint":  "eslint ./src/*",  "test":  "jest ./src/*"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

你所看到的是在两个不同的脚本中对`./src/*`的重复定义。对于这两个脚本来说，重复可能没什么大不了的，但是在较大的项目中，可能会困难得多。我从事的项目定义了非常复杂的难以阅读和维护的脚本。在这种情况下，您希望避免任何重复。

[npm](https://docs.npmjs.com/misc/scripts#packagejson-vars) 和 yarn 提供了一个很好的特性来解决这个问题。在`package.json`的`scripts`部分中，您可以引用所有已定义的值。例如，`name`属性在`npm_package_name`可用。这种行为允许您重用值。🎉

```
{  "name":  "my-package",  "config":  {  "src":  "./src/*"  },  "scripts":  {  "lint":  "eslint $npm_package_config_src",  "test":  "jest $npm_package_config_src"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

我认为这是非常好的 npm/yarn，值得了解。

## 我也发布了一个从环境中读取`package.json`值的包！

我继续阅读关于这一发现的 Twitter 帖子， [Jordon Harband 问了几个问题](https://twitter.com/ljharb/status/1108590777930665984)在 Node.js 文件中使用相同的行为是否有意义。我用 [npm-package-to-env](https://www.npmjs.com/package/npm-package-to-env) 进行了一个快速的夜间编码会议，使之成为可能。

假设你有一个`package.json`如下:

```
{  "name":  "my-package",  "config":  {  "foo":  "bar"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以请求`npm-package-to-env`，运行`config`，并通过`process.env`访问这些值。

```
require('npm-package-to-env').config();

console.log(process.env.npm_package_name); // 'my-package'
console.log(process.env.npm_package_config_foo); // 'bar' 
```

Enter fullscreen mode Exit fullscreen mode

`config`命令读出您的`package.json`，并通过`process.env`使数据可用。也许你也觉得那很有用。:)