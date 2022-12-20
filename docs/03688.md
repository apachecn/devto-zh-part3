# 在 Visual Studio 代码中调试 Pip 包

> 原文：<https://dev.to/djbeadle/debugging-a-pip-package-in-visual-studio-code-2if9>

让我们考虑这样一种情况，您正在一个简单的环境中编写一些 ***难以置信地*** 重要的业务逻辑，并且您想要确切地看到当您调用 *get_joke()* 时**py 笑话**包正在做什么。

**business_logic.py** :

```
from pyjokes import get_joke
print(get_joke()) 
```

包的 [Git 库](https://github.com/pyjokes/pyjokes)通常是一个很好的起点，但是有时您希望能够一步一步地完成包。
你可以这样做:

1.  激活您的虚拟环境(如果您尚未激活)
2.  卸载软件包`pip uninstall pyjokes`
3.  用可编辑标志

    ```
    pip install --editable git+git@github.com:pyjokes/pyjokes.git#egg=pyjokes

    ```

    从源代码重新安装软件包
    *   `--editable`可以简称为`-e`
    *   您并不局限于 Git 库！你也可以使用“svn+”和“bzr+”以及其他一些。参见 Pip 的 [VCS 支持文档](https://pip.pypa.io/en/stable/reference/pip_install/#vcs-support)
    *   `#egg=[Custom Name]`创建一个相对于项目路径的目录【自定义名称】. egg.info。你可能不会关心这个，但这是必须的。
4.  py 笑话库现在可以在`[virtual environment folder name] > src > pyjokes`中找到。但这不是我们真正感兴趣的。
5.  找到源代码在存储库中的实际位置，在这个例子中，它是一个同名的子文件夹。有时候是一个叫“src”的子文件夹。`[virtual environment folder name] > src > pyjokes > pyjokes`
6.  将此文件夹向上移动到项目的根级别。例如，此项目目录现在包含以下文件和目录:
    *   py 笑话(文件夹)
    *   虚拟环境文件夹
    *   business_logic.py
7.  快好了！在 py 笑话源代码中设置想要的断点，打开 business_logic.py，切换到 VSCode 调试器，按绿色的 run 按钮。将出现两个提示，第一个选择 Python，第二个选择“当前文件”!

作为额外的奖励，您也可以编辑这个包——祝调试好运！