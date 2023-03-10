# 使用 VS 代码调试 ES6 Mocha 单元测试

> 原文：<https://dev.to/yerac/debugging-es6-mocha-unit-tests-using-vs-code-12ki>

Mocha、VS 代码和 Node 的世界对我来说还是相当新的。通常在过去，我所有的 JS 单元测试都可以在浏览器中使用 DevTools 进行调试，但是使用 Mocha 就不是这样了(因为我没有部署我的 spec 文件)。我让 Mocha 通过启动配置加载，但由于直接使用 ES6，它最初无法工作。

如果您没有 launch.json，请从这里开始。否则跳到下一节。通过选择“调试”，然后选择“添加配置”，添加新的调试配置。选择“Node.js”会自动在名为. vscode 的根文件夹下创建一个“launch.json”。

## 添加摩卡配置到 launch.json

在 launch.json 中，就像令人惊讶的有用的注释建议的那样，您可以简单地键入“Mocha ”,然后按[ctrl]+[space]来显示 Mocha 配置的智能感知！

[![](img/8db0e559e6e50214b16397fd2fc3712a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kzYogq9K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/yer.ac/blog/wp-content/uploads/2019/04/image-1.png%3Ffit%3D700%252C283)

这将插入适当的代码片段。

[![](img/dbfcad37f5d0eb8260860648f431f52b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WZZqvDjK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/yer.ac/blog/wp-content/uploads/2019/04/image-2.png%3Ffit%3D700%252C450)

现在，从理论上来说，只要在 debug 中点击 play 图标，选择“Mocha Tests”就可以了。

[![](img/bcb267b9a5eb3b406915b912797b060d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DY1y9MPu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/yer.ac/blog/wp-content/uploads/2019/04/image-3.png%3Fw%3D840)

## 支持 ES6。

然而对我来说，这个**没有**起作用。

[![](img/5193bd7e3309a119e9d995877b2aeb7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8-uiWHZV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/yer.ac/blog/wp-content/uploads/2019/04/image-4.png%3Ffit%3D700%252C75)

这里的问题是，我得到了很多意外的令牌错误，因为我的测试使用 ES6，我怀疑默认情况下它想要使用 ES5。使用 ES6 进行单元测试的问题在另一篇文章[中解决了。](https://dev.to/wabbbit/attempting-to-use-mocha-chai-to-unit-test-es6-1eo7-temp-slug-4408678)

与我之前的帖子非常相似，我可以更新启动参数以使用 require 来拉入相同的 2 个 Babel 模块，并且还将指定我的测试的通配符文件名，以便它不会拾取任何其他代码。

```
{
            "type": "node",
            "request": "launch",
            "name": "Mocha Tests",
            "program": "${workspaceFolder}/node_modules/mocha/bin/_mocha",
            "args": [
                "./test/**/*.spec.js",
                "--require", "@babel/polyfill",
                "--require", "@babel/register",
                "-u",
                "tdd",
                "--timeout",
                "999999",
                "--colors",           
            ],
            "internalConsoleOptions": "openOnSessionStart"
        } 
```

Enter fullscreen mode Exit fullscreen mode

现在对我来说，这也不起作用，因为我使用 Chai 作为我的 BDD 测试语法。

为此，我必须在参数下将“tdd”改为“bdd”。

现在，只要设置了断点，我就可以附加和调试了！

使用 VS 代码调试 ES6 Mocha 单元测试的帖子[最早出现在](http://yer.ac/blog/2019/04/09/debugging-es6-mocha-unit-tests-using-vs-code/)[yer . AC | Adventures of a developer，and other things。](http://yer.ac/blog)。