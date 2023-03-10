# 如何跨平台后端移动单个资源

> 原文：<https://dev.to/sammyers/how-to-move-individual-resources-across-terraform-backends-52ol>

Terraform 有一些难以置信的内置功能来改变其配置的存储位置，但有时无法回避进行状态手术的需要。

# 场景

在这种情况下，我有一个带有一些地形配置的 repo。后来那个回购变成了一个模块而不是一个独立的回购。

简单的方法是`terraform destroy`旧资源和`terraform apply`新模块。

然而，有些资源我想保留(S3 水桶，KMS 钥匙...)

## 生命周期规则

在我们继续之前，请绝对确保您要移动的资源不会被意外删除。

将以下内容添加到任何资源中，以防止 Terraform 删除它。

```
lifecycle {
  prevent_destroy = true
} 
```

如果您在跟踪这些资源时发出`terraform destroy`，Terraform 将不会允许您继续。

# 此举

攻击的基本方案是用`terraform state rm`让源地形停止跟踪资源，用`terraform import`让目的地地形开始。

在源和目标 Terraform repos 中打开一个终端。

在这个例子中，我们正在移动一个 KMS 键。

```
resource "aws_kms_key" "foo" {
  lifecycle {
    prevent_destroy = true
  }
} 
```

这在源平台中称为`aws_kms_key.foo`，在目的地称为`module.example-module.aws_kms_key.foo`。

请注意，由于 repo 是目标中的一个模块，因此其名称带有前缀`module.${MODULE_NAME}.`

## 从源头获取其状态

在源回购中

```
source> terraform state show aws_kms_key.foo

id                  = aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee
arn                 = arn:aws:kms:us-west-2:REDACTED:key/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee
enable_key_rotation = false
is_enabled          = true
key_id              = aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee
key_usage           = ENCRYPT_DECRYPT
policy              = ... 
```

具体来说，我们要寻找的是第一行，即`id`。

或者为了简单起见，`terraform state show aws_kms_key.foo | head -n1`。

我们可以告诉 Terraform 在这一点上停止跟踪它，但是为了安全起见，让我们在导入之后再这样做。

## 将其导入目的地

在您的目标回购中

```
destination> terraform import module.example-module.aws_kms_key.foo aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee

module.example-module.aws_kms_key.foo: Importing from ID "aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee"...
module.example-module.aws_kms_key.foo: Import complete!
  Imported aws_kms_key (ID: aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee)
module.example-module.aws_kms_key.foo: Refreshing state... (ID: aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee)

Import successful! 
```

如果这一步给你带来任何麻烦，Terraform 文档总是会给出一个如何导入的例子。

## 从源头上清除状态

回到你的源 repo，`rm`来自状态的资源。这将使其免于任何未来的`destroy`操作。

```
source> terraform state rm aws_kms_key.foo

1 items removed.
Item removal successful. 
```

# 总结

就是这样。对所有关键资源重复上述步骤。

最后，你得到了旧的回购协议和新的回购协议。