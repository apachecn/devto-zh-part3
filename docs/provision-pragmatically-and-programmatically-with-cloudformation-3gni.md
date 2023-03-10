# 使用 CloudFormation 以实用和编程的方式进行调配，第 1 部分

> 原文：<https://dev.to/cmiles74/provision-pragmatically-and-programmatically-with-cloudformation-3gni>

[![CloudFormation Diagram](img/790f6cdb8e324bfcbf44e26ae7c70c1d.png "Diagram of our CloudFormation Tutorial Stack")](https://res.cloudinary.com/practicaldev/image/fetch/s--oZ6_eNe4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/cmiles74/cloudformation-tutorial/master/template-diagram.png)

有多少次你凝视着亚马逊通过其亚马逊网络服务(AWS)系列产品提供的服务清单，并感到焦虑和困惑的不愉快混合？如果你和我一样，每次你访问他们的网站，都会有*次。虽然我是我的软件开发人员朋友圈中第一批登上弹性计算云(EC2)列车的人之一，但我并不擅长跟上新的发展。我决定，今年将是我重回正轨、理清云虚拟化技术的字母汤的一年。*

在随意点击和阅读了大量乏味的介绍性页面后，我终于开始有了一个想法，我应该如何把所有这些东西放在一起。我发现的第一件事是:

> 通过点击一堆按钮来启动和运行所有的东西实在是太难了。

你的目标是什么？对我来说，就是能够为我的一个客户部署一个基于 web 的应用程序。作为一个推论，我希望能够为我的一个客户完全废弃一个基于 web 的应用程序(也许它已经被替换了，也许他们已经雇佣了另一个顾问)。实际上，这是两个目标。另外，我希望将我的所有客户端分开，我不希望防火墙规则的更改影响到我的所有客户端，而只是一个愿意承担风险的客户端。如果你想一想，将事情隔离开来也会更容易弄清楚月底我们可以对谁的哪些服务收费；我们还应该以一种有意义的方式标记这些资源。这是四个目标，这只是我们目前想到的。我们应该每天拍摄快照，而不是保留所有的快照。哦，备份数据库也很重要！

*   为项目提供资源
*   收回项目的资源
*   隔离项目的资源
*   按项目报告资源
*   按计划拍摄实例卷的快照并删除旧备份
*   按计划备份数据库，并按计划删除旧备份

看看这些目标，服务的数量，它们之间相互依赖的方式...答案显然是自动化。当然，亚马逊提供了一个自动化工具: [CloudFormation](https://aws.amazon.com/cloudformation/) 。

这是涵盖所有这些目标的系列文章的第一篇。我们将从一个空文件开始，然后逐步构建该文件，随着过程的进行，向该文件添加越来越多的资源。我计划总共写三篇文章，这一篇简要介绍工具，并为项目设置虚拟私有云，将它分成两个网络，并设置安全组。太刺激了！

我鼓励你跟随我们建立模板，但你可以随时在 GitHub 检查成品[。在苦读完本文之后，您可以继续阅读本系列的第二和第三部分。在这个系列的最后，我们会有一个*字面上的*做所有事情的模板。](https://github.com/cmiles74/cloudformation-tutorial)

*   [第 2 部分:我们设置一些桶、组和特权，以及我们的实例](https://dev.to/cmiles74/provision-programmatically-with-cloudformation-finally-some-instances-2dha)
*   [第 3 部分:我们将备份和保留作业放在一起](https://dev.to/cmiles74/provision-pragmatically-and-programmatically-with-cloudformation-backup-jobs-and-wrap-up-4plp)

## 安装 AWS 命令行工具

Amazon 为 CloudFormation 提供了一个笨拙的拖放界面，但这与本文的主旨背道而驰。你也可以编写你的云形成模板，然后通过他们的网络界面上传，我认为这也有点笨重。我建议您安装 [AWS 命令行工具](https://aws.amazon.com/cli/)，并使用这些工具进行部署。亚马逊也为 Windows 提供软件包，但如果你在 Windows 环境下工作，你可能会对 PowerShell 的 [AWS 工具](https://aws.amazon.com/powershell/)更感兴趣，它们做的事情差不多，但你知道，与 [PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview?view=powershell-6) 做的事情差不多。

这是一个强大的提示，但是如果你管理不止一个亚马逊账户或者处理不止一个地区的事务，你可以[配置个人资料](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html)来简化这个过程。

安装并正确配置其中一个工具后，您可以直接从控制台窗口调用 CloudFormation！`:-)`

## 开始我们新的云形成模板

在 CloudFormation 的世界中，您正在编写的文档被称为“模板”。我相信它被称为模板，因为一个模板可以用来创建许多不同的资源集。例如，在本教程中，我们将编写一个提供 web 服务器和数据库服务器的模板；我们可以使用该模板来提供尽可能多的 web-and-database-server 对，它们都是相互独立的。

你可以在 [YAML](https://yaml.org/) 或者 [JSON](https://www.json.org/) 中写你的模板，如果你是手写的(就像我们现在这样)，那么我*强烈建议你在 YAML 写。我知道，我知道，YAML 并不是每个人都喜欢的，但是在一个 JSON 文档中把所有的大括号和方括号排好，这本身就是一种精神折磨。你值得更好的，相信我:不要这样对自己。*

无论如何，我们将从一个描述和一个参数开始。正如您可能已经怀疑的那样，参数是一个值，当我们实际使用模板来提供某些东西时，我们可以提供这个值。在你最喜欢的编辑器中打开一个新文件，输入下面的文本，然后另存为`template.yaml`。

```
Description: Provisions a simple cluster with a web and a database server

Parameters:
  KeyPairName:
    Type: String
    Description: Name of keypair used when provisioning instances 
```

Enter fullscreen mode Exit fullscreen mode

在整篇文章中，我们将继续构建这个模板，但是如果您需要向前跳转或者需要参考整个内容，您可以在 GitHub 项目中浏览它。

有些决定最好推迟到绝对的最后一分钟，当你有决定要推迟时，参数就在这里。当我们决定提供我们的资源时，我们可以提供 EC2 密钥对的名称以便在那时使用。

我不打算深入讨论如何管理 EC2 密钥对。也许每个有权访问供应实例的人都有一个。也许每个客户都有一个。无论您如何管理它，您都可以在配置资源时传递它。

接下来，我们将添加更多的参数，但我们将为所有参数提供默认值。这里的想法有两个方面:首先，我们希望能够灵活地在供应时更改一些特性。第二，我们可以在整个模板中引用这些参数，这样可以避免在整个模板中散布这些相同的值。

```
 InstanceType:
    Type: String
    Description: Instance type to provision
    Default: t2.micro
  ImageId:
    Type: String
    Description: Image to use when provisioning instances
    Default: ami-035be7bafff33b6b6  # Amazon Linux 2
  RootDevice:
    Type: String
    Description: Name of the root device on provisioned instances
    Default: xvda  # varies based on Linux type (Ubunut /dev/sda1)
  RootFsType:
    Type: String
    Description: File system type for root device
    Default: xfs 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到，这些都与我们将要调配的实例相关。我们调用实例类型、我们将用来创建实例的映像、根卷的名称及其文件类型。注意，根卷和文件类型不是我们真正可以选择的，它们是由映像决定的(在本例中，是 Amazon Linux 2)。

## 调配新的虚拟私有云

为了提供模板，我们至少需要一个资源。对于这个项目，我们将创建自己的[虚拟私有云](https://aws.amazon.com/vpc/faqs/) (VPC)来保存我们的资源。这将使事情与您可能已经部署到 AWS 的其他事情隔离开来。

```
Resources:

  TutorialVPC:                   # name of the resource
    Type: AWS::EC2::VPC          # type of resource
    Properties:                  # properties of the resource
      CidrBlock: "10.6.0.0/24"
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: "Name"
          Value: "Tutorial  VPC" 
```

Enter fullscreen mode Exit fullscreen mode

模板的`Resources`部分将包含我们提供的每个资源的一节，这将是模板的主体。本节中的每个小节将以资源的名称(`TutorialVPC`)开始，当我们需要在创建其他资源时引用该资源时，可以使用这个名称(例如，当我们向这个 VPC 添加子网时)。在名称之后我们需要声明资源的`Type`:每一个 CloudFormation 资源都有一个类型，它们都清楚地记录在 [CloudFormation API 文档](https://docs.aws.amazon.com/AWSCloudFormation/latest/APIReference/Welcome.html)中。在上面的例子中，我们正在创建一个新的 [VPC](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpc.html) 实例。接下来，我们为我们的资源设置`Properties`，这里提供的值将特定于每个资源。大多数资源接受`Tags`属性，但不是全部，如果在不支持该属性的资源上设置`Tags`，CloudFormation 将抛出一个错误。

每个 VPC 都需要一个 IP 地址块，我们用`CidrBlock`属性设置它，并为[提供一个有 254 个可用地址的不可路由网络](http://jodies.de/ipcalc?host=10.6.0.0&mask1=24&mask2=)。我选择了一个不会与我在家或在工作中使用的地址范围冲突的网络，这很重要，如果你想使用虚拟专用网络(VPN)将你的亚马逊 VPC 连接到你自己的网络(T4)，也许是为了更容易地访问这些资源。

我们希望 Amazon 继续为我们的实例分配名称，所以我们将`EnableDnsSupport`和`EnableDnsHostnames`属性设置为 true。最后，我们在资源上设置了一些标签，以提醒我们自己当初为什么要提供它们。

## 使用我们的模板调配资源

有了我们的模板集，我们就可以使用它来提供一些资源了！当我们向 CloudFormation 提供模板时，我们还必须提供我们的参数值，并且我们有机会标记所提供资源的所有(可能按客户或项目或两者)。

继续运行下面的命令开始配置。当 CloudFormation 建立所有的资源时，它将它们链接成一个“堆栈”。完成后，您可以将堆栈作为一个单元来处理。堆栈名称为所有相关资源提供了一个友好的句柄。

```
aws cloudformation create-stack \
  --stack-name tutorial \
  --template-body file://template.yaml \
  --parameters ParameterKey=KeyPairName,ParameterValue=cloudfront-tutorial \
  --tags Key=Project,Value=cf-tutorial 
```

Enter fullscreen mode Exit fullscreen mode

记得在我们使用`cloudfront-tutorial`的地方提供您自己的密钥对名称。

一旦 CloudFormation 收到模板，它将为堆栈返回一个新的`StackId`，带有 [Amazon 资源名称](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html) (ARN)，您将不得不等待堆栈完成供应。你可以通过 [CloudFormation web 控制台](https://console.aws.amazon.com/cloudformation)查看你的堆栈，看看事情进展如何。有些资源比其他资源花费的时间长，CloudFormation 将确保它们以正确的顺序创建，并计算出依赖关系。这个模板应该会很快运行，因为我们没有提供太多。

我把这个命令分成几行，试图让它更容易理解。在上面的`create-stack`命令中我们...

*   让 AWS 工具使用“create-stack”命令调用 CloudFormation
*   将我们正在创建的堆栈的名称设置为“tutorial”
*   使用`file://` URL 向 CloudFormation 提供我们的模板
*   我们设置了一个模板参数`KeyPairName`(注意我们设置参数的奇怪方式)
*   我们提供了一个标签键和值，CloudFormation 将使用它来标记所有供应的资源

您可以用空格分隔多个参数，用逗号分隔多个标记。很奇怪，我不知道他们为什么不统一。

当供应完成时，您可以前往 [VPC web 控制台](https://console.aws.amazon.com/vpc)检查您的新虚拟云。没什么可看的，但是如果你从左边的导航栏中选择“Yourq VPCs ”,然后从列表中选择“VPC 教程”,你可以点击“标签”标签，查看与资源相关的标签。您将看到我们在模板中指定的标签(`Name`)和我们传递到`create-stack`命令中的标签(`Project`)。CloudFormation 还设置了自己的几个标签，将资源链接到堆栈。

当我们处理模板时，我们可能会更新堆栈或完全删除它。更新堆栈的命令几乎完全相同。

```
aws cloudformation update-stack \
  --stack-name tutorial \
  --template-body file://template.yaml \
  --parameters ParameterKey=KeyPairName,ParameterValue=cloudfront-tutorial \
  --tags Key=Project,Value=cf-tutorial 
```

Enter fullscreen mode Exit fullscreen mode

如果 CloudFormation 在创建或更新堆栈时遇到任何问题，它将回滚到堆栈的上一个有效状态。如果这发生在栈的创建过程中，你最终将没有任何资源，如果这发生在更新过程中，那么就好像你的更新尝试从未发生过。

删除堆栈要短得多。

```
aws cloudformation delete-stack --stack-name tutorial 
```

Enter fullscreen mode Exit fullscreen mode

继续运行该命令来删除堆栈。AWS 工具命令不会返回任何结果，但是如果您切换到 CloudFormation 控制台，您可能会在堆栈被完全删除之前瞥见它。如果你切换到 VPC 控制台，你会看到新的 VPC 不见了，它和堆栈一起被删除了。

在我看来，这是一种非常好的工作方式。您可以编辑您的模板，并以有意义的方式创建和链接堆栈的所有部分(在 VPC、正确的子网中使用正确的安全组和策略等进行配置)。)当你对自己的工作感到非常满意的时候，你就可以着手准备了。如果有问题或者事情没有按照预期的方式排列，你可以简单地删除堆栈并继续使用你的模板。对我来说，这比点击各种 web 控制台，并通过填写字段或对所有不同的部分应用操作来试图将一切联系起来要方便得多。

### 通过栈更新甚至删除来保留资源

CloudFormation 会尽最大努力在两次更新之间保留资源。另一方面，有些更改在供应后无法进行:在这些情况下，CloudFormation 将删除资源并创建一个替代资源。对于许多事情来说，这并不重要，但是对于像实例或弹性 IP 地址这样的事情，你可能真的*需要*来保留那些资源。 [CloudFormation 文档](https://docs.aws.amazon.com/AWSCloudFormation/latest/APIReference/Welcome.html)非常清楚哪些资源更新可以在不“服务中断”的情况下执行，请留意那些警告。

您还可以为模板中的资源设置一个`DeletionPolicy`。这使您可以向云形成发出信号，表明对于某些资源，您希望保留该资源(即使需要进行替换以进行配置)，或者(如果该资源支持)在删除该资源(即 EC2 卷或 RDS 实例)之前执行“快照”备份。也就是说，即使为您的关键资源设置了删除策略，您也应该认真考虑在配置堆栈后更新它们。

我建议您在处理模板时，不要使用删除策略。等到您对模板感觉非常好的时候，也许等到您准备好部署您的应用程序或项目的时候。当您到达这一点时，返回并将那些真正重要的东西(包含数据的卷、您大量定制的实例等)的删除策略设置为`Retain`。)然后更新你的栈。

现在我们已经有了基本的想法和机制，我们可以更快地前进了。

## 设置互联网网关

对于这个项目，我们将把我们的虚拟私有云划分为两个子网:一个子网将能够与公共互联网通信，并将托管我们的 web 服务器。另一个子网将能够与第一个子网通信，但不会面向互联网，它将容纳我们的数据库服务器。这样，我们可以确保公共互联网上的机器不能直接与我们的数据库服务器通信。

为了让我们的外部子网面向互联网，我们需要提供一个[互联网网关](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html)，然后设置路由，以便我们的虚拟云中的实例可以通过公共互联网发送和接收流量。

```
 TutorialInternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
        - Key: "Name"
          Value: "Internet  Gateway" 
```

Enter fullscreen mode Exit fullscreen mode

对于[互联网网关资源](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-internetgateway.html)，没有太多需要配置的。我们给它一个名字并添加一个标签。接下来，我们需要将它连接到我们的虚拟云。

```
 TutorialVPCGatewayAttachment:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref TutorialVPC
      InternetGatewayId: !Ref TutorialInternetGateway 
```

Enter fullscreen mode Exit fullscreen mode

配置 [VPCGatewayAttachment](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpc-gateway-attachment.html) 也没什么。有趣的是，我们告诉它我们希望它连接到哪个 VPC。我们使用 [`!Ref`函数](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-ref.html)来表示我们正在向模板中的另一个资源提供*引用*，然后我们提供该资源的名称。

## 设置子网

有了互联网，我们就可以创建两个子网了。首先，我们的公共子网。

```
 TutorialPublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref TutorialVPC
      CidrBlock: "10.6.0.0/25"
      MapPublicIpOnLaunch: true
      Tags:
        - Key: "Name"
          Value: "Public  Subnet" 
```

Enter fullscreen mode Exit fullscreen mode

我们使用[子网资源](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-subnet.html)来分割[我们的 VPC](http://jodies.de/ipcalc?host=10.6.0.0&mask1=25&mask2=) 的一大块，其中一半是我们的地址空间，我们使用`VpcId`属性将这个子网声明链接到我们的 VPC。我们将`MapPublicIpOnLaunch`属性设置为`true`，以便每个实例在启动时获得一个公共 IP。

```
 TutorialPrivateSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref TutorialVPC
      CidrBlock: "10.6.0.128/25"
      MapPublicIpOnLaunch: false
      Tags:
        - Key: "Name"
          Value: "Private  Subnet" 
```

Enter fullscreen mode Exit fullscreen mode

在上面的小节中，我们[将剩余的地址空间](http://jodies.de/ipcalc?host=10.6.0.128&mask1=25&mask2=)分配给我们的私有子网，我们再次使用`VpcId`属性将这个子网声明链接到我们的 VPC。我们不希望该网络中的实例拥有公共 IP 地址，我们通过将`MapPublicIpOnLaunch`属性设置为`false`来表明这一点。

## 设置我们的路由

我们总是有一个默认路由，让我们的 VPC 中的地址相互通信，但我们没有开始任何路由到我们的互联网网关和公共互联网。为了设置路由，我们首先需要设置路由表。

```
 TutorialPublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref TutorialVPC
      Tags:
        - Key: "Name"
          Value: "Public  Internet  Route  Table"

  TutorialPrivateRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref TutorialVPC
      Tags:
        - Key: "Name"
          Value: "Private  Route  Table" 
```

Enter fullscreen mode Exit fullscreen mode

[RouteTable resource](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-route-table.html) 没有太多内容，我们表示将使用`VpcId`属性管理我们的 VPC 的路线，并设置一个标签。

接下来，我们将设置实际路线。

```
 TutorialInternetRoute:
    Type: AWS::EC2::Route
    DependsOn: VPCGatewayAttachment
    Properties:
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref TutorialInternetGateway
      RouteTableId: !Ref TutorialPublicRouteTable 
```

Enter fullscreen mode Exit fullscreen mode

[路由资源](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-route.html)向路由表添加新路由。在这里，我们向我们的公共表添加了一个路由，以便通过我们的 internet 网关为公共 internet 上的地址路由流量。我们让 CloudFormation 知道，除非已经用 [`DependsOn`属性](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-attribute-dependson.html)设置了`VPCGatewayAttachment`，否则不应该创建这个资源。像`Retain`属性一样，你可以把它放在任何资源上。

```
 TutorialPublicSubnetPublicRoute:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref TutorialPublicRouteTable
      SubnetId: !Ref TutorialPublicSubnet 
```

Enter fullscreen mode Exit fullscreen mode

有了到公共互联网的路由设置，我们可以使用 [SubnetRouteTableAssociation 资源](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-subnet-route-table-assoc.html)将我们的路由表链接到我们的公共子网。

接下来，我们需要为我们的私有子网做一些类似的事情。这里最大的不同是，我们的私有子网不直接连接到互联网。相反，我们将使用一个[网络地址转换(NAT)网关](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)让我们私有子网中的实例连接到互联网，而不允许公共互联网上的机器启动与我们私有子网中的实例的连接。

```
 TutorialNatGatewaySubnetRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref TutorialVPC
      Tags:
        - Key: "Name"
          Value: "NAT  Gateway  Subnet  Route  Table" 
```

Enter fullscreen mode Exit fullscreen mode

我们创建一个新的路由表，并将其与我们的 VPC 相关联。

```
 TutorialNatGatewayInternetRoute:
    Type: AWS::EC2::Route
    DependsOn: TutorialVPCGatewayAttachment
    Properties:
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref TutorialInternetGateway
      RouteTableId: !Ref TutorialNatGatewaySubnetRouteTable 
```

Enter fullscreen mode Exit fullscreen mode

然后我们通过我们的互联网网关创建一个到公共互联网的路由。

下一步是创建我们的 NAT 网关，但首先我们需要获得一个公共可访问的 IP 地址，它将被分配给我们的 NAT 网关。我们可以使用 [EIP(弹性 IP)资源](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-eip.html)来处理一个新的弹性 IP 地址。

```
 TutorialNatGatewayElasticIP:
    Type: AWS::EC2::EIP
    Properties:
      Domain: vpc 
```

Enter fullscreen mode Exit fullscreen mode

我们将`domain`的值设置为`vpc`，表示我们希望从我们的 VPC 使用这个地址。有意思的是，这是该属性的唯一有效值。

现在我们可以设置 NAT 网关了...

```
 TutorialNatGateway:
    Type: AWS::EC2::NatGateway
    DependsOn: VPCGatewayAttachment
    Properties:
      AllocationId: !Sub ${TutorialNatGatewayElasticIP.AllocationId}
      SubnetId: !Ref TutorialPublicSubnet
      Tags:
        - Key: "Name"
          Value: "NAT  Gateway" 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 [NatGateway 资源](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-natgateway.html)来创建我们的新 NAT 网关，并指出我们需要在创建之前提供我们的 VPCGatewayAttachment 资源。我们设置了`SubnetId`属性，将它放在我们的公共子网中，以便它可以与互联网通信。

虽然将我们提供的弹性 IP 直接提供给网关很有意义，但是我们需要提供与该弹性 IP 相关联的`AllocationId`。这里我们使用 [`!Sub`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-sub.html) 函数来获取`NatGatewayElasticIP`的`AllocationId`，并将该值提供给`AllocationId`属性。

现在，我们可以为私有子网设置路由，以便通过 NAT 网关访问互联网。

```
 TutorialPrivateSubnetNatRoute:
    Type: AWS::EC2::Route
    DependsOn: TutorialNatGateway
    Properties:
      DestinationCidrBlock: 0.0.0.0/0
      NatGatewayId: !Ref TutorialNatGateway
      RouteTableId: !Ref TutorialPrivateRouteTable 
```

Enter fullscreen mode Exit fullscreen mode

我们创建我们的路由，使用`DependsOn`属性来表示我们需要在创建这个路由之前提供`NatGateway`。然后，我们使用`NatGateway`属性链接我们的网关，并使用`RouteTableId`将路由绑定到我们的路由表。

```
 TutorialPublicSubnetRouteAssoc:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref TutorialPublicRouteTable
      SubnetId: !Ref TutorialPublicSubnet

  TutorialPrivateSubnetRouteAssoc:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref TutorialPrivateRouteTable
      SubnetId: !Ref TutorialPrivateSubnet 
```

Enter fullscreen mode Exit fullscreen mode

最后一步是将我们的路由表与我们的子网相关联！我们使用[SubnetRouteTableAssociation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-subnet-route-table-assoc.html)将我们的路由表附加到匹配的子网。

## 设置我们的安全组

在供应实例之前，我们需要设置我们的[安全组](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html)。每个子网都有一个，这些组将指定我们允许哪些流量进出我们的每个子网。我们将允许 web 流量进出我们的公共子网，在我们的私有子网中，我们只允许 web 流量进出，不允许任何流量入站。

```
 TutorialPublicSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Description: "Web  Server  Security  Group"
    Properties:
      VpcId: !Ref TutorialVPC
      GroupDescription: Web Server Security Group
      SecurityGroupIngress:
        - CidrIp: 0.0.0.0/0         # Public Internet
          IpProtocol: tcp
          FromPort: 80              # HTTP 
          ToPort: 80
        - CidrIp: 0.0.0.0/0         # Public Internet
          IpProtocol: tcp
          FromPort: 443             # HTTPS
          ToPort: 443
        - CidrIp: 0.0.0.0/0         # Replace with your IP Range
          IpProtocol: tcp
          FromPort: 22              # SSH
          ToPort: 22
        - CidrIp: 10.6.0.0/24       # Inside the VPC
          IpProtocol: -1            # All traffic 
```

Enter fullscreen mode Exit fullscreen mode

我们使用[安全组资源](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-security-group.html)为公共子网创建新组。我们提供的`Description`在 web 控制台中是可见的，然后我们转到属性。我们使用`VpcId`属性来表示这个组属于我们的 VPC，然后为`GroupDescription`提供另一个非常相似的描述(这个属性是必需的)。

解决了这个问题后，我们继续讨论入站(入口)规则。每个规则由一个[安全组规则属性](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-security-group-rule.html)提供，该属性由一个 IP 地址范围、协议和一个端口范围组成。我们已经为 VPC 内部的 HTTP、HTTPS、SSH 和流量创建了规则。如果您正在配置 Windows 实例，不要忘记添加端口 3389 用于远程桌面访问。

在上面的例子中，我们已经向整个互联网开放了 SSH 端口:这不是一个好主意。因为这些端口几乎专门用于实例管理，所以更好的选择是将这些端口开放到您办公室使用的地址范围。如果你没有一个固定的地址范围可以使用，那么我鼓励你寻找其他的解决方案，也许使用 VPN。

```
 SecurityGroupEgress:
        - CidrIp: 0.0.0.0/0         # Public Internet
          IpProtocol: tcp
          FromPort: 80              # HTTP
          ToPort: 80
        - CidrIp: 0.0.0.0/0         # Public Internet
          IpProtocol: tcp
          FromPort: 443             # HTTPS
          ToPort: 443
        - CidrIp: 0.0.0.0/0         # Public Internet
          IpProtocol: tcp
          FromPort: 587             # SSL SMTP
          ToPort: 587
        - CidrIp: 10.6.0.0/24       # Inside the VPC
          IpProtocol: -1            # All traffic
      Tags:
        - Key: "Name"
          Value: "Public  Subnet" 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们为出站流量设置规则:HTTP、HTTPS 和 SSL 上的 SMTP。我们也有一个规则，允许所有的流量在我们的 VPC。如果您有更强的安全意识，您可以确定 VPC 内部的特定流量类型(例如，只有 web、SSH 和数据库流量)。

我们专用子网的安全组要简单得多。

```
 TutorialPrivateSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Description: "Private  Security  Group"
    Properties:
      VpcId: !Ref TutorialVPC
      GroupDescription: Private Subnet Security Group
      SecurityGroupIngress:
        - CidrIp: 10.6.0.0/24       # Inside the VPC
          IpProtocol: -1            # All traffic
      SecurityGroupEgress:
        - CidrIp: 10.6.0.0/24       # Inside the VPC
          IpProtocol: -1            # All traffic
        - CidrIp: 0.0.0.0/0         # Public Internet
          IpProtocol: tcp
          FromPort: '80'            # HTTP
          ToPort: '80'
        - CidrIp: 0.0.0.0/0         # Public Internet
          IpProtocol: tcp
          FromPort: '443'           # HTTPS
          ToPort: '443'
      Tags:
        - Key: "Name"
          Value: "Private  Subnet" 
```

Enter fullscreen mode Exit fullscreen mode

我们只允许来自 VPC 内部的传入流量进入专用子网。出站时，我们允许所有发往 VPC 其他地址的流量，并且只允许 HTTP 和 HTTPS 访问公共互联网。来自我们私有子网地址的流量已经通过我们的 NAT 网关，我们可能只会使用互联网下载软件更新。

## 调配其他资源

除了实例之外，我们还可以提供许多其他资源。您可能需要队列、通知主题等。对于这个项目，我们将设置一个 S3 存储桶来保存来自数据库服务器的转储文件。

在实例之前设置这些的好处是，我们可以告诉实例这些资源并设置策略，这样我们的实例就可以访问这些资源，而无需向它们提供一组特定的凭证。

```
 TutorialBackupS3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      AccessControl: Private
      Tags:
        - Key: "Name"
          Value: "Backup  Bucket" 
```

Enter fullscreen mode Exit fullscreen mode

够简单！我们使用[存储桶资源](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket.html)来创建我们的存储桶，并使用`AccessControl`属性将该存储桶设置为私有。

这是一段很长的路，但我们已经完成了很多。继续提供您的模板，并等待 CloudFormation 完成所有设置。从 CloudFormation web 控制台中，选择您的堆栈并单击“Resources”选项卡，以查看您已配置的所有项目。这样的进步！

## 直到下一次

这就是我们在这篇文章中要讲的全部内容，我们复习了很多材料。在本系列的下一篇文章中，我们将设置一些组和角色，并实际提供一些实例。一旦您从所有这些 YAML 中恢复过来，请查看第 2 部分！

*   [第 2 部分:我们设置一些桶、组和特权，以及我们的实例](https://dev.to/cmiles74/provision-programmatically-with-cloudformation-finally-some-instances-2dha)

感谢您的阅读！

* * *