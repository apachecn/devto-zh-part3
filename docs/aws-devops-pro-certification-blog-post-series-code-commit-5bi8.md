# AWS DevOps Pro 认证博客文章系列:代码提交

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-code-commit-5bi8>

Elisey Vavulin 在 [Unsplash](https://unsplash.com/photos/_LKQQaV-9k4) 上拍摄的照片

## 概不退换

使用 AWS 要花钱，其中一些服务可能不属于 AWS 免费层。你可以通过拆除你在学习时创造的任何东西来降低成本，但仍然有可能产生巨额账单，所以要注意你设置的实例！

我非常幸运能够使用我雇主的 AWS 帐户。你应该问问你工作的地方，是否可以做类似的安排作为你学习的一部分。

## 谁快窒息征服谁

当我尝试完善我的每个领域的心智模型时，博客帖子的格式很容易改变，所以一定要定期重温博客帖子。

## 什么？

代码提交是:

*   托管版本控制服务
*   基于 Git(使用 HTTPS 或 SSH 进行连接)
*   文件是静态加密的，密钥可以在 IAM >加密密钥中找到。
    *   AWS CLI: `aws kms list-aliases | jq '.Aliases[] | select(.AliasArn | contains("codecommit"))'`
*   HTTPS 和 SSH 连接的安全凭据是在 IAM 中为每个用户定义的。亲提示:如果需要温习 IAM，参见[海伦·安德森](https://dev.to/helenanders26)的优秀指南:
     [![helenanders26 image](img/0617c0c273f392c72909b5bbe8151430.png)](/helenanders26) 
     [## AWS 系列：你不知道我是谁吗？

    ### 海伦 安德森 3 月 16 日 194 分钟阅读

    #初学者
    # AWS
    #建筑师](/helenanders26/aws-series-dont-you-know-who-iam-26b0) 

## 为什么？

在 [FAQ](https://aws.amazon.com/codecommit/faqs/) 中发现了一个小亮点，询问代码提交与版本化的 S3 桶相比如何。在 S3 存储桶上启用版本控制意味着当您上传一个已经存在的文件时，旧版本将被替换，但是您仍然可以通过版本历史查看和下载它(参见屏幕截图)。

[![](img/be3716b4caa8075fd2400968944e69cb.png)](/img/devops-s3-versioning.png)

虽然这对于恢复到代码的前一个版本可能没问题，但它是基于每个文件来处理的。现在想象一下，试图跟踪组成代码版本的所有文件？

这实际上是一个反对版本控制系统(VCS)和文件版本控制的论点。创建 VCS 是为了简化源代码中的变更跟踪。

提示:对 S3 有点生疏了？是的海伦又罩着你了。

[![helenanders26 image](img/0617c0c273f392c72909b5bbe8151430.png)](/helenanders26) [## AWS 系列:桶里是什么？

### 海伦·安德森 3 月 26 日 194 分钟阅读

#beginners #aws #architect](/helenanders26/aws-series-whats-in-the-bucket-1k3)

## 什么时候？

SDLC 自动化:`[CodeCommit] -> ???`

## 如何？

这大致基于用户指南的[入门](https://docs.aws.amazon.com/codecommit/latest/userguide/getting-started.html)部分。

主要区别在于，我将使用 CLI 而不是 Web UI 来帮助学习这些命令。

创建回购`aws codecommit create-repository --repository-name hello-codecommit`

```
{  "repositoryMetadata":  {  "accountId":  "123456",  "repositoryId":  "dc2c437d-e850-4a8b-82e7-7ad85fee99d1",  "repositoryName":  "hello-codecommit",  "lastModifiedDate":  1553613552.003,  "creationDate":  1553613552.003,  "cloneUrlHttp":  "https://git-codecommit.xxx.amazonaws.com/v1/repos/hello-codecommit",  "cloneUrlSsh":  "ssh://git-codecommit.xxx.amazonaws.com/v1/repos/hello-codecommit",  "Arn":  "arn:aws:codecommit:xxx:1234567890:hello-codecommit"  }  } 
```

我们将使用 SSH 与我们的新 repo 通信，因此让我们设置一个专用密钥(hello-codecommit)并将公钥上传到我们的 IAM 帐户。

```
ssh-keygen -b 4096 -f hello-codecommit-ssh
# output has been omitted
aws iam upload-ssh-public-key --user booyaa \
  --ssh-public-key-body "$(cat hello-codecommit-ssh.pub)" 
```

```
{  "SSHPublicKey":  {  "UserName":  "booyaa",  "SSHPublicKeyId":  "VALUE_OF_SSHPUBLICKEYID",  "Fingerprint":  "FINGERPRINT",  "SSHPublicKeyBody":  "REDACTED",  "Status":  "Active",  "UploadDate":  "2019-03-26T15:46:14Z"  }  } 
```

记下您的`SSHPublicKeyId`，我们需要在您的 ssh 配置中添加新条目(`~/.ssh/config` ):

```
Host git-codecommit.*.amazonaws.com
User VALUE_OF_SSHPUBLICKEYID
IdentityFile /path/to/hello-codecommit-ssh 
```

让我们克隆我们的空 repo，您可以通过查看运行`aws codecommit create-repository`的输出来找到 Git URL。键名是`cloneUrlSsh`。

```
git clone ssh://git-codecommit.xxx.amazonaws.com/v1/repos/hello-codecommit
Cloning into 'hello-codecommit'...
# omitting boring git stuff
warning: You appear to have cloned an empty repository. 
```

让我们进入新的回购协议，添加一个文件，提交并将其推回。

```
cd hello-codecommit
echo "Hello world" > index.html
git add index.html
git commit -m 'initial commit'
git push 
```

接下来，让我们创建一个特性分支，这样我们就可以使用拉请求特性。

```
git checkout -b feature/index-markup 
```

将内容`index.html`替换为以下内容:

```
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  Hello world
</head>
<body>
  Hello world
</body>
</html> 
```

```
git commit -am '(feat) use markup'
git push -u origin feature/index-markup
aws codecommit create-pull-request \
  --title "Feature: use markup for the index page" \
  --description "What: switch from plain text to markup. Why: markup allows for a richer web experience" \
  --client-request-token booyaa-markup-1234 \
  --targets repositoryName=hello-codecommit,sourceReference=feature/index-markup 
```

`client-request-token`开关是我们随机生成的一个令牌，API 使用它来确保请求不会随着参数的改变而重复。如果再次运行该命令，您将获得前一个事务的输出，而不是新的 pull 请求。

`targets`开关需要存储库名称(hello-codecommit)和您提出拉请求的分支(feature/index-markup)。

最后让我们把`aws codecommit delete-repository --repository-name hello-codecommit-ui`拆了，如果你成功了，你会拿回`repositoryId`。

## API 和 CLI 特性和动词

### 特性

*   贮藏室ˌ仓库
*   树枝
*   文件/文件夹
*   提交/差异/Blob
*   拉动请求
*   评论
*   引发

### 动词(CRUD)

*   创造
*   批处理-获取/获取/列出/描述
*   更新/上传
*   删除

### 离群值

*   测试库触发器
*   快进合并拉取请求

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*