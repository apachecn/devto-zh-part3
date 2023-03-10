# 使用 create-react-app 和 VSCode 的绝对导入

> 原文：<https://dev.to/oliverandrich/absolute-imports-with-create-react-app-and-vscode-ihn>

在我的项目中，我从未想过使用绝对路径来导入模块。但是最近一位新来的同事询问他的项目是否可行。他对这些点和斜线有点恼火。

```
import LoginButton from "../../components/LoginButton". 
```

能够在每个地方使用绝对路径看起来更漂亮，并且使代码的重构也更容易一些。

```
import LoginButton from "components/LoginButton". 
```

我们使用 create-react-app 来启动我们所有的项目。因此，将它添加到项目中很容易。我们只需按照本指南中的步骤，在你的项目根目录下创建一个名为`jsconfig.json`的文件，内容如下。

```
{  "compilerOptions":  {  "baseUrl":  "src"  },  "include":  ["src"]  } 
```

需要做一些额外的工作来配置 VSCode 和 ESLint 以支持它。我们通常使用来自 **eslint-plugin-import** 的标准规则，在编辑代码时添加一些额外的错误检查。

```
{  "extends":  ["react-app",  "plugin:import/errors",  "plugin:import/warnings"],  } 
```

为了支持 VSCode 中的绝对路径，我们必须像这样扩展这个配置。

```
{  "extends":  ["react-app",  "plugin:import/errors",  "plugin:import/warnings"],  "settings":  {  "import/resolver":  {  "node":  {  "moduleDirectory":  ["node_modules",  "src/"]  }  }  }  } 
```

干杯！再见，再见所有这些冗长的点和斜线。