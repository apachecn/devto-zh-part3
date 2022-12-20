# 清理并删除 Python3 自制软件安装

> 原文：<https://dev.to/therealdarkmage/clean-up-and-remove-a-python3-homebrew-install-21ai>

TL；TIL pygame 博士用家酿 Python3 坏了，所以我决定擦屁股从头开始。我想我会让你知道我做了什么，这样下次有人遇到这种事，他们就有了方便的参考。

[https://stack overflow . com/questions/11248073/删除 pip 安装的所有软件包的最简单方法是什么](https://stackoverflow.com/questions/11248073/what-is-the-easiest-way-to-remove-all-packages-installed-by-pip)

倾斜大约`pip freeze` :

```
freeze  Output installed packages in requirements format. 
```

Enter fullscreen mode Exit fullscreen mode

如果您想快速重新安装将要删除的所有内容，请将要求保存到文件中。

```
pip3 freeze > requirements.txt
pip3 freeze | xargs pip3 uninstall -y 
```

Enter fullscreen mode Exit fullscreen mode

[https://apple . stack exchange . com/questions/284824/remove-and-reinstall-python-on-MAC-can-I-trust-these-old-references](https://apple.stackexchange.com/questions/284824/remove-and-reinstall-python-on-mac-can-i-trust-these-old-references)

```
rm -rfv /usr/local/bin/python3* 
```

Enter fullscreen mode Exit fullscreen mode

好了，该装官方 Python3 了。

*   [https://www.python.org/](https://www.python.org/)

此时你可以做`pip3 install -r requirements.txt`来重新安装你卸载的所有东西。我现在保持我的东西更干净`^_^`！

* * *

重申一下:

```
pip3 freeze > requirements.txt
pip3 freeze | xargs pip3 uninstall -y
rm -rfv /usr/local/bin/python3*
# Reinstall python3 here!
pip3 install -r requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# [如果你需要一名计算机科学导师、代码审核员，或者仅仅是一个可以和程序配对的人，请联系我](https://codementor.io/mikebell66)