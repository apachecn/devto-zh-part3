# Bugfix 更新

> 原文：<https://dev.to/jks7743/bugfix-update-2397>

# 假设

假设是一个开源库，它允许你使用“基于属性的测试”来编写测试，并且仍然是一个正在进行的工作。它的目标是成为 QuickCheck 的一个简单的替代方案，而不需要用户具备函数式编程的知识。我偶然发现这个库希望满足我的 bugfix 任务的需求，并发现了一个看起来对我来说很容易开始工作的 bug。

# 臭虫

我决定处理的这个 bug 更像是一个清晰的问题。我将确保变量的类型正确，并进行测试以查看错误检查是否正常工作，我很清楚我必须做什么，我只需要进行适当的测试，并找出在检查后引发错误时最好的格式。