# 用 GitHub 动作修改代码的最好方法是什么？

> 原文：<https://dev.to/hitochan777/whats-the-best-way-to-modify-code-with-github-actions-4k6>

我最近在尝试 GitHub 动作，想知道用 GitHub 动作修改代码的最好方法。

我的 github 库与 CircleCI 集成在一起，circle ci 使用`prettier`检查我的代码是否格式化良好。我还添加了 renew bot 来自动生成更新包依赖关系的 PRs。
问题是 renew bot 生成的代码没有格式化，导致 CI 失败。我的解决方案是每当有对存储库的推送时，触发 GitHub 动作来运行`prettier`，然后调用 GitHub API 来推送格式化代码的提交。你有什么看法？