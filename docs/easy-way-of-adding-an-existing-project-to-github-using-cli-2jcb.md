# 使用 CLI 向 github 添加现有项目的简单方法。

> 原文：<https://dev.to/sandipbasnet/easy-way-of-adding-an-existing-project-to-github-using-cli-2jcb>

如果您已经有了现有的项目或文件，并且想要在 github repo 上推送/发布它，但是不熟悉 git 命令，那么下面的步骤将使您在几分钟内完成任务。

第一步:-首先访问你的 github 仪表板，然后创建一个私人或公共的 repo，但不要启动 README 和任何许可证模板，然后单击创建新的存储库按钮，之后你将被重定向到快速设置页面，现在复制项目 URL 与 HTTPS 选项。

步骤 2:-打开终端，然后将目录更改为您的工作目录或项目目录。

步骤 3:-使用
$git init 将本地项目初始化为 Git 存储库。

步骤 4:-使用$git add 将文件添加到存储库中。，这会将本地存储库的文件添加到初始提交的暂存中。

步骤 5:-使用$git commit -m“您的提交消息”提交您在本地存储库中添加或暂存的文件。

步骤 6:-现在用$git remote add origin < your remote repository URL>添加您从快速设置页面复制的远程存储库的 URL，然后用$git remote -v 验证它。

步骤 7:-现在使用$git push origin master 将您的暂存文件推送到远程存储库

之后，你将被要求 Github 用户名和密码，请提供这些凭证，一旦你输入你的密码，你的本地项目就开始推送到 Github。