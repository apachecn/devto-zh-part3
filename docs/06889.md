# Fix neovim errors

> 原文：<https://dev.to/acro5piano/fix-neovim-errors-12cb>

[![image](img/62fc027e6c8e7588f0446f5fe763da9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--92Bdl5tY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/10719495/56094309-19e30500-5f0e-11e9-921f-6add35cba9fb.png)

从最新的 OSX 更新，一些插件开始不工作。尤其是`deoplete`和`UltiSnips`显示错误。

原因可能是缺少 Python3，所以下面的命令将修复它们:

```
brew install neovim
pip3 install -U neovim
nvim

# in neovim
:UpdateRemotePlugins 
```

Enter fullscreen mode Exit fullscreen mode

如果仍然有问题，在 vim 中运行`checkhealth`。

```
:checkhealth 
```

Enter fullscreen mode Exit fullscreen mode