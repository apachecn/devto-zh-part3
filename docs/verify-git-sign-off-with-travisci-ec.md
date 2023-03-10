# 与 TravisCI 一起验证 git 签核

> 原文：<https://dev.to/gonsie/verify-git-sign-off-with-travisci-ec>

我不知道如何解释我作为 [mpiFileUtils](https://github.com/hpc/mpifileutils) 项目贡献者的角色。我加入是因为这些工具非常有用，而且非常酷！我还想代表 HPC 用户，并贡献文档让更多的人使用它们。虽然我没有接触“核心”代码，但我已经[将构建系统转换成 CMake](https://github.com/hpc/mpifileutils/pull/208) ，创建了不同的[用户](https://mpifileutils.readthedocs.io/)和[开发者](https://github.com/hpc/mpifileutils/wiki)文档，并开始作为团队开发过程的 [scrum master](https://en.wikipedia.org/wiki/Scrum_(software_development)) 。

我最近注意到的一件事是，我们记录的[贡献需求](https://github.com/hpc/mpifileutils/blob/master/.github/CONTRIBUTING.md)与我们在实践中所做的不匹配。也就是说，我们的团队已经同意使用“签署”提交作为我们项目的一部分(受 [OpenMPI](https://www.open-mpi.org/faq/?category=contributing#contribute-code) 的启发)。但是，由于我们没有自动检查它，签署并不总是发生。

所以，今天我开始补救这种情况。

## 验证 git 签收

我们使用一种不太严格的 git 签名形式，只是在提交消息中有一个“签名者”行(而不是完整的 gpg 密钥签名)。这可以/应该自动添加到带有`git commit -s`标志的提交消息中。检查最近一次提交是否得到了批准很简单:

```
git show --summary HEAD | grep -q "Signed-off-by:" 
```

## 与 TravisCI 集成

我们已经和 T2 一起运行了一些自动化测试。Travis 提供了一个`before_script`步骤，它在我们的项目构建之前触发。这是 git-show + grep one liner 的最佳位置！一切似乎都正常…直到我实际创建了这个变更的拉请求。奇怪。

原来，TravisCI 不仅检查一个分支是否通过了测试，还检查与主合并的分支*是否通过了测试。Travis 正在进行合并，因此在运行验证行之前更改了`HEAD`。因此，验证应该只在测试分支时触发。幸运的是，Travis 提供了一个环境变量，设置为测试是否在 PR 上运行。*

因此，这个一行程序变成了:

```
'if ["$TRAVIS_PULL_REQUEST" = "false"]; then git show --summary HEAD | grep -q "Signed-off-by:"; fi' 
```

有了这个小小的改变，我们的团队现在很容易自动地保持我们设定的标准。

## 告诫

mpifileutils 团队已经同意，我们不会在每次提交时都强制签署，只是在最后一次提交拉请求时强制签署。因此，该检查只查看最后一次提交的`HEAD`。