# 将项目放到 GitHub 上

> 原文：<https://dev.to/httpjunkie/getting-a-project-onto-github-42e5>

1.  打开命令行，将目录更改为项目的根目录
2.  本地初始化 Git，运行:`git init`
3.  分别运行:`git add .`和`git commit -m "initial commit"`
4.  这将暂存并提交所有文件
5.  考虑使用[。gitignore](https://gist.github.com/httpJunkie/d2cb743e924213902e46f5b35c29a3c1) 文件
6.  在 GitHub 上创建一个新的 repo:“您的存储库”>“新建”
7.  如果你的回购命名为:“MyRepo”，你的 Github 用户命名为:“MrGuy”
8.  运行:`git remote add origin https://github.com/MrGuy/MyRepo`
9.  运行:`git push -u origin master`