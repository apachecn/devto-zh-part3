# 相当快速的饭桶把戏

> 原文：<https://dev.to/aleclarson/pretty-quick-git-trick-3jdc>

在一个 [`husky`](https://github.com/typicode/husky) 预提交钩子中使用 [`pretty-quick`](https://github.com/azz/pretty-quick) 的一个很酷的事情是，它在你运行时捕获未解决的合并冲突:

```
git cherry-pick --continue 
```

Enter fullscreen mode Exit fullscreen mode