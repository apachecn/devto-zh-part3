# 我最喜欢的 VSCode 扩展和设置

> 原文：<https://dev.to/nickjj/my-favorite-vscode-extensions-and-settings-k5l>

**本文最初发布于 2018 年 11 月 20 日:[https://nickjanetakis . com/blog/my-favorite-vs code-extensions-and-settings](https://nickjanetakis.com/blog/my-favorite-vscode-extensions-and-settings)**

* * *

编辑:我不再使用这个设置了。2019 年 3 月我改用 Vim，因为 VSCode 写起来效率太低。[阅读我为什么转用 Vim](https://nickjanetakis.com/blog/vim-is-saving-me-hours-of-work-when-writing-books-and-courses) 。

我认为自己是一名通用开发人员。作为一名自由职业者，我倾向于涉猎一系列不同的语言和技术，所以能够使用像 VSCode 这样的编辑器很好，它几乎适用于所有东西。

## VSCode 扩展

如果您打开 VSCode 扩展面板(`CTRL + Shift + X`)，您可以按名称搜索这些扩展中的任何一个，以获得每个扩展的完整详细信息并下载到那里。

在列表之后，还有一行脚本，你可以用来安装所有这些扩展，当然你也可以编辑掉你不想要的扩展。

#### 主题

让我们从两个主题开始。

我喜欢保持一个明亮和黑暗的主题，即使我大部分时间使用明亮的主题。我通常在光线充足的房间里写代码，我发现浅色的主题让文本更容易阅读，但是当我关灯时，我使用深色的主题。

##### GitHub Plus 主题仙丹改编[光明]

这个主题与常规的 GitHub Plus Light 主题相同，只是它包含了对 Elixir 更好的语法着色支持。[下面是它的样子](https://raw.githubusercontent.com/thenikso/github-plus-theme/master/screenshot.jpg)。

##### 德拉库拉官方【黑暗】

德古拉不会错的！[下面是它的样子](https://draculatheme.com/assets/img/screenshots/vscode.png)。

#### 功能

这将是一个按字母顺序排序的非主题扩展列表。

##### 自动热键

AutoHotkey 是一种用于 Windows 的脚本语言。这个扩展增加了语法高亮和代码片段。

##### 码头工人

Docker 为 Dockerfiles 添加了语法高亮，但也为 Dockerfiles 和 docker-compose.yml 文件添加了自动完成功能。非常方便！

##### DotENV

DotENV 为有环境变量的点文件增加了语法高亮，比如`.env`。

##### erb

erb 增加了对 ERB 模板语言的语法突出显示支持。

##### Excel 查看器

Excel Viewer 提供了一个预览窗口，您可以在其中打开 CSV 文件并按列对其进行排序。非常适合轻量级 CSV 浏览。

##### gettext

gettext 为`po`、`pot`和`potx`文件增加了语法高亮显示(i18n 相关)。

##### HTML 片段

HTML Snippets 添加了一堆 HTML5 代码片段。

##### 金佳

Jinja 为 Jinja 2 模板语言增加了语法高亮和代码片段。

##### 液态语言支持

Liquid Language 支持为 Liquid template 语言增加了语法高亮显示。

##### 降价预览 Github 造型

Markdown Preview Github 样式将 Markdown 预览设置为与您的`README.md`文件在 Github 上的外观相同。真的有用！

##### engine . conf

nginx.conf 为 nginx 配置文件增加了语法高亮。

##### 偏微分

部分差异让您比较文件、剪贴板和选定文本之间的文本差异。

##### 彩虹 CSV

Rainbow CSV 通过对每一列进行不同的着色，使查看 CSV 文件变得非常容易。

##### 外壳检查

shellcheck 是一个优秀的 Shell / Bash 脚本林挺工具。

##### 拼写正确

拼写正确是一个超级快速的离线拼写检查。

##### systemd-单位-文件

systemd-unit-file 增加了 systemd 单元文件的语法突出显示。

##### 待办事宜树

Todo Tree 将在你的代码中寻找`TODO`和`FIXME`行，并在你的侧边栏中以树形视图的形式呈现它们。

##### vscode-elixir

vscode-elixir 为 elixir 编程语言增加了语法突出显示等功能。

##### vscode-gemfile

vscode-gemfile 允许你将鼠标悬停在你的`Gemfile`中的宝石上，并在 RubyGems 上获得它的链接。

##### vscodefirstpupper

VSCodeFirstUpper 允许您将文本字符串转换为不同样式的标题大小写。我用它来写我所有的博客文章标题。

##### 字计数器

WordCounter 将单词、字符、行数和阅读时间的计数添加到状态栏中。

#### 快速安装这些扩展

这里有一个将安装上述所有扩展的 1 liner。只需将其复制/粘贴到您的终端。请随意从下面的列表中删除您不想要的扩展名。

```
code \
  --install-extension abusaidm.html-snippets \
  --install-extension ban.spellright \
  --install-extension bierner.markdown-preview-github-styles \
  --install-extension bung87.vscode-gemfile \
  --install-extension coolbear.systemd-unit-file \
  --install-extension CraigMaslowski.erb \
  --install-extension dracula-theme.theme-dracula \
  --install-extension fimars.github-plus-theme-elixir-adapted \
  --install-extension GrapeCity.gc-excelviewer \
  --install-extension Gruntfuggly.todo-tree \
  --install-extension hangxingliu.vscode-nginx-conf-hint \
  --install-extension kirozen.wordcounter \
  --install-extension mechatroner.rainbow-csv \
  --install-extension mikestead.dotenv \
  --install-extension mjmcloug.vscode-elixir \
  --install-extension mrorz.language-gettext \
  --install-extension neilding.language-liquid \
  --install-extension PeterJausovec.vscode-docker \
  --install-extension ryu1kn.partial-diff \
  --install-extension samuelcolvin.jinjahtml \
  --install-extension shanoor.vscode-nginx \
  --install-extension slevesque.vscode-autohotkey \
  --install-extension timonwong.shellcheck \
  --install-extension wholroyd.jinja \
  --install-extension zkirkland.vscode-firstupper 
```

我通过运行这个命令生成了这个列表:

`code --list-extensions | xargs -L 1 echo code --install-extension`

#### 等等，Python 和 Ruby 语言扩展在哪里？

我不使用它们，因为我所有的应用程序都在 Docker 中运行，所以像自动完成这样的东西对我没有帮助，因为 VSCode 还不能找出 Docker 容器中的东西在哪里运行。

最终它将能够解决这个问题，然后我会考虑使用这些扩展。

我只使用 vscode-elixir 扩展来获得对 elixir 的语法突出显示支持。默认情况下，VSCode 为 Python 和 Ruby 提供语法高亮支持，没有任何扩展。

这种设置的一个好处是，VSCode 在输入时速度非常快。从来没有任何微口吃或试图与自动完成窗口战斗，带来不正确的信息。

这也是我不使用 Ansible 扩展的原因，尽管我一直在使用 Ansible。它的自动完成窗口减慢了我的速度。我将林挺和代码格式化警告留给了 VSCode 之外的工具，如`yamllint`和`ansible-lint`。

#### 等等，调试呢？

事实上，调试 web 应用程序很难。

我的意思是，当然，当我使用 VB6 和 C#处理原生 Windows 应用程序时，我喜欢调试器，但我从未找到一个不错的 web 应用程序调试解决方案，所以根据我的经验，我发现大多数时候使用 print 语句会更快。

我也偶尔使用内置于我正在使用的编程语言中的交互式调试器，但它与 VSCode 无关。

## VSCode 设置

以下是我上次使用 VSCode 后的最新设置:

```
{  "editor.fontSize":  15,  "terminal.integrated.fontSize":  15,  "editor.minimap.enabled":  false,  "editor.mouseWheelZoom":  true,  "editor.rulers":  [80],  "editor.tabSize":  2,  "editor.cursorWidth":  5,  "editor.cursorBlinking":  "solid",  "editor.renderWhitespace":  "boundary",  "explorer.autoReveal":  false,  "workbench.colorCustomizations":  {  "activityBar.background":  "#021e45",  "statusBar.background":  "#021e45",  "statusBar.foreground":  "#e0e0e0",  "editorLineNumber.foreground":  "#d1d1d1",  "editorOverviewRuler.errorForeground":  "#fff"  },  "[yaml]":  {  "editor.autoIndent":  false  },  "files.associations":  {  "*.erb":  "erb"  },  "files.exclude":  {  "**/.git":  true,  "**/.svn":  true,  "**/.hg":  true,  "**/CVS":  true,  "**/.DS_Store":  true,  "**/_site":  true,  "**/tmp":  true,  "**/*.pyc":  true,  "**/*.asset-cache":  true,  "**/*.cache":  true,  "**/*.jekyll-cache":  true,  "**/*.tweet-cache":  true,  "**/__pycache__":  true,  "**/.pytest_cache":  true,  "**/*.egg-info":  true,  "**/*.coverage":  true,  "**/celerybeat-schedule":  true,  "**/node_modules":  true,  "**/bower_components":  true  },  "files.insertFinalNewline":  true,  "files.trimFinalNewlines":  true,  "files.trimTrailingWhitespace":  true,  "files.eol":  "\n",  "[markdown]":  {  "files.trimTrailingWhitespace":  false  },  "telemetry.enableTelemetry":  false,  "terminal.integrated.shell.windows":  "C:\\WINDOWS\\System32\\wsl.exe",  "terminal.external.windowsExec":  "C:\\WINDOWS\\System32\\wsl.exe",  "workbench.list.openMode":  "doubleClick",  "workbench.sideBar.location":  "left",  "workbench.startupEditor":  "newUntitledFile",  "git.ignoreMissingGitWarning":  true,  "shellcheck.run":  "onSave",  "shellcheck.useWSL":  true,  "extensions.ignoreRecommendations":  true,  "workbench.activityBar.visible":  true,  "workbench.colorTheme":  "GitHub Plus Elixir Adapted",  "editor.accessibilitySupport":  "off",  "window.title":  "${activeEditorLong}",  "editor.detectIndentation":  false,  "editor.autoIndent":  false,  "problems.decorations.enabled":  false,  "editor.renderControlCharacters":  false,  "breadcrumbs.enabled":  false,  "editor.autoClosingQuotes":  "never",  "editor.autoSurround":  "never",  "editor.autoClosingBrackets":  "never",  "shellcheck.disableVersionCheck":  true,  "todo-tree.defaultHighlight":  {  "foreground":  "green",  "type":  "none"  },  "todo-tree.customHighlight":  {  "TODO":  {},  "FIXME":  {}  }  } 
```

**你最喜欢的 VSCode 扩展和设置是什么？下面让我知道！**