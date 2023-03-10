# 对 AWS 中 S3 时段的交叉帐户访问

> 原文：<https://dev.to/shnere/cross-account-access-to-s3-buckets-in-aws-224f>

有些情况下，您可能希望在不同的 AWS 帐户中读取/写入 S3 存储桶，这可以通过对 S3 存储桶策略进行一些调整并更新用户策略来轻松实现。

### 场景

[![Cross-account access overview](img/08157408561dfe872396f9b91d929eb5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NFz5Ayev--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0qapyvv1ym2f3l4jvg3p.png)

让我们想一个可能的场景，我们将假设我们有两个 AWS 账户， **AWS 账户 A** 和 **AWS 账户 B** 。

您在**帐户 A** 中已经有了一个访问密钥，并且想要将文件写入到**帐户 B** 中的一个或多个 S3 存储桶，您更愿意继续使用用于写入多个帐户的同一个访问密钥，而不是在**帐户 B** 中创建一个新的访问密钥，因为这可能会使我们的代码变得复杂，并使密钥轮换更加复杂。

您想这样做的原因可能还有很多，但最重要的是能够用一个用户/访问键访问多个 AWS 帐户中的存储桶。

### 先决条件

*   拥有多个 AWS 帐户
*   更新帐户 A 中**的存储桶策略的权限**
*   允许更新帐户 B 中的用户策略
*   **账户 B** 中用户的 ARN，在 CLI 中调用`aws sts get-caller-identity`即可找到

### 入门

例如，我们将授予**帐户 B** 中的用户对**帐户 A** 中的 S3 存储桶的读写权限。

#### 将存储桶策略附加到帐户 A 存储桶，授予对帐户 B 的访问权限

第一步是将一个存储桶策略附加到我们想要访问的存储桶，您可以在存储桶的 permissions 部分添加该策略，`bucket name > permissions > bucket policy`:

添加以下策略，用您的用户 ARN 填充`{ACCOUNT_B_USER_ARN}`,并随意将`Action`设置为您希望允许的操作。

```
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Sid": "Read and Write Account Permissions",
         "Effect": "Allow",
         "Principal": {
            "AWS": "{ACCOUNT_B_USER_ARN}"
         },
         "Action": [
            "s3:PutObjectAcl",
            "s3:PutObject",
            "s3:List*",
            "s3:Get*"
         ],
         "Resource": [
            "arn:aws:s3:::account_a_bucket"
         ]
      }
   ]
} 
```

通过这个策略，我们让 bucket 知道一个新用户将拥有给定的权限。在我们的例子中，向`account_a_bucket`添加、获取和列出对象。

现在我们测试我们的新权限，您可以尝试使用控制台列出对象:

```
aws s3 ls s3://account_a_bucket --profile AccountBUser 
```

您应该会得到一个`AccessDenied`错误。这很好，这意味着我们仍然需要通过策略向`Account B User`授予权限。

#### 给账户 B 用户添加策略

为了访问`account_a_bucket`，我们将**帐户 B** 中的一个策略附加到**帐户 B** 用户

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "",
            "Effect": "Allow",
            "Action": [
                "s3:PutObjectAcl",
                "s3:PutObject",
                "s3:List*",
                "s3:Get*"
            ],
            "Resource": [
                "arn:aws:s3:::account_a_bucket/*",
                "arn:aws:s3:::account_a_bucket"
            ]
        }
    ]
} 
```

保存并再次测试，您现在应该可以访问`account_a_bucket`

```
aws s3 ls s3://account_a_bucket --profile AccountBUser 
```

### 最佳实践

在前面的示例中，我们将存储桶策略中的权限直接授予用户，但更好的做法是将存储桶策略中的权限授予帐户 B 的**中的`root`，这样根用户可以使用策略将所需的访问权限授予其帐户中的用户，而不需要对帐户 A** 的**进行任何更改**

[![Overview with root granting access](img/2de8f6d99c8405c2c846bae3ff66f1eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sYI9G43B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iwas5548xihvcg8cig22.png)

### 附加文档

[AWS:桶所有者授予跨帐户桶权限](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-walkthroughs-managing-access-example2.html)