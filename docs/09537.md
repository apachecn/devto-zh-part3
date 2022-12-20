# 用 Python 的子流程模块自动化最枯燥的事情

> 原文：<https://dev.to/mekicha/automate-the-most-boring-thing-with-pythons-subprocess-module-1n9b>

你听说过 Python 标准库中的[子流程](https://docs.python.org/3/library/subprocess.html)模块吧？它如何:

> 允许您生成新进程，连接到它们的输入/输出/错误管道，并获取它们的返回代码。

不仅如此，它还打算取代旧的模块和功能，如`os.system`和`os.spawn*`，说实话，我没有使用经验。

基本上，子进程模块允许您从代码中启动其他程序，并将它们驱动到您想要的任何目的。想想做一些有趣的事情，比如:

1.  通过 [webbrowser](https://docs.python.org/3.6/library/webbrowser.html#) 模块或者像 [selenium](https://github.com/SeleniumHQ/selenium) 这样的第三方库从你的代码中控制浏览器
2.  使用[结构](http://www.fabfile.org/)自动化服务器部署或管理任务
3.  从您的代码运行 android 模拟器。
4.  是啊，我是说，你明白了！

如果你想深入了解，文档有你需要的所有信息。如果是你，谢谢你的到来:)

我们这些只想在河边袖手旁观和舀些水的人，就是正在读这句话的人和这句话下面的人。

玩笑归玩笑，让我们走到这一步的是我和其他同事合作开发了几个存储库。所有这些回复都存在于我的主目录下的`Projects`子目录中。目标是能够在我的工作日开始时自动运行所有这些回购的`git pull`。另一种选择是将`cd`放入一个项目目录，运行`git pull`、`cd`进入下一个目录，等等。我的朋友们，这太无聊了！

事不宜迟，代码如下:

```
 from subprocess import check_output, CalledProcessError
from pathlib import Path

project_dir = Path.home() / 'Projects' / 'work'

for d in project_dir.iterdir():
    if d.is_dir(): # this check is not really needed
        try:
            out = check_output(["git", "pull", d], cwd=d)
            res = out.decode()
            print(res.splitlines()[-1])
        except CalledProcessError as exc:
            pass 
```

简而言之，我正在遍历我的工作目录中的所有目录(repos ),对它们运行一个`git pull`。我还对输出进行解码(默认情况下输出是以字节为单位的)并打印输出列表中的最后一个元素(调用 res.splitlines 将输出变成一个列表)。
如果有错误，我就当没有发生过一样传过去。

仅此而已，相信我！我可以在任何地方运行这个小脚本并更新我的回复。也许我甚至可以进一步检查输出，看看回购是否真的有更新。如果是这样，我可以在代码编辑器或浏览器中打开那个特定的 repo。但是这有什么意义呢？