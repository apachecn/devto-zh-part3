# 服务结构上的 Azure 功能

> 原文：<https://dev.to/kaiwalter/azure-functions-on-service-fabric-40n8>

这篇文章描述了在虚拟网络的服务结构中运行功能的步骤和知识，该虚拟网络通过 ExpressRoute 连接到我在[构建关键任务中提到的企业后端系统。网微服务- BRK3047(视频)](https://youtu.be/sU52ULgX7YU?t=2058) / [(会话描述)](https://mybuild.techcommunity.microsoft.com/sessions/77069)。

# 第 1 章- Azure Functions / WebHost v1

## 为什么？

我正在做一个项目，这个项目是一个将我们公司网络之外的应用程序集成到我们公司后端系统的平台。这个[集成平台](https://www.youtube.com/watch?v=BoZimCedfq8&feature=youtu.be&t=1328)的很大一部分是在微软 Azure 云环境中构建的。

尽管它“仅仅”是作为数字世界和传统世界之间的一个集成层，但它仍然保留了一些值得保护的业务数据和逻辑，而不是公开的。

我们在这个场景中使用的大多数服务- *API 管理、Cosmos DB、服务总线、服务结构、存储* -都可以通过虚拟网络端点获得，因此对这些服务的访问可以减少到虚拟网络的边界-这限制了整个环境的攻击面。

集成故事的一个主要元素是处理逻辑。这主要是通过使用一个简单的无状态输入-处理-输出概念来实现的，这个概念是由 Azure Functions(以及其他功能&无服务器平台)提供的。事实上，与 API 管理一起，这些功能是系统的 DNA，而其他服务“仅仅”满足它们单一的预期目的(存储，排队，...).

## 挑战

现在。如何在虚拟网络的边界内获得这些功能...

*   无缝访问连接的后端资源
*   没有任何公开曝光

那时， **VNET** 对等的应用服务能力不足以满足我的这些需求。

应用服务环境(又名 **ASE** ):早在 2017 年春夏，西欧的实例部署时间在 4 到 6 小时之间&每月的成本超出了合理的范围——[我的方法在这里描述](https://github.com/KaiWalter/ARM-FunctionApp-in-ILBASE-SelfSignedCert)。此外，ASE 增加了一些虚拟机/资源，这些虚拟机/资源在只有功能的情况下不会得到适当利用。也许与此同时，v2 也有所改进——我没有检查。

但是我们已经有**服务结构**在虚拟网络中运行——托管有状态服务和无状态服务，我们无法通过 API 管理轻松地将其连接到后端资源。那么为什么不把 Azure 的功能挤进服务结构中呢？

## 动机

一路上我发现，我已经在 Stack Overflow 上发表了几篇问答风格的文章。然而，我想把它拼凑成一个更全面的故事，给那里的人们一个跟随的机会，并可能为自己改编一些东西。

## WebHost v1 作为服务架构应用

在容器在服务结构上可用之前，我在这方面投入了一些时间。派生 WebJobs 脚本 SDK /函数 v1 我尝试修改代码，以便它可以作为本机服务结构应用程序运行。我放弃了这种方法:我有太多的工作要做，又缺乏成功的知识。

在路上，我设法获得了一个[功能控制台主机，作为托管在服务结构](https://github.com/KaiWalter/azure-webjobs-sdk-script-console-host-in-servicefabric)中的客户可执行文件，但这没有帮助——我真的需要 WebHost。

## WebHost v1 作为服务结构中的容器

幸运的是，大约在 2017 年秋天，容器在 SF 中得到支持，我能够将 v1 WebHost 烘焙到 Windows 容器中。

最后，通过这种方法，以这种模式托管的功能可以作为一等公民存在于虚拟网络中，并且我们能够将上述无状态服务结构应用程序迁移到功能中，以实现一个公共编程模型。此外，我们在 API 管理中托管的许多“跳转”API(从公共消费或应用服务计划的功能到私有虚拟网络的桥接)也可以删除。

* * *

### 可扩展性

在讨论该方法的单个元素之前，先说一下可伸缩性——这似乎是一个显而易见的问题；感谢 [Paco](https://twitter.com/pacodelacruz) 指出。

当我们开始开发我们的平台时，我们认为它需要非常具有可扩展性。每秒钟有成千上万的信息从四面八方涌来，需要以同样的速度立即传递。结果是:不太好。一些生产者可能会生成大量的消息(例如，业务系统中的大量数据变更),但是大多数消费者——包括我们的数据库 Cosmos DB——需要一种平衡的方式来传递这些消息。因此，这个平台更像是一个三明治——允许从生产者那里快速卸载，并向消费者应用节流转发。

基于我们最初的假设，我们开始用 API 管理来处理卸载点，API 管理将请求传递给消费计划中的 Azure 函数。这种设置很好地满足了可伸缩性需求——增加传入流量会使功能扩大，减少流量又会缩小。我们没有考虑到——当时也不知道——函数消费计划沙盒环境的局限性。HTTP 触发的功能拾取传入的流量，并将其分发到几个目标消息队列、数据库和/或目标消费者 HTTP 端点。高消息负载加上过多的处理或转发步骤导致了大量端口耗尽的情况。为了避免这种情况，我们决定让 API 管理承担初始负载，将消息元数据直接放入服务总线队列，将消息有效负载放入 Blob 存储。信件功能以受控速度处理该消息流量。

因此，不再需要消费计划和功能应用实例的灵活扩展。今天，我们根据我们在某些服务总线队列中的积压，对容器进行半自动扩展。

* * *

### 关键要素和陷阱

#### 构建功能主机

当我开始的时候，可以用`Dockerfile` :
直接下载预建的函数主机

```
...
ADD https://github.com/Azure/azure-functions-host/releases/download/1.0.11559/Functions.Private.1.0.11559.zip C:\\WebHost.zip

RUN Expand-Archive C:\WebHost.zip ; Remove-Item WebHost.zip`
... 
```

Enter fullscreen mode Exit fullscreen mode

在某个时候，团队停止提供这些预录制版本。这需要将我们的 CI/CD 流程调整为功能主机基础映像进入[下载源代码](https://gist.github.com/KaiWalter/e69cfd1d19f56b107acae102484e77d1)，例如在 Azure DevOps(又名 VSTS)中构建它以加载到基础映像:

```
...
ADD Functions.Private.zip C:\\WebHost.zip

RUN Expand-Archive C:\WebHost.zip ; Remove-Item WebHost.zip
... 
```

Enter fullscreen mode Exit fullscreen mode

#### 管理主密钥/秘密

为了控制函数主机在启动时使用的主密钥——而不是生成随机密钥——我们准备了自己的`host_secrets.json`文件

```
{
   "masterKey": {
   "name": "master",
   "value": "asGmO6TCW/t42krL9CljNod3uG9aji4mJsQ7==",
   "encrypted": false
},
"functionKeys": [
      {
         "name": "default",
         "value": "asGmO6TCW/t42krL9CljNod3uG9aji4mJsQ7==",
         "encrypted": false
      }
   ]
} 
```

Enter fullscreen mode Exit fullscreen mode

然后将该文件放入功能主机的指定机密文件夹(`Dockerfile` ):

```
...
ADD host_secrets.json C:\\WebHost\\SiteExtensions\\Functions\\App_Data\\Secrets\\host.json
... 
```

Enter fullscreen mode Exit fullscreen mode

#### 自动启动网站

`Dockerfile`包含了这个配置，使默认网站自动启动并指向函数 WebHost。

```
...  RUN  Import-Module  WebAdministration;  \  Set-ItemProperty  'IIS:\Sites\Default Web Site\'  -name  physicalPath  -value  'C:\WebHost\SiteExtensions\Functions';  \  Set-ItemProperty  'IIS:\Sites\Default Web Site\'  -name  serverAutoStart  -value  'true';  \  Set-ItemProperty  'IIS:\AppPools\DefaultAppPool\'  -name  autoStart  -value  'true';  ... 
```

Enter fullscreen mode Exit fullscreen mode

#### 永远开启/保持开启

我还用后台服务总线队列处理测试了这个设置。尽管我为 Web 站点设置了 autostart 属性，但后台处理只在 WebHost 由 HTTP 触发器启动时才开始。因此，我至少有一个 HTTP 触发的函数(在下面的示例`GetServiceInfo`中),我在服务结构负载平衡器的 HTTP health probe 中查询它。这使得网络主机保持运行，进行后台处理。

从服务光纤臂模板:

```
...
        "loadBalancingRules": [
          {
            "name": "Service28000LBRule",
            "properties": {
              "backendAddressPool": {
                "id": "[variables('lbPoolID0')]"
              },
              "backendPort": 28000,
              "enableFloatingIP": false,
              "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
              },
              "frontendPort": 28000,
              "idleTimeoutInMinutes": 5,
              "probe": {
                "id": "[concat(variables('lbID0'),'/probes/Service28000Probe')]"
              },
              "protocol": "Tcp"
            }
          },
...
        "probes": [{
...
          {
            "name": "Service28000Probe",
            "properties": {
              "protocol": "Http",
              "port": 28000,
              "requestPath": "/api/GetServiceInfo",
              "intervalInSeconds": 60,
              "numberOfProbes": 2
            }
          },
... 
```

Enter fullscreen mode Exit fullscreen mode

#### 加载自己的证书集

`Dockerfile`可用于将证书加载到容器中，供功能 App 使用:

```
...  ADD  Certificates\\mycompany.org-cert1.cer  C:\\certs\\mycompany.org-cert1.cer  ADD  Certificates\\mycompany.org-cert2.cer  C:\\certs\\mycompany.org-cert2.cer  RUN  Set-Location  -Path  cert:\LocalMachine\Root;\  Import-Certificate  -Filepath  "C:\\certs\\mycompany.org-cert1.cer";\  Import-Certificate  -Filepath  "C:\\certs\\mycompany.org-cert2.cer";\  Get-ChildItem;  ... 
```

Enter fullscreen mode Exit fullscreen mode

#### 扩展启动

为了给应用程序容器的启动添加更多的处理(我们稍后会用到),从`microsoft/aspnet:4.7.x`映像
传递过来的`ENTRYPOINT`

```
...
ENTRYPOINT ["C:\\ServiceMonitor.exe", "w3svc"] 
```

Enter fullscreen mode Exit fullscreen mode

可以替换为备选输入脚本

```
...
    Set-ItemProperty 'IIS:\AppPools\DefaultAppPool\' -name autoStart -value 'true';

EXPOSE 80

ENTRYPOINT ["powershell.exe","C:\\entry.PS1"] 
```

Enter fullscreen mode Exit fullscreen mode

哪个在容器开始时执行步骤:

```
...
# this is where the magic happens
...
C:\ServiceMonitor.exe w3svc 
```

Enter fullscreen mode Exit fullscreen mode

#### 包装完毕

这是基础图像`Dockerfile`的样子

```
FROM microsoft/aspnet:4.7.1
SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

ADD Functions.Private.zip C:\\WebHost.zip

RUN Expand-Archive C:\WebHost.zip ; Remove-Item WebHost.zip

ADD host_secrets.json C:\\WebHost\\SiteExtensions\\Functions\\App_Data\\Secrets\\host.json

ADD entry.PS1 C:\\entry.PS1

ADD Certificates\\mycompany.org-cert1.cer C:\\certs\\mycompany.org-cert1.cer
ADD Certificates\\mycompany.org-cert2.cer C:\\certs\\mycompany.org-cert2.cer

RUN Set-Location -Path cert:\LocalMachine\Root;\
    Import-Certificate -Filepath "C:\\certs\\mycompany.org-cert1.cer";\
    Import-Certificate -Filepath "C:\\certs\\mycompany.org-cert2.cer";\
    Get-ChildItem;

RUN Import-Module WebAdministration;                                                        \
    $websitePath = 'C:\WebHost\SiteExtensions\Functions';                                   \
    Set-ItemProperty 'IIS:\Sites\Default Web Site\' -name physicalPath -value $websitePath; \
    Set-ItemProperty 'IIS:\Sites\Default Web Site\' -name serverAutoStart -value 'true';    \
    Set-ItemProperty 'IIS:\AppPools\DefaultAppPool\' -name autoStart -value 'true';

EXPOSE 80 
```

Enter fullscreen mode Exit fullscreen mode

具体哪些可以被 App 引用`Dockerfile`比如:

```
FROM mycompanycr.azurecr.io/functions.webhost:1.0.11612
SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

COPY App.zip App.zip

RUN Expand-Archive App.zip ; \
    Remove-Item App.zip

SHELL ["cmd", "/S", "/C"]
ENV AzureWebJobsScriptRoot='C:\App'

WORKDIR App 
```

Enter fullscreen mode Exit fullscreen mode

每一个 Azure Function host release 都用相应的 release 标签加载到容器注册表中。这允许使用功能主机的不同(已验证或初步)版本来操作功能应用。

### MSI =托管服务身份

应用服务中运行的功能允许托管服务身份从 KeyVault 访问机密。

为了在我们的环境中实现同样的目标，我们首先必须[将托管服务身份添加到服务结构/虚拟机规模集](https://stackoverflow.com/questions/52578135/how-can-i-add-managed-service-identity-to-a-container-hosted-inside-azure-vm-sca/52578136#52578136)。

现在，上面介绍的`entry.PS1`启动脚本可以用来添加到 MSI 端点的路由，并在容器启动时检查它:

```
Write-Host  "adding route for Managed Service Identity"  $gateway  =  (Get-NetRoute  |  Where-Object  {$_.DestinationPrefix  -eq  '0.0.0.0/0'}).NextHop  $arguments  =  'add','169.254.169.0','mask','255.255.255.0',$gateway  &'route'  $arguments  $response  =  Invoke-WebRequest  -Uri  'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F'  -Method  GET  -Headers  @{Metadata="true"}  -UseBasicParsing  Write-Host  "MSI StatusCode :"  $response.StatusCode  C:\ServiceMonitor.exe  w3svc 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 第 1 章——学习

好了，问题解决了。

但是:

*   Windows 服务器核心映像的大小约为 6GB，因此服务结构节点需要大量时间来加载这些映像的新版本
*   时间在流逝

使用 Azure 函数 v2 和。NET Core 有可能大幅缩减图像的大小，并在 Linux 上托管这些图像。

# 第二章——Azure Functions/web host v2

在前一章中，解释了为什么以及如何将现在已经过时的 Azure 功能 v1 WebHost 在容器内工作到服务结构中。

对于 v2，对 Azure 函数的容器化有了更多的支持。Docker Hub 上已经有了[图像，GitHub](https://hub.docker.com/_/microsoft-azure-functions-base) 上已经有了用于 Windows 和 Linux 的[基本图像样本，它们提供了我必须自己在 v1 中弄清楚的东西。](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/nanoserver-1803/Dockerfile)

但是:

*   纳米服务器小磁盘映像不能是...太小
*   Nanoserver `microsoft/dotnet:2.1-aspnetcore-runtime-nanoserver-1803`基本映像中没有 PowerShell 这是我在 v1 中实现的调整所需要的
*   PowerShell Core 还没有证书 cmdlets。那么，如何将我的公司证书加载到映像中呢？

## 解决基本问题

### 增加 Nanoserver smalldisk OS 磁盘

这个问题的解决方案在堆栈溢出中描述:

*   [如何为基于 Windows Server smalldisk 的 VMSS 或服务结构集群扩展操作系统磁盘？](https://stackoverflow.com/questions/51336867/how-can-i-extend-os-disks-for-windows-server-smalldisk-based-vmss-or-service-fab)
*   [如何将数据磁盘附加到 Windows Server Azure VM 并直接在模板中格式化？](https://stackoverflow.com/questions/51205573/how-can-i-attach-a-data-disk-to-a-windows-server-azure-vm-and-format-it-directly)

### 添加 PowerShell 核心

以上面提到的示例`Dockerfile`为例，实现一个多阶段构建，然后允许在 PowerShell Core:
中运行`entry.PS1`脚本

```
# escape=`

# --------------------------------------------------------------------------------
# PowerShell
FROM mcr.microsoft.com/powershell:nanoserver as ps
...
# --------------------------------------------------------------------------------
# Runtime image
FROM microsoft/dotnet:2.2-aspnetcore-runtime-nanoserver-1803

COPY --from=installer-env ["C:\\runtime", "C:\\runtime"]

COPY --from=ps ["C:\\Program Files\\PowerShell", "C:\\PowerShell"]
...
USER ContainerAdministrator
CMD ["C:\\PowerShell\\pwsh.exe","C:\\entry.PS1"] 
```

Enter fullscreen mode Exit fullscreen mode

### 添加 certoc.exe 安装证书

证书安装的相同方法:只是借用另一个映像

```
...
# --------------------------------------------------------------------------------
# Certificate Tool image
FROM microsoft/nanoserver:sac2016 as tool
...
ADD Certificates\\mycompany.org-cert1.cer C:\\certs\\mycompany.org-cert1.cer
ADD Certificates\\mycompany.org-cert2.cer C:\\certs\\mycompany.org-cert2.cer
ADD host_secret.json C:\\runtime\\Secrets\\host.json
ADD entry.PS1 C:\\entry.PS1

USER ContainerAdministrator
RUN icacls "c:\runtime\secrets" /t /grant Users:M
RUN certoc.exe -addstore root C:\\certs\\mycompany.org-cert1.cer
RUN certoc.exe -addstore root C:\\certs\\mycompany.org-cert2.cer
USER ContainerUser
... 
```

Enter fullscreen mode Exit fullscreen mode

与 Windows Server Core 1803 和 Nanoserver 1803 基本映像相比的重大变化还要求切换用户上下文以导入证书。

### 处理机密

正如上面的`Dockerfile`示例所示，目录 ACL 也需要修改，以便在用户上下文中运行的主机能够写入 secrets 文件夹。

[检出堆栈溢出:系统。UnauthorizedAccessException:在 Azure Functions Windows 容器](https://stackoverflow.com/questions/53683035/system-unauthorizedaccessexception-access-to-the-path-c-runtime-secrets-host)中对路径“C:\runtime\Secrets\host.json”的访问被拒绝

### 微星

此外，在切换到 Nanoserver 和 PowerShell 核心后，MSI 部分需要一些调整。直到`Get-NetRoute` cmdlet 在 PowerShell Core 中不可用，这个奇怪的字符串管道练习才被要求提取默认网关。

```
Write-Host  "adding route for Managed Service Identity"  $gateway  =  (route  print  |  ?  {$_  -like  "*0.0.0.0*0.0.0.0*"}  |  %  {$_  -split  " "}  |  ?  {$_.trim()  -ne  ""  }  |  ?  {$_  -ne  "0.0.0.0"  })[0]  $arguments  =  'add',  '169.254.169.0',  'mask',  '255.255.255.0',  $gateway  &'route'  $arguments  # --------------------------------------------------------------------------------  # test MSI access  $response  =  Invoke-WebRequest  -Uri  'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F'  -Method  GET  -Headers  @{Metadata  =  "true"}  -UseBasicParsing  Write-Host  "MSI StatusCode :"  $response.StatusCode  # --------------------------------------------------------------------------------  # start Function Host  dotnet.exe  C:\runtime\Microsoft.Azure.WebJobs.Script.WebHost.dll 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 增加更多功能

### @微软。密钥库(SecretUri=...)应用程序设置

[App 服务中的 Azure 函数支持应用设置中的`@Microsoft.KeyVault()`语法。](https://azure.microsoft.com/sv-se/blog/simplifying-security-for-serverless-and-web-apps-with-azure-functions-and-app-service/)为了实现与容器中的环境变量相同的效果，这个脚本扩展完成了转换:

```
...  # --------------------------------------------------------------------------------  # replace Environment Variables holding KeyVault URIs with actual values  $msiEndpoint  =  'http://169.254.169.254/metadata/identity/oauth2/token'  $vaultTokenURI  =  'https://vault.azure.net&api-version=2018-02-01'  $authenticationResult  =  Invoke-RestMethod  -Method  Get  -Headers  @{Metadata  =  "true"}  -Uri  ($msiEndpoint  +  '?resource='  +  $vaultTokenURI)  if  ($authenticationResult)  {  $requestHeader  =  @{Authorization  =  "Bearer $($authenticationResult.access_token)"}  $regExpr  =  "^@Microsoft.KeyVault\(SecretUri=(.*)\)$"  Get-ChildItem  "ENV:*"  |  Where-Object  {$_.Value  -match  $regExpr}  |  ForEach-Object  {  Write-Host  "fetching secret for"  $_.Key  $kvUri  =  [Regex]::Match($_.Value,  $regExpr).Groups[1].Value  if  (!$kvUri.Contains("?api-version"))  {  $kvUri  +=  "?api-version=2016-10-01"  }  $creds  =  Invoke-RestMethod  -Method  GET  -Uri  $kvUri  -ContentType  'application/json'  -Headers  $requestHeader  if  ($creds)  {  Write-Host  "setting secret for"  $_.Key  [Environment]::SetEnvironmentVariable($_.Key,  $creds.value,  "Process")  }  }  }  # --------------------------------------------------------------------------------  # start Function Host  dotnet.exe  C:\runtime\Microsoft.Azure.WebJobs.Script.WebHost.dll 
```

Enter fullscreen mode Exit fullscreen mode

# 第三章——解开单身之谜

在将函数应用从 v1 逐步迁移到 v2 时，我意识到，突然之间，定时器触发函数的单独执行不再适用于 v2。在 v1 中，您可以将跨多个容器执行的相同功能应用程序的所有实例的通用主机 id 放入`host.json` :

```
{
  "id": "4c45009422854e56a8a70567cd7219fe",
... 
```

Enter fullscreen mode Exit fullscreen mode

然后，WebHost 实例锁定或同步共享存储上的单例执行(由`AzureWebJobsStorage`引用)。

迁移到 v2 的函数在相同的时间间隔突然执行了多次(正好是同一个函数 app 的容器数),这绝对不是预期的行为。`host.json`中指定的`id`似乎不再相关。

在 v2 主机中检查`ScriptHostIdProvider`我了解到`id`可以在环境变量
中设置

```
...
AzureFunctionsWebHost:hostid=4c45009422854e56a8a70567cd7219fe
... 
```

Enter fullscreen mode Exit fullscreen mode

通常平台(Azure Functions / App Service)关心的是设置这个唯一的 id。但是，当在多个实例中托管函数运行时，必须注意这一点。

尽管如此，函数运行时的制造者并不喜欢设置一个显式的`hostid`

*   [https://github.com/Azure/Azure-Functions/issues/809](https://github.com/Azure/Azure-Functions/issues/809)
*   [https://github . com/Azure/Azure-functions-core-tools/issues/1012](https://github.com/Azure/azure-functions-core-tools/issues/1012)

并在主机启动时发出警告:

```
warn: Host.Startup[0]
      Host id explicitly set in configuration. This is not a recommended configuration and may lead to unexpected behavior.
info: Host.Startup[0]
      Starting Host (HostId=4c45009422854e56a8a70567cd7219fe, InstanceId=181fb9ee-be21-4c7e-bcf1-c325fce7532b, Version=2.0.12353.0, ProcessId=5804, AppDomainId=1, InDebugMode=False, InDiagnosticMode=False, FunctionsExtensionVersion=) 
```

Enter fullscreen mode Exit fullscreen mode

> 重要提示:当在多个功能应用程序之间共享相同的存储空间(由`AzureWebJobsStorage`引用)时，`hostid`对于每个功能应用程序必须是唯一的。否则国内功能 app 锁定和持久功能会搞砸。

# 第 4 章 MSI 上的 ServiceBus NamespaceManager

因为 NamespaceManager 不再受。NET Core 兼容包`Microsoft.Azure.ServiceBus`(当在 WebJobs 或函数中使用服务总线时，它是`Microsoft.Azure.WebJobs.Extensions.ServiceBus`的依赖项)，必须使用包`Microsoft.Azure.Management.ServiceBus.Fluent`和附属包。

这个包支持基于 MSI 的认证，我可以利用 MSI 的可用性(我在第 2 章中描述过):

```
...
    // some magic that determines subscriptionId, resourceGroupName & sbNamespaceName
...
    var credentials = SdkContext.AzureCredentialsFactory.FromMSI(new MSILoginInformation(MSIResourceType.VirtualMachine), AzureEnvironment.AzureGlobalCloud);
    var azure = Azure
            .Configure()
            .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
            .Authenticate(credentials)
            .WithSubscription(subscriptionId);

    var sbNamespace = azure.ServiceBusNamespaces.GetByResourceGroup(resourceGroupName, sbNamespaceName);
    var queues = sbNamespace.Queues.List();
... 
```

Enter fullscreen mode Exit fullscreen mode

剩下的唯一一件事就是授权在 AAD 中为服务总线资源上设置的集群 VM 规模创建的 MSI 例如，在我的例子中，当只需要检索队列消息计数时，授予一个 *Reader* 角色。