# JSON 模式棒极了

> 原文：<https://dev.to/nickymeuleman/json-schemas-are-awesome-312m>

## *T3】配置文件 T5】*

那些话让你感到(有点)害怕吗？

您并不孤单，编写配置文件在大多数开发人员最喜欢的消遣列表中排名很低。

当编写这些文件时(例如一个`.eslintrc.json`)，拼写是你最大的敌人之一。

*是`no-unused-vars``noUnusedVars``no-unused-var`还是别的？*

许多小时的开发时间都浪费在拼写上了

记住某个键期望得到的值是这些敌人中的另一个。

*这个是期望一个数组还是一个对象，不记得了*😰

一个会工作，另一个会给你一张去 crypticError 镇的单程票。

由于这些原因(以及更多原因)，在编写配置时打开文档是绝对的*必须*

## 有更好的办法

解决办法？怠惰

我在开玩笑，部分是。解决方案是一个 JSON 模式。利用这些方法之一，您可以依靠智能自动完成和错误检测来完成繁重的工作！

为了以最少的努力开始，您可以使用 [VSCode](https://code.visualstudio.com/) ，它具有对 JSON-schema 的[内置支持。创建一个在 schemastore.org](https://json-schema.org/implementations.html#editors)[列出的文件，并触发编辑器中的自动完成功能(ctrl/cmd + space)](http://schemastore.org/json/)

[![empty .babelrc file](img/3d554e6dbaf4b3b29d531f39a106c564.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V40sPwOg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/96ufgabar7ixnh89lqs0.png)

点击列表中的一个选项`tab`，如果那个键需要一个数组作为值**那么自动得到空的直括号**。
使用您选择的配置文件。如果模式支持的话，嵌套的数量并不重要，你的编辑器会知道什么可以放在那里。

您可以在编辑器的工作空间设置中定义想要使用的确切方案。当我写这篇文章时，我知道你*可以*做到，但不知道*如何*。幸运的是，由 JSON-schema 提供的自动完成功能准确地告诉了我它所期望的。

在 VSCode 工作区设置`.json`文件(ctrl/cmd+shift+p 来搜索)

```
{  "json.schemas":  [  {  "fileMatch":  ["/.babelrc"],  "url":  "http://json.schemastore.org/babelrc"  }  ]  } 
```

## 自己的图式

模式不需要是远程的。您还可以将您选择的文件链接到自己编写的和/或本地的模式。

在这个`json`文件中，我们指向用于验证的模式。

> 许多(但不是所有)编辑器支持这种将文件链接到模式的方式

在`drivers.json`中

```
{  "$schema":  "./drivers.schema.json"  } 
```

`$schema`值指向同一目录中的模式文件

在`drivers.schema.json`中

```
{  "$schema":  "http://json-schema.org/draft-07/schema"  } 
```

这里的`$schema`值定义了正在使用的 JSON-schema 规范的版本。

一个简单的文件很无聊，下面的代码给`driver.json`增加了一些约束

在`drivers.schema.json`中

```
{  "$schema":  "http://json-schema.org/draft-07/schema",  "title":  "Drivers",  "description":  "Formula 1 drivers",  "type":  "object",  "additionalProperties":  false,  "properties":  {  "$schema":  {  "type":  "string"  },  "series":  {  "type":  "string"  },  "season":  {  "type":  "integer"  }  }  } 
```

*   标题和描述用于描述遵循该模式的文件。
*   整个文件的类型应该是`object`(这不是问题，因为它是`json`)
*   只接受我们声明的(顶级)属性/键。
*   这些属性中的每一个都应该是指定的类型。

在这里，我试图将季节定义为`"2018"`

[![incorrect type error](img/000de79e45cec8c50b0eab230c3f4b38.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P1lSUCdt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6c1kwh3xo2r7die93gc6.png)

现在:更复杂的东西。

在`drivers.schema.json`中

```
{  //  ...  "properties":  {  //  ...  "driverList":  {  "type":  "array",  "items":  {  "$ref":  "#/definitions/driver"  }  }  },  "definitions":  {  "driver":  {  "type":  "object",  "additionalProperties":  false,  "properties":  {  "raceNumber":  {  "type":  "integer",  "minimum":  0,  "maximum":  99,  "description":  "number this driver races with"  },  "code":  {  "type":  "string",  "minLength":  3,  "maxLength":  3,  "description":  "3 letter abbreviation"  },  //  ...,  "team":  {  "$ref":  "#/definitions/team"  }  //  ...  },  "required":  ["raceNumber",  "code",  "team"]  },  "team":  {  "enum":  [  //  ...  ]  }  }  } 
```

在这个要点中，整个文件[是可用的](https://gist.github.com/NickyMeuleman/f18b70684e12697d71b4178ad1403988#file-drivers-schema-json)

这是一个满足该模式的文件示例

在`drivers.json`中

```
{  "$schema":  "./drivers.schema.json",  "series":  "f1",  "season":  2018,  "driverList":  [  {  "raceNumber":  14,  "code":  "ALO",  "firstName":  "Fernando",  "lastName":  "Alonso",  "dateOfBirth":  "1981-07-29",  "team":  "MCLAREN",  "quotes":  ["GP2 engine, GP2",  "5 second penalty? Issa yoke"]  },  {  "raceNumber":  3,  "code":  "RIC",  "firstName":  "Daniel",  "lastName":  "Ricciardo",  "dateOfBirth":  "1989-07-01",  "nationality":  "Australian",  "team":  "REDBULL"  }  ]  } 
```

所有信息可在[官方规范文件](https://json-schema.org/specification.html)中找到。

您可以提供的各种选项在[他们的模式验证文档](https://json-schema.org/latest/json-schema-validation.html)中具体列出。

这是一个艰难的阅读。如果您不喜欢，不必担心，因为 JSON-schema 本身具有智能自动完成和错误处理功能！
在处理模式文件时，您在编辑器中获得的信息非常好。

[![autocomplete while writing a schema](img/6db26d58e214be3fe35f5d4e8bf65d90.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iWnSScmZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u68dd7nswf4tp81n2z15.png)

[理解 JSON 模式](https://json-schema.org/understanding-json-schema/)也是获得您正在寻找的信息的一个很好的方法。它目前基于规范的第 6 版，很快会得到更新！

## 不只是 JSON

这些 JSON 模式也可以应用于其他文件类型，比如`YML`

用 Red Hat 安装 [YML 支撑延长件](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-yaml)。

将远程模式链接到文件可以在您的工作区设置中完成。

```
{  "yaml.schemas":  {  "http://json.schemastore.org/prisma":  "prisma.yml"  }  } 
```

现在我的`prisma.yml`文件准备好了！

[![prisma.yml with working schema](img/7fd77a69ac8cdaf33d19d3b1521a1a0e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vJHgeTXm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r3fn4hpk1o49w5sklk3y.png)

我们在`drivers.schema.json`中的顶层`"type": "object"`现在可以是另一个值。
例如`"array"`代表包含一个大的顶级列表的`drivers.yml`。