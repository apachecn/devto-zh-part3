# Crib-Notes:使用内嵌策略的实例角色的跟踪权限

> 原文：<https://dev.to/ferricoxide/crib-notes-tracing-permissions-for-an-instance-role-that-uses-inline-policies-54p1>

这篇文章的基本原理与[上一个主题](https://dev.to/ferricoxide/crib-notes-tracing-permissions-for-an-instance-role-2joo)中关于跟踪使用托管策略的实例角色中的 IAM 权限的基本原理基本相同。所以我会抄袭剩下的介绍...

当使用 AWS EC2 实例时——特别是当自动化其部署和生命周期时——通常使用 AWS IAM 系统的实例角色策略特性。有时，您可能会被问到，“该实例被赋予了什么权限。”要回答这个问题，您可以使用 AWS 的 IAM web 控制台或 IAM CLI。在前一种情况下，要将信息发送给请求者，您可以选择截屏，或者尝试将 UI 中的文本复制并粘贴到您的电子邮件/slack/等。回复。在后一种情况下，您可以直接转储列举权限的 JSON 格式的策略文档。

通常，我更喜欢 CLI 选项，因为我不必担心“接收者需要对结果做什么”。只需转储一个文本文件，我就不必担心被指责屏幕截图的内容不能用于轻松创建另一个类似的策略。也很容易将输出重定向到一个文件，并将其附加到我响应的任何媒体上...或者，如果在 CLI 环境中启用了邮件，只需将输出直接传输到基于 CLI 的电子邮件工具，从而节省流程中的一个步骤(为胜利而懒惰！).

但是，如何从“这个帐户中有一个实例:它有什么特权”变成“它能做什么？”。基本上，这是一个三步走的过程:

1.  获取附加到 EC2 实例的实例角色的名称。这可以通过类似于以下的方法来完成:

```
$ aws ec2 describe-instances --instance-id <INSTANCE_ID> \
   --query 'Reservations[].Instances[].IamInstanceProfile[].Arn[]' --out text | \
  sed 's/^.\*arn:.\*profile\///' 
```

1.  获取附加到角色的内联策略列表。这可以通过类似于以下的方法来完成:

```
$ aws iam list-role-policies --role-name <OUPUT_FROM_PREVIOUS> 
```

1.  获取与实例角色的内联策略相关联的权限列表。这可以通过类似于以下的方法来完成:

```
$ aws iam get-role-policy --role-name INSTANCE --policy-name <OUPUT_FROM_PREVIOUS> 
```

以上步骤将转储出查询的内联策略的*的完整 IAM 策略/权限:*

*   如果内联策略是附加到角色的唯一的*策略，那么输出将向*显示实例角色授予它所附加的任何 ec2 的所有*权限。*
*   如果内联策略是*而不是*附加到角色的唯一内联策略，则有必要迭代附加到角色的其余策略以获得聚集的权限集。

为了便于迭代，可以使用类似下面的脚本来封装前面流程描述中的第二步和第三步:

```
#!/bin/bash
#
# Script to dump out all permissions granted through an IAM role with multiple
# inline IAM policies attached.
###########################################################################

if [[ $# -eq 0 ]]
then
   echo "Usage: ${0} <instance-id>" >&2
   exit 1
fi

PROFILE_NAME=$( aws ec2 describe-instances --instance-id "${1}" \
   --query 'Reservations[].Instances[].IamInstanceProfile[].Arn' --out text | \
  tr '\t' '\n' | sort -u | sed 's/^.*arn:.*profile\///' )

POLICY_LIST_RAW=$( aws iam list-role-policies --role-name ${PROFILE_NAME} )
POLICY_LIST_CLN=($( echo ${POLICY_LIST_RAW} | jq .PolicyNames[] | sed 's/"//g' ))

for ITER in $( seq 0 $(( ${#POLICY_LIST_CLN[@]} - 1 )) )
do
  aws iam get-role-policy --role-name "${PROFILE_NAME}" \
    --policy-name "${POLICY_LIST_CLN[${ITER}]}"
done 
```