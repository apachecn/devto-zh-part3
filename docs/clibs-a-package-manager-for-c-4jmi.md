# CLibs:C 语言的包管理器

> 原文：<https://dev.to/noah11012/clibs-a-package-manager-for-c-4jmi>

许多现代语言，如 Python 和 Rust，都有自己的标准包管理器，帮助安装项目的依赖项。c 从来没有过...直到现在。

CLibs 是一个面向 C 的包管理器，它专注于“微”库。这意味着您可以用 CLibs 安装的所有库都是轻量级的、快速的，并且在您的整个项目中占用的空间很小。

## 入门

要安装 CLibs，请在您的首选终端中输入以下命令:

```
git clone https://github.com/clibs/clib.git /tmp/clib
cd /tmp/clib
make
sudo make install 
```

确保您的系统上安装了 libcurl 的开发版本。

搜索您需要的包很容易。

```
clib search [package-name] 
```

安装也很容易。

```
clib install <user>/<repo> 
```

您可以一次安装多个软件包。

## 与 CLibs 整合

也许你确信 CLibs 是 C 的包管理器，你想让你的项目得到 CLibs 的支持。

CLibs 的方式是在头脑中创建的，这是非常简单的。在项目的根目录下创建 package.json 文件。一个模板会像这样:

```
{
  "name": "term",
  "version": "0.0.1",
  "repo": "clibs/term",
  "description": "Terminal ansi escape goodies",
  "keywords": ["terminal", "term", "tty", "ansi", "escape", "colors", "console"],
  "license": "MIT",
  "src": ["src/term.c", "src/term.h"]
} 
```

有关 package.json 的更多详细信息:[https://github . com/clibs/clib/wiki/explain-of-package . JSON](https://github.com/clibs/clib/wiki/Explanation-of-package.json)

现在你知道了 C 语言有一个包管理器，你会增加对 CLibs 的支持吗？

CLibs 的 Github 库:[https://github.com/clibs/clib](https://github.com/clibs/clib)