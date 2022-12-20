# 崇高 3:去任何地方...小费

> 原文：<https://dev.to/jamesthomson/sublime-3-goto-anything-tip-190b>

如果你使用 Sublime 3，你可能习惯于使用`Goto Anything...` (CMD+P Mac，CTRL+P Windows)来快速调出文件。一个缺点是，这将带来供应商和编译文件。这真的不理想。因此，要解决这个问题，您可以排除这些目录。

打开你的用户设置，添加这一行:

```
"binary_file_patterns": ["node_modules/", "public/", "vendor/", "*.jpg", "*.jpeg", "*.png", "*.gif", "*.ttf", "*.tga", "*.dds", "*.ico", "*.eot", "*.pdf", "*.swf", "*.jar", "*.zip"], 
```

Enter fullscreen mode Exit fullscreen mode

如果您添加了其他文件夹，请务必在它们后面加上“/”。

然后保存文件，你就可以开始了。

一如既往，编码快乐！🤓