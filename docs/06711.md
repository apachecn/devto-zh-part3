# 如何解决重命名文件和文件夹的 Git 问题

> 原文：<https://dev.to/paramharrison/how-to-solve-git-issues-on-renaming-files-and-folder-5adf>

由于 git 中的文件重命名问题，你们中的许多人可能会做恶梦。最近我遇到了一个问题，一切都在本地工作，但我的 circle CI 反复抛出错误，因为在传输 JSX 时找不到文件。

文件名是`NavBar`,在最近的 git 提交中，我将其重命名为`Navbar`。我没有注意到名称上的不匹配，我需要一段时间来找到问题的根源。

这是我解决问题的方法，可能还有其他更好的解决方案，但这种方法对我很有效。

```
# Rename the file once again from Navbar to NavBar (basically revert back to original file name)
git mv NavBar.jsx Navbar.jsx 
```

这个解决方案也可以应用于文件夹名称的更改。对于文件夹，

```
git mv ./NavBar ./Navbar 
```

希望这能帮助你学习 git move 命令的技巧🤗