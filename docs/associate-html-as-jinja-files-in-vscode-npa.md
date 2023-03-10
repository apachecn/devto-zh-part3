# 在 VSCode 中将 HTML 关联为 Jinja 文件

> 原文：<https://dev.to/wrrnwng/associate-html-as-jinja-files-in-vscode-npa>

Visual Studio 代码可以为你的 Jinja 模板提供语法高亮，即使在 Flask 项目中 Jinja 文件通常以扩展名`*.html`命名。

我假设您已经为 VSCode 安装了 [Python 扩展](https://marketplace.visualstudio.com/itemdetails?itemName=ms-python.python)。

从下拉菜单中打开您的`Preferences > Settings`或者您当前项目的`.vscode`目录中的`settings.json`文件，添加:

```
{  "files.associations":  {  "*.html":  "jinja-html"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

我不会把它放在用户`settings.json`文件中，因为这将全局关联所有`*.html`文件和`jinja-html`。