# npm 安装。分期发行

> 原文：<https://dev.to/ishwar/npm-install-staging-issue-3m9o>

通过删除“package-lock.json”文件解决了该问题。npm 文件、node_modules 文件和清理 npm 缓存
所需步骤:

npm 安装 npm@latest -g
npm 缓存清理—强制
rm -rf ~/。npm
rm -rf 节点 _ 模块
rm -f 包-锁. json

在 npm 安装中。转移问题，当您运行 npm install 时，它会创建 node_modules 文件夹，但所有模块都放在名为的子文件夹中。脚手架