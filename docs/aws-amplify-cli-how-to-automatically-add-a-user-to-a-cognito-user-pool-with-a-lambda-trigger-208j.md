# AWS Amplify CLI，如何使用 Lambda 触发器自动将用户添加到 Cognito 用户池

> 原文：<https://dev.to/beavearony/aws-amplify-cli-how-to-automatically-add-a-user-to-a-cognito-user-pool-with-a-lambda-trigger-208j>

# 在我们的 AWS Amplify CLI 项目中处理认知用户群

AWS Amplify Transform 提供了一个`@auth`指令。有了它，我们就有可能基于静态或动态组授予对我们的 API 的某些部分的访问权。详见[文档](https://aws-amplify.github.io/docs/cli/graphql#auth)。

假设我们有任务 api，我们想要:

1.  每个用户登录使用我们的应用程序，并授予我们的 API 读取权限
2.  拥有经理，可以额外创建和更新任务
3.  有了管理员，他们可以做任何事情

我们不会讨论 API 和`@auth`指令，而是解决这个问题，在注册时，我们希望所有用户都在一个名为*‘用户’*的组中。经理和管理员将在 AWS 管理控制台中手动添加到组中，但每个用户至少属于**“用户”**组。

我们希望我们的代码在不离开 Amplify CLI 的情况下尽可能多地处理基础设施。

让我们看看如何在每个环境堆栈中实现自动化:

1.  创建所有用户组(“用户”、“经理”、“管理员”)
2.  添加一个触发函数，自动将用户添加到组中
3.  允许功能将用户添加到组中
4.  在注册后，在 Cognito 用户池中配置一个触发器来运行该功能

## 自定义云生成模板

使用 Amplify CLI 添加或修改类别时，有些模板不安全，会被覆盖。我们可以安全地将定制资源添加到`amplify/backend/api/${api-name}/stacks/CustomResources.json`文件，或者将其他 json 文件添加到这个目录。这些栈是 api 类别的一部分，但是可以用来创建 CloudFormation 支持的任何东西。重点是**创造**！我们可以引用后端模板的其他部分，但是我们不能更改它们(至少就我们所知)。

我们需要改变`amplify/backend/auth/cognitoxxxx/cognitoxxxx-cloudformation-template.yml`，它将被类似
的操作覆盖

```
amplify update auth
amplify add analytics 
```

因此，我们需要准备好多次重做我们的更改。在源代码控制中检入我们的代码会帮助我们做到这一点。

## 生成组

我们希望将三个组添加到我们的用户池。

*   管理员
*   经理
*   用户

如果我们不介意重做更改，当 Amplify CLI 更新该文件时，我们可以编辑**‘auth’**cloud formation 模板。

在`amplify/backend/auth/cognitoxxxx/cognitoxxxx-cloudformation-template.yml`中，在`Outputs :`之前的末尾加上

```
Resources:
  ## Other resources ...
  ## ...
  UserPoolGroupAdmins:
    Type: 'AWS::Cognito::UserPoolGroup'
    Properties:
      GroupName: Admins
      UserPoolId: !Ref UserPool

  UserPoolGroupManagers:
    Type: 'AWS::Cognito::UserPoolGroup'
    Properties:
      GroupName: Managers
      UserPoolId: !Ref UserPool

  UserPoolGroupUsers:
    Type: 'AWS::Cognito::UserPoolGroup'
    Properties:
      GroupName: Users
      UserPoolId: !Ref UserPool

Outputs: 
```

否则，我们可以走安全路线，使用`amplify/backend/api/${api-name}/stacks/CustomResources.json`文件来创建这些组。

将以下内容添加到*“资源”*部分，紧接在*“企业资源”* :

```
 "Resources":  {  "EmptyResource":  {  "Type":  "Custom::EmptyResource",  "Condition":  "AlwaysFalse"  },  "UserPoolGroupAdmins":  {  "Type":  "AWS::Cognito::UserPoolGroup",  "Properties":  {  "GroupName":  "Admins",  "UserPoolId":  {  "Ref":  "AuthCognitoUserPoolId"  }  }  },  "UserPoolGroupManagers":  {  "Type":  "AWS::Cognito::UserPoolGroup",  "Properties":  {  "GroupName":  "Managers",  "UserPoolId":  {  "Ref":  "AuthCognitoUserPoolId"  }  }  },  "UserPoolGroupUsers":  {  "Type":  "AWS::Cognito::UserPoolGroup",  "Properties":  {  "GroupName":  "Users",  "UserPoolId":  {  "Ref":  "AuthCognitoUserPoolId"  }  }  }  } 
```

这样做的缺点是，它会显示为好像是 ***api*** 类别发生了变化，而不是 ***auth*** 类别发生了变化。

## 在注册时使用 Lambda 触发器自动将用户添加到 Cognito 用户池组

既然我们已经自动创建了 Cognito-UserPool-Groups，我们希望在注册时将用户分配到其中一个组。

Cognito 在特定的生命周期事件中提供触发器。其中一个触发器是“后确认”，它可以在用户被成功确认后运行 Lambda 函数。

## 添加新的 Lambda 函数

AWS Amplify CLI 提供了向我们的项目添加功能的能力:

```
amplify add function 
```

选择名称*‘addUserToGroup’*，当被要求友好的名称和 Lambda 函数名称时，作为模板，我们需要 *Hello world 函数*。

更改我们新创建的函数的文件`index.js`。

```
exports.handler = function afterConfirmationTrigger(event, context, callback) {
  const AWS = require('aws-sdk');
  const cognitoISP = new AWS.CognitoIdentityServiceProvider({
    apiVersion: '2016-04-18'
  });

  const params = {
    GroupName: 'Users',
    UserPoolId: event.userPoolId,
    Username: event.userName
  };

  cognitoISP
    .adminAddUserToGroup(params)
    .promise()
    .then(() => callback(null, event))
    .catch(err => callback(err, event));
}; 
```

在`event.json`中，我们可以根据自己的喜好改变有效载荷的内容来测试功能。

```
{  "userPoolId":  "the-user-pool-id",  "userName":  "user1",  "userPoolGroupName":  "Users"  } 
```

要运行测试，请使用:

```
amplify invoke function addUserToGroup 
```

这还不行。因为我们需要给函数权限去做我们想让它做的事情。

## 添加 IAM 策略以允许将用户添加到组中

为了让我们的新功能能够将用户添加到组中，需要附加一个策略。这次我们需要编辑 Lambda 函数的 CloudFormation 模板。这应该是相当安全的，因为其他类别通常不会对这个模板产生副作用。

我们需要指定用户池目标的资源 ARN。我们可以使用通配符或使用参数来获得用户池的确切 ARN。

### 不带参数

我们编辑文件`amplify/backend/function/addUserToGroup/addUserToGroup-cloudformation-template.json`并添加到数组`lambdaexecutionpolicy.Properties.PolicyDocument.Statement`

```
{  "Effect":  "Allow",  "Action":  ["cognito-idp:AdminAddUserToGroup"],  "Resource":  {  "Fn::Sub":  [  "arn:aws:cognito-idp:${region}:${account}:userpool/${region}*",  {  "region":  {  "Ref":  "AWS::Region"  },  "account":  {  "Ref":  "AWS::AccountId"  }  }  ]  }  } 
```

这很简单，并且在一个地方，但是将给予该函数访问我们区域中所有用户池的权限。

### 具有参数和最小权限

要定位单个用户池 istead，我们需要知道它的 ARN。为了获得访问权限，我们创建一个参数并将其传递给我们的 CloudFormation 模板。

我们创建一个新文件`amplify/backend/function/addUserToGroup/parameters.json`,并将用户池的 ARN 添加到其中。我们可以看看`amplify/backend/api/${api-name}/parameters.json`中的 **authcognitoname** 。

```
{  "AuthCognitoUserPoolArn":  {  "Fn::Sub":  [  "arn:aws:cognito-idp:${AWS::Region}:${AWS::AccountId}:userpool/${id}",  {  "id":  {  "Fn::GetAtt":  ["--authcognitoname--",  "Outputs.UserPoolId"]  }  }  ]  }  } 
```

为了将参数传递给 CloudFormation 模板，我们需要编辑`amplify/backend/function/addUserToGroup/addUserToGroup-cloudformation-template.json`。

首先，我们必须定义参数。我们通过向“参数”数组添加以下内容来实现这一点:

```
{  "Parameters":  {  //  ...  other  parameters  "AuthCognitoUserPoolArn":  {  "Type":  "String"  }  }  } 
```

然后我们向`lambdaexecutionpolicy.Properties.PolicyDocument.Statement`添加一个语句，该语句引用**“AuthCognitoUserPoolArn”**参数并允许动作**“cognito-IDP:AdminAddUserToGroup”**:

```
{  "Effect":  "Allow",  "Action":  ["cognito-idp:AdminAddUserToGroup"],  "Resource":  {  "Ref":  "AuthCognitoUserPoolArn"  }  } 
```

我们可以运行`amplify push`来部署函数，并通过转到 Lambda 管理控制台并找到我们的函数来检查策略是否添加成功。如果我们点击**“Amazon Cognito 用户池”**框，我们可以查看它下面的政策。

## 添加 Lambda 后确认-触发

要将 Lambda 函数分配给 Cognito-UserPool-Trigger，我们需要编辑**‘auth’**cloud formation 模板。

> 这可能会在某些 Amplify CLI 操作中被覆盖，但我们看不到其他选择。

在`amplify/backend/auth/cognitoxxxx/cognitoxxxx-cloudformation-template.yml`中，我们在该部分的某处添加了我们的触发器:

```
# BEGIN USER POOL RESOURCES
UserPool:
  # Created upon user selection
  # Depends on SNS Role for Arn if MFA is enabled
  Type: AWS::Cognito::UserPool
  UpdateReplacePolicy: Retain
  Properties:
    # Other properties ...
    # Add this:
    LambdaConfig:
      PostConfirmation: !Sub
        - arn:aws:lambda:${region}:${account}:function:addUserToGroup-${env}
        - {
            region: !Ref 'AWS::Region',
            account: !Ref 'AWS::AccountId',
            env: !Ref env,
          } 
```

为了将更改部署到我们的用户池，我们运行

```
amplify push 
```

## 检查我们的工作

一切正常后，我们可以登录 AWS 管理控制台并检查:

*   Cognito 用户池应该已经创建了我们的三个组
*   有一个后确认触发器指向我们的 addUserToGroup Lambda 函数
*   Lambda 函数是在我们的环境中创建的
*   该函数附加了正确的策略，允许它将用户添加到我们的用户池中的组

为了看到它的运行，我们在我们选择的 Amplify CLI 应用程序中注册了一个新用户，并观察这个新用户是如何自动添加到**‘Users’**cogn ITO-UserPool-Group 的。

## 重述

我们在 api 类别的 CustomResources.json 中创建了 UserPool-Groups，添加了一个 Lambda 函数，附加了一个访问用户池的策略，并编辑了我们的 Cognito CloudFormation 模板，以定义一个执行我们新的 Lambda 函数的后确认触发器。现在，每次用户注册时，它将被分配到一个组。

随着 Amplify CLI 的不断发展，我们应该会看到这方面的帮助。所以这个程序可能会在短时间内变得无关紧要。