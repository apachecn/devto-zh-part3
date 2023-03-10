# 默认情况下启用 AWS EBS 加密

> 原文：<https://dev.to/akloya/enable-ebs-encryption-by-default-5ala>

基于 [Jeff Barr 最近的博客](https://aws.amazon.com/blogs/aws/new-opt-in-to-default-encryption-for-new-ebs-volumes/?utm_source=feedburner&utm_medium=feed&utm_campaign=Feed%3A+AmazonWebServicesBlog+%28Amazon+Web+Services+Blog%29) AWS 让您更容易、更简单地保护您的数据免受未经授权的访问。我觉得任何使用 AWS 的人都应该启用它。

如果您更喜欢通过 AWS CLI 进行，请确保您已更新到 CLI 的最新版本[AWS-CLI/1 . 16 . 169 Python/2 . 7 . 10 Darwin/17 . 7 . 0 boto core/1 . 12 . 159]

```
aws ec2 enable-ebs-encryption-by-default 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:您必须在您操作的所有地区运行此命令。

下面是 python 脚本，可以帮助您在您感兴趣的地区使用下面的

```
import boto3

# list the regions you are interested to run this script on
regions = ['us-east-1']

for region in regions:
    client = boto3.client('ec2', region)
    response = client.enable_ebs_encryption_by_default()
    print("Default EBS Encryption for region", region,": ",  response['EbsEncryptionByDefault']) 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:共享脚本将使用默认的 ebs 密钥。如果你对使用不同的 KMS 键感兴趣，那么使用下面的

```
response = client.modify_ebs_default_kms_key_id(
    KmsKeyId='string'
) 
```

Enter fullscreen mode Exit fullscreen mode