# 在 Concourse 中使用 AWS Secrets Manager

> 原文：<https://dev.to/sirech/using-aws-secrets-manager-in-concourse-3o14>

<figure>[![Keeping it secret](img/11f31f4910ea4e8e682636726d19d47a.png "Keeping it secret")](///static/10dbb304c8c5ad37269239e7a1855de6/fc83b/lock.jpg)

<figcaption>
 <small>作者 FreeImages.com/Braden·海斯</small> 
</figcaption>

</figure>

当我写关于[现代化构建管道](//../modernizing-your-build-pipelines)时，我有一大堆不太适合这篇文章的内容。我将把其中一些作为小文章转储到这里。我相信它们可以作为一个更好的 *CI* 设置的起点。

我想说的第一件事是连接 [Concourse CI](https://concourse-ci.org/) 和 [AWS Secrets Manager](https://aws.amazon.com/secrets-manager/) 。你为什么要这么做，我想你会问？

嗯，如果你有管道建设的东西，他们可能会想在某些时候访问一些秘密。它可能是访问 *Github* 的部署密钥，或者是`npm`的凭证或其他东西。你不想以明文形式存储它。如果你不得不手动维护像 [Jenkins](https://jenkins.io/doc/book/using/using-credentials/) 这样的凭证，你就会知道那真的很糟糕。但这并不是必须的。另一种方法是连接`Concourse`和`AWS Secrets Manager`，使用 [Terraform](https://www.terraform.io/) 来保存所有代码。

*Concourse* 支持一堆开箱即用的[凭证管理员](https://concourse-ci.org/creds.html)。一旦您配置了它，您的工作和任务就可以非常方便地访问机密。看起来就这么简单

```
platform: linux
inputs:
  - name: git
  - name: shared-tasks
params:
  NPM_TOKEN: ((npm_auth_token))
run:
  path: sh
  dir: git
  args:
  - -ec
  - |
    ../shared-tasks/scripts/install-yarn-packages.sh
    ./go linter-js 
```

这样， *linter* 任务将可以安全地访问`NPM_TOKEN`，这需要从私有的`npm`存储库中提取。但是对于编写管道并且不想做大量管道工作的人来说，这非常方便。

### 我得给自己弄点这个

首先，需要用正确的设置启动 concourse，这样它就知道在正确的区域使用`ASM`，以及正确的路径

```
/usr/local/bin/concourse web \
  --aws-secretsmanager-region=us-east-1 \
  --aws-secretsmanager-team-secret-template=/concourse-ci/{{.Secret}} \
  --aws-secretsmanager-pipeline-secret-template=/concourse-ci/{{.Secret}} 
```

您可以使用更多的粒度，并在其中包含一个`{{.Team}}`或`{{.Pipeline}}`，但是在这种情况下，我们只需要简单的秘密。这样，实例就能够用上面提到的语法找到给定路径下的秘密。

### 给我权利

然而，我们还没有到那一步。广场需要能够接触到这些秘密。您可以向它传递凭证，但是更安全的方法是定义一个 *IAM 角色*，正如文档中提到的。如果您在 EC2 上运行您的配置项，这个示例将起作用。第一，政策

```
resource "aws_iam_policy" "concourse-ci-web" {
  name = "${aws_iam_role.concourse-ci-web.name}"
  policy = "${data.aws_iam_policy_document.concourse-ci.json}"
}

data "aws_iam_policy_document" "concourse-ci" {
  statement {
    effect = "Allow"

    actions = [
      "secretsmanager:DescribeSecret",
      "secretsmanager:GetSecretValue",
      "kms:Decrypt",
    ]

    resources = [
      "arn:aws:secretsmanager:*:*:secret:concourse-ci/*",
      "arn:aws:secretsmanager:*:*:secret:/concourse-ci/*",
      "${formatlist("arn:aws:kms:*:*:key/%s", var.asm_key_ids)}",
    ]
  }
} 
```

然后，该策略与 EC2 承担的角色相关联

```
resource "aws_iam_role" "concourse-ci-web" {
  name = "concourse-ci-web"
  assume_role_policy = "${data.aws_iam_policy_document.assume_role_policy_ec2.json}"
}

resource "aws_iam_instance_profile" "concourse-ci-web" {
  name = "${aws_iam_role.concourse-ci-web.name}"
  role = "${aws_iam_role.concourse-ci-web.name}"
}

resource "aws_iam_role_policy_attachment" "concourse-ci-web" {
  policy_arn = "${aws_iam_policy.concourse-ci-web.arn}"
  role = "${aws_iam_role.concourse-ci-web.name}"
} 
```

最后是承担角色的策略

```
data "aws_iam_policy_document" "assume_role_policy_ec2" {
  statement {
    effect = "Allow"
    actions = ["sts:AssumeRole"]

    principals {
      type = "Service"
      identifiers = ["ec2.amazonaws.com"]
    }
  }
} 
```

### 就这样？

嗯，大部分是。这个例子并不完整，还需要更多的代码来使它工作，但这是它的要点。最好的部分是，您只需设置一次，然后就可以访问您在该名称空间中定义的秘密，就像这样。