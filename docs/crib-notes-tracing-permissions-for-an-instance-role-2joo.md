# 备注:使用托管策略的实例角色的跟踪权限

> 原文：<https://dev.to/ferricoxide/crib-notes-tracing-permissions-for-an-instance-role-2joo>

当使用 AWS EC2 实例时——特别是当自动化其部署和生命周期时——通常使用 AWS IAM 系统的[实例角色策略](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html)特性。有时，您可能会被问到，“该实例被赋予了什么权限。”要回答这个问题，您可以使用 AWS 的 IAM web 控制台或 IAM CLI。在前一种情况下，要将信息发送给请求者，您可以选择截屏，或者尝试将 UI 中的文本复制并粘贴到您的电子邮件/slack/等。回复。在后一种情况下，您可以直接转储列举权限的 JSON 格式的策略文档。

通常，我更喜欢 CLI 选项，因为我不必担心“接收者需要对结果做什么”。只需转储一个文本文件，我就不必担心被指责屏幕截图的内容不能用于轻松创建另一个类似的策略。也很容易将输出重定向到一个文件，并将其附加到我响应的任何媒体上...或者，如果在 CLI 环境中启用了邮件，只需将输出直接传输到基于 CLI 的电子邮件工具，从而节省流程中的一个步骤(为胜利而懒惰！).

但是，如何从“这个帐户中有一个实例:它有什么特权”变成“它能做什么？”基本上，这是一个四步的过程:

1.  获取附加到 EC2 实例的实例角色的名称。这可以通过类似于以下的方法来完成:

```
$ aws ec2 describe-instances --instance-id <INSTANCE_ID> \
    --query 'Reservations[].Instances[].IamInstanceProfile[].Arn[]' --out text | \
  sed 's/^.\*arn:.\*profile\///'
$ aws iam get-instance-profile --instance-profile-name <OUPUT_FROM_PREVIOUS> \
    --query 'InstanceProfile.Roles[].RoleName' --out text 
```

1.  获取附加到角色的策略列表。这可以通过类似于以下的方法来完成:

```
aws iam list-attached-role-policies --role-name <OUPUT_FROM_PREVIOUS> \
    --query 'AttachedPolicies[].PolicyArn[]' --out text 
```

1.  查找附加策略的当前版本。这可以用类似于以下的方法来完成:

```
aws iam get-policy --policy-arn <OUPUT_FROM_PREVIOUS> \
    --query 'Policy.DefaultVersionId' --out text 
```

1.  获取附加策略/策略活动版本的内容。这可以通过使用步骤#2 和#3 的输出来完成，方法类似于:

```
aws iam get-policy-version \
     --policy-arn <OUPUT_FROM_STEP_2> \
     --version-id <OUPUT_FROM_STEP_3> 
```

上述步骤将转储查询的受管策略的完整 IAM 策略/权限:

*   如果内联策略是附加到角色的唯一策略，输出将显示实例角色授予它所附加的任何 ec2 的所有权限。
*   如果内联策略不是附加到角色的唯一内联策略，则有必要迭代附加到角色的其余策略，以获得聚集的权限集。

为了便于迭代，可以使用类似于下面的脚本来封装前面流程描述中的步骤:

```
#!/bin/bash
#
# Script to dump out all permissions granted through an IAM role with multiple
# managed IAM policies attached.
###########################################################################

if [[ $# -eq 0 ]]
then
   echo "Usage: ${0} <instance-id>" >&2
   exit 1
fi

PROFILE_NAME="$( aws ec2 describe-instances --instance-id "${1}" \
  --query 'Reservations[].Instances[].IamInstanceProfile[].Arn[]' \
  --out text | sed 's/^.*arn:.*profile\///' )"
ROLE_NAME="$( aws iam get-instance-profile \
  --instance-profile-name "${PROFILE_NAME}" \
  --query 'InstanceProfile.Roles[].RoleName' \
  --out text | sed 's/^.*arn:.*profile\///' )"
ATTACHED_POLICIES=($( aws iam list-attached-role-policies \
  --role-name "${ROLE_NAME}" --query 'AttachedPolicies[].PolicyArn[]' | \
  jq .[] | sed 's/"//g' ))

for ITER in $( seq 0 $(( ${#ATTACHED_POLICIES[@]} - 1 )) )
do
   POLICY_VERSION=$( aws iam get-policy --policy-arn \
     ${ATTACHED_POLICIES[${ITER}]} --query \
     'Policy.DefaultVersionId' --out text )
   aws iam get-policy-version --policy-arn ${ATTACHED_POLICIES[${ITER}]} \
     --version-id "${POLICY_VERSION}"
done 
```