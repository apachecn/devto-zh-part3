# 巴什！巴什！砰！砰！

> 原文：<https://dev.to/slatham/bash-bash-bang-bang-hel>

# 砰砰(！！)

Bang Bang 是什么？在命令行中键入两个感叹号，然后尽可能用力按回车键。

# [T1】！！=再放一遍最后一个命令，Sam。](#-play-the-last-command-again-sam)

## 举例:

假设您正在全局安装一个 npm 软件包(正如您所做的):-

`npm install -g <package_name>`

却收到许多拒绝许可的错误。没错，你又忘了用“sudo”了吧！

你可以按向上箭头，然后导航到行首，键入“sudo”，按空格键，然后温顺地按下回车键。

相反，你可以推出万能的 Bang Bang: -

`sudo !!`[![boom](img/d0e9a5d7c5fb713211c633ebde6ea875.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--mq1CJXKF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zcb020lvonv063e9br02.png)

在我们的例子中，这相当于:-

`sudo npm install –g <package_name>`

你最终会在任何地方使用这个快捷方式。

去上班后才发现忘记了自己的通行证，想想吧-

`pick up pass !!`

然后，如果这不起作用，部署大炮:-

`sudo !!`

问题解决了。