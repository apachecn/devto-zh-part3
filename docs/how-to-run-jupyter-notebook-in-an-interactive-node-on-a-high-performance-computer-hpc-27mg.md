# 如何在高性能计算机(HPC)的交互节点中运行 Jupyter notebook？

> 原文：<https://dev.to/rohitfarmer/how-to-run-jupyter-notebook-in-an-interactive-node-on-a-high-performance-computer-hpc-27mg>

[![Computer](img/95e1895aa10716c9834169d3f3300d38.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DZ9T-ekE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9509rdfdw2ad3r18lf6b.jpg)

下面是在高性能计算机(HPCs)的交互节点中运行 Jupyter notebook 的示例协议。大多数 HPC 都有专门的交互方式。因此，您可能需要根据自己的需要调整这个协议。我很乐意与您一起讨论和解决问题；通过[联系我 contact@rohitfarmer.dev](//mailto:contact@rohitfarmer.dev) 。欢迎提出任何建议，为该协议增加更多高级功能。

1.  SSH 到 HPC。
2.  声明一个交互节点(遵循您的 HPC 的标准过程，在我的例子中是`qrsh`)。
3.  请注意交互节点的名称。
4.  通过`jupyter notebook --no-browser --ip='0.0.0.0'`在声明的交互节点上运行 Jupyter，或者在 bashrc 中创建一个快捷方式的别名。比如`alias jup='jupyter notebook --no-browser --ip='0.0.0.0''`。
5.  在您的计算机上，使用如上所述的交互节点名称启动另一个带隧道的 SSH 会话`ssh user@host -L8888:nodeName:8888 -N`。*提示可能不会返回，您也可能在终端中看不到任何消息，但只要没有错误消息，它可能运行得很好。*
6.  为了避免每次创建隧道时都要重写第 5 步中的代码，您可以使用下面的 shell 脚本。

我把它命名为`jupssh`。

```
#!/bin/sh

# Check if the arugment is passed.
if [[$# -eq 0]];
then
    echo 'Usage: jupssh <node name>'
    exit 1
fi

ssh user@host -L8888:$1:8888 -N 
```

Enter fullscreen mode Exit fullscreen mode

*   复制 Jupyter 守护进程在步骤 4 中生成的 URL，并将其粘贴到您计算机上的浏览器中。URL 应该类似于`http://(nodeName or 127.0.0.1):8888/?token=3f7c3a8949b3fa1961c63653873fea075a93a29bffe373b5`。在 URL 中选择节点名或 127.0.0.1。

顶部横幅照片由
[unsplash-logo费代丽卡·加利](https://unsplash.com/@fedechanw?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge "Download free do whatever you want high-resolution photos from Federica Galli") 拍摄