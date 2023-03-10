# 使用 AWS CDK 为无服务器微服务构建 CICD 管道

> 原文：<https://dev.to/paulswail/building-cicd-pipelines-for-serverless-microservices-using-the-aws-cdk-1o7d>

*这是系列文章的第 5 部分[将单一的 SaaS 应用程序迁移到无服务器——决策日志](https://winterwindsoftware.com/serverless-migration-journal/)。*

为了确保我从传统 Express.js API 迁移到 Lambda 的每条新路径在发布到产品之前都经过彻底的测试，我已经开始实施一个 CICD 过程。

我对 CICD 进程的主要目标是:

*   每个无服务器服务都有自己的管道，因为每个都是独立部署的。
*   合并到 master 应该会触发主管道，该管道将执行从开发到生产的测试和部署。
*   CICD 管道将在开发 AWS 帐户托管，但必须能够部署到其他 AWS 帐户。
*   所有 CICD 配置都将通过基础设施即代码来完成，因此我可以在创建新服务时轻松设置管道。控制台访问将是严格只读的。

我已经决定使用 AWS [CodePipeline](https://aws.amazon.com/codepipeline/) 和 [CodeBuild](https://aws.amazon.com/codebuild/) 来创建管道，因为它们是无服务器的，并且内置了 IAM 支持，我可以使用它来安全地部署到 prod 服务器。CodePipeline 充当编排器，而 CodeBuild 充当任务运行器。CodePipeline 管道中的每个阶段都包含一个或多个调用 CodeBuild 项目的操作，该项目包含创建发布包、运行测试、进行部署等实际命令。

## 管道概述

对于我的 CICD 流程的第一个版本，我将部署一个单独的服务 [`ac-rest-api`](https://winterwindsoftware.com/serverless-migration-journal-part3/) (它目前只包含一个单独的测试 Lambda 函数+ API GW 路由)。该管道将按如下方式工作:

1.  每当代码被推送到托管我的`ac-rest-api`服务的 repo 的主分支时，就会触发 GitHub 源动作。
2.  运行 lint +单元/集成测试，并构建部署包(使用`sls package`命令)
3.  将包(使用`sls deploy`)部署到开发阶段，并对其运行验收测试。
4.  将包部署到试运行阶段，并对其运行验收测试。
5.  将包部署到生产阶段，并对其运行验收测试。

下面是我完成的管道的样子:

[![CodePipeline stages](img/37dbc7af2a4bec32b57a285e27b0ed89.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2HkN_bLG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://winterwindsoftware.com/img/blog-images/codepipeline-stages.png)

## 将管道作为基础设施进行管理

与大多数 AWS 服务一样，很容易找到关于如何使用 AWS 控制台创建代码管道的教程，但很难找到详细的基础设施代码示例。CodeBuild 在这方面做得更好，因为文档似乎一般都推荐在 repo 中使用一个`buildspec.yml`文件来指定构建命令。

由于在迁移过程中，我将为我确定的每个微服务创建几个管道，所以我想要一些我可以轻松重用的东西。

我考虑了这些定义我的管道的选项:

1.  使用原始云形成 YAML 文件。
2.  在 serverless.yml 的资源部分使用 CloudFormation。
3.  使用新的 [AWS 云开发工具包(CDK)](https://github.com/awslabs/aws-cdk) ，它允许您使用流行语言(Javascript/Typescript、Java、C#)定义高级基础架构构造，从而在底层生成云构建堆栈。

我对 CloudFormation 比较精通，但我发现它的开发体验非常令人沮丧，过去我花了几天时间来构建和运行复杂的 CloudFormation 堆栈。我对它的主要不满是:

*   创作和看到部署完成(失败或成功)之间的缓慢反馈循环
*   缺少模板/模块，这使得在不复制和粘贴大量样板文件的情况下很难重用(我知道像对流层这样的工具在这方面有所帮助，但我是一名 Javascript 开发人员，不想学习一门新语言(Python))
*   我总是需要在浏览器选项卡中打开文档，以查看资源的可用属性

有鉴于此，我决定继续使用 CDK，这(理论上)应该有助于减轻所有这些投诉。

## 创建我的 CDK 应用

CDK 提供了一个用于部署“应用程序”的 CLI。在这种情况下，应用程序实际上是一个资源树，您可以使用面向对象的编程模型来构建它，树中的每个节点都称为一个“构造”。CDK 为所有主要的 AWS 资源提供了现成的构造，也允许(并且[鼓励](https://docs.aws.amazon.com/CDK/latest/userguide/writing_constructs.html))你自己编写。一个 CDK 应用程序包含至少一个 CloudFormation 堆栈结构，它使用该结构来发起幕后部署。因此，您仍然可以获得 CloudFormation 提供的自动回滚优势。

*关于什么是 CDK 以及如何安装和引导你自己的应用程序的更详细的背景，我推荐从这里[开始](https://docs.aws.amazon.com/CDK/latest/userguide/what-is.html)。*

我还决定尝试使用 Typescript(而不是普通的 Javascript ),因为 CDK 是用 Typescript 编写的，强类型似乎非常适合具有复杂属性集的基础设施资源。

我首先创建了一个名为`ServiceCicdPipelines`的自定义顶级构造，它充当我将创建的每个服务的所有管道和任何补充资源的容器。我已经在[这个要点](https://gist.github.com/paulswail/2cdda90261e6d17506114ea00d780eb6)中包含了所有的源代码，但是它的主要元素如下:

*   `cicd-pipelines-stack`:部署所有 CICD 相关资源的单一云架构。
*   `pipelines[]`:`ServicePipeline`对象的列表——一个定制的构造，它封装了为单个无服务器服务创建单个管道的逻辑。
*   `alertsTopic`:将接收管道错误通知的 SNS 主题。

定制的`ServicePipeline`结构是大部分逻辑所在。它将一个`ServiceDefinition`对象作为参数，看起来像这样:

```
export interface ServiceDefinition {
    serviceName: string;
    githubRepo: string;
    githubOwner: string;
    githubTokenSsmPath: string;
    /** Permissions that CodeBuild role needs to assume to deploy serverless stack */
    deployPermissions: PolicyStatement[];
} 
```

Enter fullscreen mode Exit fullscreen mode

该对象是定义正在部署的服务的所有唯一属性的地方。目前，我在这里没有太多的选择，但我希望随着时间的推移增加这一点。

`ServicePipeline`构造也是定义管道每个阶段的地方:

```
export class ServicePipeline extends Construct {
    readonly pipeline: Pipeline;
    readonly alert: PipelineFailedAlert;

    constructor(scope: Construct, id: string, props: ServicePipelineProps) {
        super(scope, id);
        const pipelineName = `${props.service.serviceName}_${props.sourceTrigger}`;
        this.pipeline = new Pipeline(scope, pipelineName, {
            pipelineName,
        });

        // Read Github Oauth token from SSM Parameter Store
        // https://docs.aws.amazon.com/codepipeline/latest/userguide/GitHub-rotate-personal-token-CLI.html
        const oauth = new SecretParameter(scope, 'GithubPersonalAccessToken', {
            ssmParameter: props.service.githubTokenSsmPath,
        });

        const sourceAction = new GitHubSourceAction({
            actionName: props.sourceTrigger === SourceTrigger.PullRequest ? 'GitHub_SubmitPR' : 'GitHub_PushToMaster',
            owner: props.service.githubOwner,
            repo: props.service.githubRepo,
            branch: 'master',
            oauthToken: oauth.value,
            outputArtifactName: 'SourceOutput',
        });

        this.pipeline.addStage({
            name: 'Source',
            actions: [sourceAction],
        });

        // Create stages for DEV => STAGING => PROD.
        // Each stage defines its own steps in its own build file
        const buildProject = new ServiceCodebuildProject(this.pipeline, 'buildProject', {
            projectName: `${pipelineName}_dev`,
            buildSpec: 'buildspec.dev.yml',
            deployerRoleArn: CrossAccountDeploymentRole.getRoleArnForService(
                props.service.serviceName, 'dev', deploymentTargetAccounts.dev.accountId,
            ),
        });
        const buildAction = buildProject.project.toCodePipelineBuildAction({
            actionName: 'Build_Deploy_DEV',
            inputArtifact: sourceAction.outputArtifact,
            outputArtifactName: 'sourceOutput',
            additionalOutputArtifactNames: [
                'devPackage',
                'stagingPackage',
                'prodPackage',
            ],
        });
        this.pipeline.addStage({
            name: 'Build_Deploy_DEV',
            actions: [buildAction],
        });
        const stagingProject = new ServiceCodebuildProject(this.pipeline, 'deploy-staging', {
            projectName: `${pipelineName}_staging`,
            buildSpec: 'buildspec.staging.yml',
            deployerRoleArn: CrossAccountDeploymentRole.getRoleArnForService(
                props.service.serviceName, 'staging', deploymentTargetAccounts.staging.accountId,
            ),
        });
        const stagingAction = stagingProject.project.toCodePipelineBuildAction({
            actionName: 'Deploy_STAGING',
            inputArtifact: sourceAction.outputArtifact,
            additionalInputArtifacts: [
                buildAction.additionalOutputArtifact('stagingPackage'),
            ],
        });
        this.pipeline.addStage({
            name: 'Deploy_STAGING',
            actions: [stagingAction],
        });

        // Prod stage requires cross-account access as codebuild isn't running in same account
        const prodProject = new ServiceCodebuildProject(this.pipeline, 'deploy-prod', {
            projectName: `${pipelineName}_prod`,
            buildSpec: 'buildspec.prod.yml',
            deployerRoleArn: CrossAccountDeploymentRole.getRoleArnForService(
                props.service.serviceName, 'prod', deploymentTargetAccounts.prod.accountId,
            ),
        });
        const prodAction = prodProject.project.toCodePipelineBuildAction({
            actionName: 'Deploy_PROD',
            inputArtifact: sourceAction.outputArtifact,
            additionalInputArtifacts: [
                buildAction.additionalOutputArtifact('prodPackage'),
            ],
        });
        this.pipeline.addStage({
            name: 'Deploy_PROD',
            actions: [prodAction],
        });

        // Wire up pipeline error notifications
        if (props.alertsTopic) {
            this.alert = new PipelineFailedAlert(this, 'pipeline-failed-alert', {
                pipeline: this.pipeline,
                alertsTopic: props.alertsTopic,
            });
        }
    }
}

export interface ServicePipelineProps {
    /** Information about service to be built & deployed (source repo, etc) */
    service: ServiceDefinition;
    /** Trigger on PR or Master merge?  */
    sourceTrigger: SourceTrigger;
    /** Account details for where this service will be deployed to */
    deploymentTargetAccounts: DeploymentTargetAccounts;
    /** Optional SNS topic to send pipeline failure notifications to */
    alertsTopic?: Topic;
}

/** Wrapper around the CodeBuild Project to set standard props and create IAM role */
export class ServiceCodebuildProject extends Construct {
    readonly buildRole: Role;
    readonly project: Project;

    constructor(scope: Construct, id: string, props: ServiceCodebuildActionProps) {
        super(scope, id);

        this.buildRole = new ServiceDeployerRole(this, 'project-role', {
            deployerRoleArn: props.deployerRoleArn,
        }).buildRole;

        this.project = new Project(this, 'build-project', {
            projectName: props.projectName,
            timeout: 10, // minutes
            environment: {
                buildImage: LinuxBuildImage.UBUNTU_14_04_NODEJS_8_11_0,
            },
            source: new CodePipelineSource(),
            buildSpec: props.buildSpec || 'buildspec.yml',
            role: this.buildRole,
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 管道代码组织的快速说明

我在一个共享的`autochart-infrastructure` repo 中创建了所有这些 CDK 定制构造，我用它来定义多个服务使用的共享的、跨领域的基础设施资源。我在下面介绍的 buildspec 文件与它们的服务位于同一个存储库中(这是应该的)。我对将管道定义和构建脚本放在单独的回购协议中并不完全满意，我可能会考虑将我的可重用 CDK 结构移动到共享库中的方法，并在引用该库时在特定于服务的回购协议中定义 CDK 应用的源代码。

## 编写代码构建脚本

在上面的管道定义代码中，您可能已经注意到每个部署阶段都有自己的 buildspec 文件(使用命名约定`buildspec.<stage>.yml`)。我想尽量减少管道本身的逻辑数量，让它单独负责编排。所有的逻辑都将存在于构建脚本中。

### 构建并部署到开发阶段

一旦 Github 中的主分支发生推送，下一个管道步骤就是运行测试、打包并部署到开发阶段。如下所示:

```
# buildspec.dev.yml
version: 0.2

env:
  variables:
    TARGET_REGION: us-east-1
    SLS_DEBUG: ''
    DEPLOYER_ROLE_ARN: 'arn:aws:iam::<dev_account_id>:role/ac-rest-api-dev-deployer-role'

phases:
  pre_build:
    commands:
      - chmod +x build.sh
      - ./build.sh install
  build:
    commands:
      # Do some local testing
      - ./build.sh test-unit
      - ./build.sh test-integration
      # Create separate packages for each target environment
      - ./build.sh clean
      - ./build.sh package dev $TARGET_REGION
      - ./build.sh package staging $TARGET_REGION
      - ./build.sh package prod $TARGET_REGION
      # Deploy to DEV and run acceptance tests there
      - ./build.sh deploy dev $TARGET_REGION dist/dev
      - ./build.sh test-acceptance dev

artifacts:
  files:
    - '**/*'
  secondary-artifacts:
    devPackage:
      base-directory: ./dist/dev
      files:
        - '**/*'
    stagingPackage:
      base-directory: ./dist/staging
      files:
        - '**/*'
    prodPackage:
      base-directory: ./dist/prod
      files:
        - '**/*' 
```

Enter fullscreen mode Exit fullscreen mode

这里有几点需要注意:

*   需要一个`DEPLOYER_ROLE_ARN`环境变量来定义一个预先存在的 IAM 角色，该角色有权在目标帐户中执行部署步骤。我们将在后面介绍如何设置它。
*   每个构建命令引用一个`build.sh`文件。这种间接的原因是为了在开发期间更容易测试构建脚本，因为我没有办法在本地调用 buildspec 文件(这是基于崔琰在他的生产就绪无服务器课程的 [CI/CD 模块中推荐的方法)](https://livevideo.manning.com/module/38_5_2/production-ready-serverless/ci-cd/setting-up-a-ci-cd-pipeline-for-deploying-lambda-functions?)
*   在打包之前，我运行单元和集成测试。这将在本地运行 Lambda 函数代码(在 CodeBuild 容器中),而不是通过 AWS Lambda，尽管集成测试可能会影响函数调用的现有下游 AWS 资源。
*   我在这里创建了 3 个部署工件，每个阶段一个，并将它们作为输出工件包含在内，供以后的阶段使用。我对此并不完全满意，因为它违背了 Devops 的最佳实践，即在每个阶段都有一个单一的不可变的工件流。我这样做的原因是 build.sh 文件使用的`sls package`命令需要提供给它一个特定的 stage。然而，我后来了解到[有一个解决这个](https://medium.com/@bishwash.aryal/serverless-framework-build-immutable-package-for-ci-cd-pipeline-949b49f7df91)的方法，所以我可能会很快改变它。
*   我运行验收测试(测试新部署的 API 网关端点)作为最后一步。

`build.sh`文件如下:

```
#!/bin/bash
set -e
set -o pipefail

instruction()
{
  echo "usage: ./build.sh package <stage> <region>"
  echo ""
  echo "/build.sh deploy <stage> <region> <pkg_dir>"
  echo ""
  echo "/build.sh test-<test_type> <stage>"
}

assume_role() {
  if [ -n "$DEPLOYER_ROLE_ARN" ]; then echo "Assuming role $DEPLOYER_ROLE_ARN ..."
    CREDS=$(aws sts assume-role --role-arn $DEPLOYER_ROLE_ARN \
        --role-session-name my-sls-session --out json)
    echo $CREDS > temp_creds.json
    export AWS_ACCESS_KEY_ID=$(node -p "require('./temp_creds.json').Credentials.AccessKeyId")
    export AWS_SECRET_ACCESS_KEY=$(node -p "require('./temp_creds.json').Credentials.SecretAccessKey")
    export AWS_SESSION_TOKEN=$(node -p "require('./temp_creds.json').Credentials.SessionToken")
    aws sts get-caller-identity
  fi
}

unassume_role() {
  unset AWS_ACCESS_KEY_ID
  unset AWS_SECRET_ACCESS_KEY
  unset AWS_SESSION_TOKEN
}

if [ $# -eq 0 ]; then instruction
  exit 1
elif [ "$1" = "install" ] && [ $# -eq 1 ]; then npm install elif [ "$1" = "test-unit" ] && [ $# -eq 1 ]; then npm run lint
  npm run test elif [ "$1" = "test-integration" ] && [ $# -eq 1 ]; then echo "Running INTEGRATION tests..."
  npm run test-integration
  echo "INTEGRATION tests complete."
elif [ "$1" = "clean" ] && [ $# -eq 1 ]; then rm -rf ./dist
elif [ "$1" = "package" ] && [ $# -eq 3 ]; then STAGE=$2
  REGION=$3
  'node_modules/.bin/sls' package -s $STAGE -p "./dist/${STAGE}" -r $REGION
elif [ "$1" = "deploy" ] && [ $# -eq 4 ]; then STAGE=$2
  REGION=$3
  ARTIFACT_FOLDER=$4
  echo "Deploying from ARTIFACT_FOLDER=${ARTIFACT_FOLDER}"
  assume_role
  # 'node_modules/.bin/sls' create_domain -s $STAGE -r $REGION
  echo "Deploying service to stage $STAGE..."
  'node_modules/.bin/sls' deploy --force -s $STAGE -r $REGION -p $ARTIFACT_FOLDER
  unassume_role
elif [ "$1" = "test-acceptance" ] && [ $# -eq 2 ]; then STAGE=$2
  echo "Running ACCEPTANCE tests for stage $STAGE..."
  STAGE=$STAGE npm run test-acceptance
  echo "ACCEPTANCE tests complete."
elif [ "$1" = "clearcreds" ] && [ $# -eq 1 ]; then unassume_role
  rm -f ./temp_creds.json
else instruction
  exit 1
fi 
```

Enter fullscreen mode Exit fullscreen mode

在上面的脚本中需要注意的主要事情是在 deploy 命令之前调用的`assume_role`函数。为了将 CodeBuild 部署到不同的 AWS 帐户，无服务器框架的`sls deploy`命令需要作为目标帐户中定义的角色运行。为此，Codebuild IAM 角色(在 DEV 帐户中运行)需要承担这个角色。我目前正在通过调用 AWS CLI 的`aws sts assume-role`命令来获取这个部署角色的临时凭证。然而，这似乎很麻烦，所以如果有人知道更干净的方法，我很想听听。

### 部署到暂存和生产

部署和运行测试的脚本类似于 dev 脚本，但是更简单:

```
# buildspec.staging.yml
version: 0.2

env:
  variables:
    TARGET_REGION: us-east-1
    SLS_DEBUG: '*'
    DEPLOYER_ROLE_ARN: 'arn:aws:iam::<staging_account_id>:role/ac-rest-api-staging-deployer-role'

# Deploy to STAGING stage and run acceptance tests.
phases:
  pre_build:
    commands:
      - chmod +x build.sh
      - ./build.sh install
  build:
    commands:
      - ./build.sh deploy staging $TARGET_REGION $CODEBUILD_SRC_DIR_stagingPackage
      - ./build.sh test-acceptance staging 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意的关键点是:

*   我使用`$CODEBUILD_SRC_DIR_stagingPackage`环境变量来访问最后一个管道步骤中名为`stagingPackage`的输出工件所在的目录。

`buildspec.prod.yml`文件与暂存文件非常相似，除了它引用了生产 IAM 角色和工件源目录。

## 调配权限和跨账户权限

这可能是整个过程中最困难的部分。正如我上面提到的，部署脚本假设一个预先存在的`deployer-role` IAM 角色，它存在于被部署到的阶段的 AWS 帐户中。
为了设置这些角色，我再次使用 CDK 来定义一个定制的构造`CrossAccountDeploymentRole`，如下所示:

```
export interface CrossAccountDeploymentRoleProps {
    serviceName: string;
    /** account ID where CodePipeline/CodeBuild is hosted */
    deployingAccountId: string;
    /** stage for which this role is being created */
    targetStageName: string;
    /** Permissions that deployer needs to assume to deploy stack */
    deployPermissions: PolicyStatement[];
}

/**
 * Creates an IAM role to allow for cross-account deployment of a service's resources.
 */
export class CrossAccountDeploymentRole extends Construct {
    public static getRoleNameForService(serviceName: string, stage: string): string {
        return `${serviceName}-${stage}-deployer-role`;
    }

    public static getRoleArnForService(serviceName: string, stage: string, accountId: string): string {
        return `arn:aws:iam::${accountId}:role/${CrossAccountDeploymentRole.getRoleNameForService(serviceName, stage)}`;
    }

    readonly deployerRole: Role;
    readonly deployerPolicy: Policy;
    readonly roleName: string;

    public constructor(parent: Construct, id: string, props: CrossAccountDeploymentRoleProps) {
        super(parent, id);
        this.roleName = CrossAccountDeploymentRole.getRoleNameForService(props.serviceName, props.targetStageName);
        // Cross-account assume role
        // https://awslabs.github.io/aws-cdk/refs/_aws-cdk_aws-iam.html#configuring-an-externalid
        this.deployerRole = new Role(this, 'deployerRole', {
            roleName: this.roleName,
            assumedBy: new AccountPrincipal(props.deployingAccountId),
        });
        const passrole = new PolicyStatement(PolicyStatementEffect.Allow)
            .addActions(
                'iam:PassRole',
            ).addAllResources();
        this.deployerPolicy = new Policy(this, 'deployerPolicy', {
            policyName: `${this.roleName}-policy`,
            statements: [passrole, ...props.deployPermissions],
        });
        this.deployerPolicy.attachToRole(this.deployerRole);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

传递给构造函数的`props`对象包含服务的特定需求。这里最主要的是`deployPermissions`，它是一组 IAM 策略语句，使用户能够运行无服务器框架的`sis deploy`命令来部署所有必要的资源(云构造栈、Lambda 函数等)。

需要注意的重要一点是，这组权限不同于您的无服务器服务需要执行的*运行时*权限。计算出一个服务需要什么样的*部署时间* IAM 权限是无服务器领域的一个众所周知的问题，我还没有找到一个好的解决方案。最初，我给了我的角色 admin 访问权限，直到我让端到端管道工作为止，然后向后工作，添加更细粒度的权限，但是这涉及到大量的试验和错误。

有关 IAM 如何与无服务器框架一起工作的更多信息，我推荐阅读[IAM 基础知识:使用无服务器管理权限](https://serverless.com/blog/abcs-of-iam-permissions/)。

## 构建和测试自己的管道的一些技巧

*   总是首先在本地运行 build.sh 文件，以确保它在您的计算机上工作
*   使用 CodePipeline 控制台中的“Release Change”按钮，以便启动新的管道执行，而无需向 Github 推送虚拟提交。

## 未来增强功能

展望未来，我想对我的 CICD 流程做一些补充:

*   在部署到产品之前，添加一个[手动批准步骤](https://docs.aws.amazon.com/codepipeline/latest/userguide/approvals.html)。
*   如果针对生产部署的验收测试失败，添加一个自动回滚操作。
*   拉请求的创建/更新应该触发较短的仅测试管道。这将有助于尽早发现集成问题。
*   构建通知的松散集成。
*   更新我的包步骤，以便它只构建一个将被部署到所有环境的不可变包。
*   获得额外的元，并有一个 CICD 代码的管道🤯。在部署新管道之前，将对我的管道的更改进行测试。

## 下一步

在我的迁移计划中，下一步是在 API 网关中内置一个新路由可以使用的授权机制。这将是我的新 CICD 管道测试的第一个真正的生产代码。

✉️ ***如果你喜欢这篇文章，并想了解更多关于无服务器的信息，你可能会喜欢[我关于在 AWS](https://winterwindsoftware.com/newsletter/) 中构建无服务器应用的每周简讯。***

* * *

您还可能会喜欢:

*   [担心无服务器带走](https://winterwindsoftware.com/concerns-that-serverless-takes-away/)
*   [“无服务器”的不同定义](https://winterwindsoftware.com/serverless-definitions/)
*   [无服务器词汇表](https://winterwindsoftware.com/serverless-glossary/)

*原载于 winterwindsoftware.com*。