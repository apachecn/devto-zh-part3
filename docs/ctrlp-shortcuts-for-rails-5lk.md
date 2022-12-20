# Rails 的 CtrlP 快捷方式

> 原文：<https://dev.to/scottw/ctrlp-shortcuts-for-rails-5lk>

我喜欢@garybernhardt 在 vim 中为他的 Selecta 脚本提供特定的 rails 快捷方式。作为一个 CtrlP + Ag 的粉丝，我不想切换，所以我想出了下面这个效果很好的方法。

```
map <leader>gv :CtrlP app/views<cr>
map <leader>ga :CtrlP app/assets<cr>
map <leader>gc :CtrlP app/controllers<cr>
map <leader>gw :CtrlP app/workers<cr>
map <leader>gm :CtrlP app/models<cr>
map <leader>gl :CtrlP lib<cr>
map <leader>gs :CtrlP spec<cr> 
```

模式应该很明显。我最初犯的一个错误是使用了:

```
map <leader>gl :CtrlP<cr>lib/ 
```

这最终只是在搜索中为您输入 lib/这明显慢了很多，因为每一次击键都是一次无用的搜索。还有，像 *lib* 这样的小目录名，很容易出现无效的模糊匹配。CtrlP +目录，然后按 enter 键，使搜索只集中在指定的目录上。