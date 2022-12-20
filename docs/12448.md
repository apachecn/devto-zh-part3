# 重新加载你的 Chrome 扩展-保存在 VS 代码中！

> 原文：<https://dev.to/solomon/reloading-your-chrome-extensions-on-save-in-vs-code-3cgb>

*更新:[还有更好的办法](https://dev.to/sgvictorino/reloading-your-unpacked-chrome-extensions-on-save-from-anywhere-884)T3】*

有一个非常酷的 Chrome 扩展，我在 DEV 上评论过，叫做“重新加载扩展”，它让我避免了加载扩展的错误(除了重新加载未打包的扩展的不便之外):

[![solomon profile image](img/2696e42461622da0727c86999f4920c7.png) ](/solomon) [ Solomon ](/solomon) • [<time datetime="2019-02-23T22:55:47Z"> Feb 23 '19 </time>](https://dev.to/solomon/comment/92b9) 

没错。尤其是当我启用/加载一个扩展后，扩展图标没有更新时(我认为这是无意的行为)，我只需进入 [reload.extensions](http://reload.extensions) ，一切都按预期工作！

但是对于我们这些有抱负的扩展开发者来说，它会变得更加方便。

[![kinghat profile image](img/dc9d4f83396fcad58bc07f3223687ad7.png) ](/kinghat) [ kinghat ](/kinghat) • [<time datetime="2019-02-23T22:48:44Z"> Feb 23 '19 </time>](https://dev.to/kinghat/comment/92b7) 

您是否使用扩展重新加载器 url 来自动重新加载？

(感谢 [@kinghat](https://dev.to/kinghat) 激发了这篇帖子！)

# 配置

## 先决条件

您需要安装以下软件:

*   [Visual Studio 代码](https://code.visualstudio.com/Download)
*   [扩展重新加载器](https://github.com/arikw/chrome-extensions-reloader)
*   [谷歌 Chrome](https://www.google.com/chrome) 、[勇敢](//brave.com)、[维瓦尔第](https://vivaldi.com)，或者任何支持安装 Chrome 扩展的浏览器。示例命令配置是针对 Chrome 的，但是`start http://reload.extensions` (Windows)、`open http://reload.extensions` (Mac)和`xdg-open http://reload.extensions` ( [GNU/Linux](https://en.wikipedia.org/wiki/GNU/Linux_naming_controversy) )可以与上述任何浏览器一起工作，假设它们是您计算机的默认浏览器。

您可以获得这个名为 [RunOnSave](https://marketplace.visualstudio.com/items?itemName=emeraldwalk.RunOnSave) 的 Visual Studio 代码扩展，它允许您将它添加到您的`settings.json`:

## 窗口

```
 "emeraldwalk.runonsave":  {  "commands":  [  {  "match":  "\\\\extension-project-folder\\\\.*",  "cmd":  "start chrome http://reload.extensions"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

只要确保将“扩展-项目-文件夹”替换为您的项目文件夹的名称。

像 Mac 和 GNU/Linux 这样的类 Unix 操作系统不需要对反斜杠进行双重转义。

## macOS

```
 "emeraldwalk.runonsave":  {  "commands":  [  {  "match":  "/extension-project-folder/.*",  "cmd":  "open -a 'Google Chrome' http://reload.extensions"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

## GNU/Linux

```
 "emeraldwalk.runonsave":  {  "commands":  [  {  "match":  "/extension-project-folder/.*",  "cmd":  "google-chrome http://reload.extensions"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

## 自动保存配置

(这一步是可选的，但推荐使用)

为了减少中断，我建议当 VS 代码窗口在`settings.json` :
中失去焦点时，将保存模式设置为自动保存

```
"files.autoSave":  "onWindowChange" 
```

Enter fullscreen mode Exit fullscreen mode

## 就是这样！

这有点烦人(因为每次你从 VS 代码中切换出来时，它都会聚焦你的浏览器)，除非你只是在代码和 Chrome 之间切换，在这种情况下，它真的工作得很好！

我还没有在所有的操作系统和浏览器上测试过，所以请在下面的评论中告诉我这是否对你有用。

* * *

感谢阅读！

如果你从这篇文章中获得了一些知识，请按下那个❤️按钮奇数次。

# 来源

[https://stackoverflow.com/a/32775952](https://stackoverflow.com/a/32775952)
[https://stackoverflow.com/a/23039509](https://stackoverflow.com/a/23039509)
[https://superuser.com/a/790678](https://superuser.com/a/790678)
[https://askubuntu.com/a/19920](https://askubuntu.com/a/19920)
[https://github . com/emerald walk/vs code-runonsave/blob/master/readme . MD](https://github.com/emeraldwalk/vscode-runonsave/blob/master/README.md)