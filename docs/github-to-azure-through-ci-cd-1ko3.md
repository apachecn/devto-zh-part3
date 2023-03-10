# 通过 CI/CD 从 GitHub 到 Azure

> 原文：<https://dev.to/justanotherdeveloper2019/github-to-azure-through-ci-cd-1ko3>

在我之前的 **[文章](https://dev.to/ajithk444/host-a-website-on-azure-in-5-minutes-540a)** 中，我们看到了如何将一个应用部署到微软 Azure。作为延续，我们可以看到如何将相同的应用从 Github 部署到 Azure。

**第一步:** *源代码控制*

选择 GitHub 并链接您的帐户。

[![](img/0ca4b12ae00eb6c0d09323c93914539b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3oQX5hge--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aqsnmqv6qysc8qcfe9zl.png)

**第二步:**_ 构建提供者

在下一步中，选择 *Kudu 构建服务器*
[![](img/4ddc1b2722d12c78301108f90f8ffe95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ejmMm3cK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/obhal2qe0r8o01p4ortp.png)

**第三步:** *配置构建*

选择 webapp 的组织、存储库和分支。
[![](img/5773e13bfbd91681d8f928559f4deff1.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--jdup_Qy7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vi899ume59nkm0juyb0y.png)

**第四步:** *完成 CI/CD 设置*

[![](img/62fe69cc265d526fcc1671c81eb54d1d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--blM02-ef--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1a1yl4ysgf8wjy2qakep.png)

**第五步:** *瞧！！*
现在 github repo 与 Azure app 部署同步。

[![](img/b5be4db5c1bd0e3aefffe13b7f3f166d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Qh757MI4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8f0zh67chqxnhw4z56yb.png)

现在，如果您在 repo 中进行任何更改，并提交到已部署的分支，那么 CI/CD 管道会将新的构建推送到 Azure app center。

希望这篇文章对你有所帮助。

链接到我之前关于部署的文章:[托管一个应用到 Azure](https://dev.to/ajithk444/host-a-website-on-azure-in-5-minutes-540a)