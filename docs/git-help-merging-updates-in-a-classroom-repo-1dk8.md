# Git 帮助:在课堂报告中合并更新

> 原文：<https://dev.to/goodidea/git-help-merging-updates-in-a-classroom-repo-1dk8>

我有一份课程报告，我用它来教一门反应课。([这里可以看到](https://git.generalassemb.ly/good-idea/react-exercises))。

每节课都有几个文件:

*   减价文件
*   测试文件
*   练习文件
*   解决方案文件
*   也许一些样本数据

学生将在练习文件中工作，构建 react 组件以通过测试。他们的大部分更改都在这里，但是他们也会更改测试文件中的几行(当他们在进行新的测试时，将`it.skip`更改为`it`)。)

每个人都创建了自己的回购分支，并克隆了它。我有一个`init`脚本，它将原始回购添加为一个`upstream`遥控器。

随着我们的进展，我正在对主回购进行更新:修复一些打字错误，并清理笔记。我想找出一种方法，让我的学生在不丢失他们已经完成的工作的情况下获取这些更新。

一个简单的解决方案是 doing `git fetch upstream && git merge --strategy-option ours upstream/master` -这将引入所有新的更新，但每当有合并冲突时，选择学生的工作。

但是，我也希望学生能够合并更新，如果他们选择这样做。一种情况是，如果他们在一个特定的练习中挣扎，想要“重置”它。

理想的工具是交互式 CLI，其中:

*   学生运行“更新课程”脚本
*   对于每个合并冲突，他们可以选择:
    *   保留自己的工作
    *   备份他们自己的工作，并合并到新的更新中。例如，如果在`./src/lesson1/exercise.js`中有冲突，他们可以选择这个选项，并以如下方式结束:
        *   `./src/lesson1/exercise.js`(更新文件)
        *   (他们已经完成的工作)

我的学生对 git 相当陌生，所以我想给他们提供一些简单的东西。

我对 git 的了解还不足以解决这个问题，但是如果给我指出正确的方向，我可以编写某种 bash 脚本。

有人知道有什么工具、工作流程或 git 魔法可以做到这一点吗？