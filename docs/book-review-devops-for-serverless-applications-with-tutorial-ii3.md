# 书评:面向无服务器应用的 DevOps(带教程)

> 原文：<https://dev.to/kayis/book-review-devops-for-serverless-applications-with-tutorial-ii3>

在我成为全栈开发人员的过程中，我决定将无服务器作为我的后端选择。为了获得如何保持事情正常运行的感觉，我试图在 DevOps 方面接受一些教育，因此我阅读了[devo PS for server less Applications](https://www.amazon.com/DevOps-Serverless-Applications-Seamlessly-environment-ebook/dp/B07DH9T53M)。

> DevOps(“开发”和“运营”的缩写)是一种软件开发方法，它将软件开发(Dev)和信息技术运营(Ops)结合在一起。DevOps 的目标是缩短系统开发的生命周期，同时提供与业务目标紧密一致的特性、修复和更新。([维基百科](https://en.wikipedia.org/wiki/DevOps))

## 我喜欢“面向无服务器应用的开发运维”的什么

作者写了多种框架和云提供商，而不仅仅是“无服务器和 AWS”这使他能够谈论每种方法的细微差别，并从总体上让您对 DevOps 有更广泛的理解。

他还深入探讨了监控和排除无服务器系统故障的最佳实践，以便您可以使用作者的经验。我认为这些也是这本书最精彩的章节。“是的，起初看起来那样做是个好主意，但不要这样做，几个月后你会后悔的！”

## 我不喜欢“无服务器应用的开发运维”的地方

嗯，我认为这本书本来可以更短，不是因为它在许多页上有无用的文字，而是因为它谈论了太多的事情。

当然，作者使用了所有这些框架和平台，这很好，可以给我一个好主意，告诉我要做什么以及为什么要做，所有的平台都很好，可以证明所有的想法。对我来说，作为一个 AWS 用户，在一个小节而不是整个章节中提到 Google 和 IBM 的无服务器平台是可以的。

但这是个人偏好，如果你对整个生态系统感兴趣，读这本书并没有错。:)

## 教程

承蒙作者的好意，我为你准备了一个教程，所以你可以画出他的风格。Shashikant Bangera 将讲解如何“为 kubeless 添加 DevOps 风味”。

## 为 Kubeless 添加 DevOps 风味

### 什么是无功受禄？

Kubeless 是一个基于 Kubernetes 的开源无服务器框架。它允许您部署和执行一段代码，而不用担心底层的基础设施。它使用 Kubernetes 的资源来提供自动缩放、路由和监控。

部署后功能可以通过发布订阅、HTTP 和调度来触发。pub-sub 事件通过 Kafka 集群进行管理，Kafka 集群是 Kubeless 中的一个现成组件，由一个基本的 Kafka 集群和一个代理和一个动物园管理员组成。HTTP 触发器可通过 Kubernetes 服务获得，预定的函数转换成 cron 作业。

Kubeless 支持的语言/运行时有

*   python2.7/3.4/3.6
*   节点 6/8/分布式 8
*   卢布 2.4
*   php7.2
*   go1.10
*   点网核心 2.0
*   java1.8
*   芭蕾舞演员
*   jvm1.8
*   Kubeless 还支持 HTTP、NATS、Kafka、cron 和流触发器。

### 无库建筑

Kubeless 使用自定义资源定义，这意味着当您创建自定义资源定义时，Kubernetes API 服务器会为每个指定的版本创建一个资源路径。

自定义资源定义可以是命名空间的，也可以是集群范围的，所以 CRD 被称为函数，这意味着无库函数可以在后台创建为普通的 Kubernetes 资源，并创建一个控制器。控制器将监视这些自定义资源，并根据运行时需求启动。

### 如何设置 Kubeless

建立无库是相当容易的。首先，从发布页面下载 Kubeless，创建一个名称空间，然后，通过发布页面上的 YAML 清单，创建函数的自定义资源定义并启动一个控制器。如果你在你的个人笔记本电脑上设置 Kubeless，那么你必须使用 minikube 来完成这项工作。

首先，去[https://github.com/kubernetes/minikube](https://github.com/kubernetes/minikube)设置 minikube。一旦安装了 minikube，您应该能够在一个虚拟机中拥有一个单节点 Kubernetes 集群。您还应该能够通过命令提示符执行 minikube 命令。

创建一个集群，然后在该集群中创建无库资源。然后，创建一个简单的无库功能，然后，部署和调用它。您还将为 minikube 设置一个仪表板，并分别查看控制器和函数是如何创建和部署的。下面是实现这一点的方法:

(1)创建一个 minikube 本地 Kubernetes 集群:

```
$ minikube start
Starting local Kubernetes v1.9.0 cluster...
Starting VM...
Getting VM IP address...
Moving files into cluster...
Setting up certs...
Connecting to cluster...
Setting up kubeconfig...
Starting cluster components...
Kubectl is now configured to use the cluster.
Loading cached images from config file. 
```

Enter fullscreen mode Exit fullscreen mode

(2)接下来，使用以下命令创建一个 minikube 仪表板。命令触发器将打开带有仪表板的浏览器。仪表板将向您显示服务、窗格和管理器。

```
$ minikube dashboard 
```

Enter fullscreen mode Exit fullscreen mode

(3)现在您有了一个集群，向它部署 Kubeless。对于多个 Kubernetes 环境(非 RBAC、RBAC 和 OpenShift)，有几个可用的 Kubernetes 清单。在这里，您将使用非 RBAC(非基于角色的访问控制)的清单。

```
$ export RELEASE=$(curl -sk
https://api.github.com/repos/kubeless/kubeless/releases/latest | grep tag_name |
cut -d '"' -f 4)
$ kubectl create ns kubeless
$ echo $RELEASE
$ kubectl create -f
https://github.com/kubeless/kubeless/releases/download/v1.0.0-alpha.8/kubeless-
non-rbac-v1.0.0-alpha.8.yaml
serviceaccount "controller-acct" created
customresourcedefinition "functions.kubeless.io" created
customresourcedefinition "httptriggers.kubeless.io" created
customresourcedefinition "cronjobtriggers.kubeless.io" created
configmap "kubeless-config" created
deployment "kubeless-controller-manager" created 
```

Enter fullscreen mode Exit fullscreen mode

(4)现在您已经部署了 Kubeless，请检查它是否已正确部署:

```
$ kubectl get pods -n kubeless
NAME READY STATUS RESTARTS AGE
kubeless-controller-manager-c6b69df76-65gsh 1/1 Running 0 2m
$ kubectl get deployment -n kubeless
NAME DESIRED CURRENT UP-TO-DATE AVAILABLE AGE
kubeless-controller-manager 1 1 1 1 3m
$ kubectl get customresourcedefinition
NAME AGE
cronjobtriggers.kubeless.io 3m
functions.kubeless.io 3m
httptriggers.kubeless.io 3m 
```

Enter fullscreen mode Exit fullscreen mode

(5)接下来，在本地安装无库 CLI，用于部署、调用和删除无库功能:

```
$ export OS=$(uname -s| tr '[:upper:]' '[:lower:]')
$ curl -OL
https://github.com/kubeless/kubeless/releases/download/$RELEASE/kubeless_$OS-
amd64.zip
$ unzip kubeless_$OS-amd64.zip
$ sudo mv bundles/kubeless_$OS-amd64/kubeless /usr/local/bin/ 
```

Enter fullscreen mode Exit fullscreen mode

(6)现在，创建一个功能并部署它。以下是您将部署和调用的简单 Python 函数。创建一个名为`test.py`的函数，内容如下:

```
def hello(event, context):
  print event
  return event['data'] 
```

Enter fullscreen mode Exit fullscreen mode

(7)现在部署功能。当您刷新 minikube 仪表板时，您应该能够看到部署在那里的`hello`函数:

```
$ kubeless function deploy hello --runtime python2.7--from-file test.py --handler test.hello --namespace kubeless
INFO[0000] Deploying function...
INFO[0000] Function hello submitted for deployment
INFO[0000] Check the deployment status executing 'kubeless function ls hello' 
```

Enter fullscreen mode Exit fullscreen mode

下面的列表解释了上述代码的各种元素:

*   `kubeless function deploy hello`告诉 Kubeless 注册一个名为`hello`的新函数。使用此名称可以通过网络访问该功能。请注意，这不需要与代码中使用的函数名相同。
*   `--trigger-http`告诉 Kubeless 将通过 HTTP 调用该函数。
*   `--runtime python2.7`告诉 Kubeless 使用 Python 2.7 执行代码。Node 也支持作为运行时，将来会有更多的支持。
*   告诉 Kubeless 要在代码模块内部调用的函数的名称。您可以在前面的 Python 代码中看到该函数被称为`hello`。
*   `--from-file /tmp/hello.py`告诉 Kubeless 上传并使用`/tmp/hello.py`文件作为函数的源。也可以用其他方式传递函数。

您将看到通过以下命令创建的函数自定义资源:

```
$ kubectl get functions
NAME AGE
hello 2m

$ kubeless function ls --namespace kubeless
NAME NAMESPACE HANDLER RUNTIME DEPENDENCIES STATUS
hello kubeless test.hello python2.7 1/1 READY 
```

Enter fullscreen mode Exit fullscreen mode

(8)现在，调用函数，如下:

```
$ kubeless function call hello --data 'Hello Serverless!' --namespace kubeless
Hello Serverless! 
```

Enter fullscreen mode Exit fullscreen mode

(9)您也可以删除该功能，如下:

```
$ kubeless function delete hello --namespace kubeless
$ kubeless function ls --namespace kubeless
NAME NAMESPACE HANDLER RUNTIME DEPENDENCIES STATUS 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，您已经在本地安装了 Kubeless，创建了一个简单的函数，部署了它，调用了它，并取消了部署。在下一节中，您将学习如何使用无服务器框架自动化部署。

### 设置持续集成和部署

您将使用无服务器框架来启动无服务器功能的开发和部署。无服务器框架已经提供了许多特性，无需太多努力就可以采用。这里看一下无服务器提供的各种功能。

**服务的创建**

使用`create`命令创建使用运行时传递的基本服务，使用`path`创建目录。目前，提供了两个运行时——一个是 Python，另一个是 Node.js。因此，如果您运行以下带有`path`参数的命令，它将创建一个具有简单无服务器功能的文件夹。目前可用的运行时间有`kubeless-python`和`kubeless-nodejs` :

```
$ serverless create --template kubeless-python --path myKubelessFunc 
```

Enter fullscreen mode Exit fullscreen mode

`create`命令将创建一个服务，每个服务配置将有以下三个文件:

*   `serverless.yml`:这个文件的主要职责是声明服务，定义提供者，自定义插件(在本例中是无服务器-无库插件)，以及函数将要执行的事件或触发器，使用无服务器变量配置文件。
*   `handler.py`:该文件将包含功能代码。带有`serverless.yml`的函数定义将指向`handler.py`。
*   这是你的函数的 npm 包定义文件，包含所有的依赖项和无服务器插件。

用您的函数和配置更新这些文件，如下面的代码所示。您正在更新一个函数，以便从通过 JSON 公开的站点提要中搜索自行车站点。该代码也可以在 GitHub 资源库的`https://github.com/shzshi/kubeless-serverless.git` :
中找到

```
#handler.py import urllib2
import json
def find(event, context):
  term = event['data']['term']
  url = "https://feeds.capitalbikeshare.com/stations/stations.json"
  response = urllib2.urlopen(url)
  stations = json.loads(response.read())
  hits = []
  for station in stations["stationBeanList"]:
    if station["stAddress1"].find(term) > -1: hits.append(station)
  return json.dumps(hits) 
```

Enter fullscreen mode Exit fullscreen mode

将`serverless.yaml`替换为以下内容:

```
# serverless.yml
service:bikesearch
provider:
  name:kubeless
  runtime:python2.7
plugins:
  -serverless-kubeless
functions:
  bikesearch:
    handler:handler.find 
```

Enter fullscreen mode Exit fullscreen mode

**部署功能**

因为所需的文件是通过模板创建的，所以您可以根据需要修改它们，然后在需要时简单地部署和调用它们。使用无服务器部署它们；然后`npm install`将获得无服务器所需的依赖项，比如`kubeless-serverless`插件。然后，您可以部署该功能，如下面的代码所示:

```
$ npm install
$ serverless deploy -v
Serverless: Packaging service...
Serverless: Excluding development dependencies...
Serverless: Deploying function bikesearch...
Serverless: Pods status: {"waiting":{"reason":"PodInitializing"}}
Serverless: Function bikesearch successfully deployed
Serverless: Skipping ingress rule generation 
```

Enter fullscreen mode Exit fullscreen mode

**调用功能**

可以通过命令行或者通过 Kubeless 提供的 UI 调用 Kubeless 函数。可以通过本地下载文件并运行它，使用 Docker 映像和 Docker 文件，或者通过存储库提供的 Kubernetes 清单来提供无 Kubernetes。这里使用了 Kubernetes 清单，如下面的代码所示:

```
$ kubectl create -f https://raw.githubusercontent.com/kubeless/kubeless-ui/master/k8s.yaml
serviceaccount "ui-acct" created
clusterrole "kubeless-ui" created
clusterrolebinding "kubeless-ui" created
deployment "ui" created
service "ui" created
$ minikube service ui -n kubeless 
```

Enter fullscreen mode Exit fullscreen mode

`minikube`命令将弹出一个浏览器并打开一个 UI。UI 能够创建、编辑、调用和删除函数，所以调用您部署的函数。在`textarea`请求中添加`{"term":"New York"}`，选择**请求为**，点击**运行功能**。该功能将成功执行，并显示站数据响应输出。

您也可以通过无服务器框架调用相同的函数，该函数将执行并获取所需的数据，如下面的代码所示:

```
$ serverless invoke --function bikesearch --data '{"term":"Albemarle"}' -l
Serverless: Calling function: bikesearch...
--------------------------------------------------------------------
[ { availableDocks: 12,
totalDocks: 15,
city: '',
altitude: '',
stAddress2: '',
longitude: -77.079382,
lastCommunicationTime: '2018-08-15 04:16:15 PM',
postalCode: '',
statusValue: 'In Service',
testStation: false,
stAddress1: 'Tenleytown / Wisconsin Ave &amp; Albemarle St NW',
stationName: 'Tenleytown / Wisconsin Ave &amp; Albemarle St NW',
landMark: '',
latitude: 38.947607,
statusKey: 1,
availableBikes: 1,
id: 80,
location: '' } ] 
```

Enter fullscreen mode Exit fullscreen mode

如果出了问题会怎么样？您没有适当的错误处理，但是您可以通过在调用函数时输入错误来测试日志记录。所以，调用数据中有错误的函数，如下面的代码所示:

```
$serverless invoke --function bikesearch --data '{"trm":"Albemarle"}' -l
Serverless: Calling function: bikesearch...

  Error --------------------------------------------------

  Internal Server Error

    For debugging logs, run again after setting the "SLS_DEBUG=*" environment variable.

  Get Support --------------------------------------------
    Docs: docs.serverless.com
    Bugs: github.com/serverless/serverless/issues
    Forums: forum.serverless.com
    Chat: gitter.im/serverless/serverless

  Your Environment Information -----------------------------
    OS: darwin
    Node Version: 6.10.3
    Serverless Version: 1.26.1 
```

Enter fullscreen mode Exit fullscreen mode

Serverless 返回了一个带有 500 服务器代码的错误消息，这是 web 框架应该有的。但是，为了更好地调试错误的来源，查看 Python 堆栈跟踪会很有用。因此，获取日志来查看错误是什么:

```
$ serverless logs -f bikesearch
Hit Ctrl-C to quit.
172.17.0.1 - - [15/Aug/2018:20:17:18 +0000] "POST / HTTP/1.1" 200 460 "" "" 0/934928
172.17.0.1 - - [15/Aug/2018:20:17:18 +0000] "GET /healthz HTTP/1.1" 200 2 "" "kube-probe/." 0/133
172.17.0.1 - - [15/Aug/2018:20:17:48 +0000] "GET /healthz HTTP/1.1" 200 2 "" "kube-probe/." 0/72
172.17.0.1 - - [15/Aug/2018:20:18:18 +0000] "GET /healthz HTTP/1.1" 200 2 "" "kube-probe/." 0/108
172.17.0.1 - - [15/Aug/2018:20:18:48 +0000] "GET /healthz HTTP/1.1" 200 2 "" "kube-probe/." 0/123
172.17.0.1 - - [15/Aug/2018:20:19:18 +0000] "GET /healthz HTTP/1.1" 200 2 "" "kube-probe/." 0/74
172.17.0.1 - - [15/Aug/2018:20:19:48 +0000] "GET /healthz HTTP/1.1" 200 2 "" "kube-probe/." 0/138
172.17.0.1 - - [15/Aug/2018:20:20:18 +0000] "GET /healthz HTTP/1.1" 200 2 "" "kube-probe/." 0/75
172.17.0.1 - - [15/Aug/2018:20:20:48 +0000] "GET /healthz HTTP/1.1" 200 2 "" "kube-probe/." 0/149
172.17.0.1 - - [15/Aug/2018:20:21:18 +0000] "GET /healthz HTTP/1.1" 200 2 "" "kube-probe/." 0/187
172.17.0.1 - - [15/Aug/2018:20:21:48 +0000] "GET /healthz HTTP/1.1" 200 2 "" "kube-probe/." 0/147
172.17.0.1 - - [15/Aug/2018:20:22:18 +0000] "GET /healthz HTTP/1.1" 200 2 "" "kube-probe/." 0/71
172.17.0.1 - - [15/Aug/2018:20:22:47 +0000] "POST / HTTP/1.1" 200 2131 "" "" 1/232988
Traceback (most recent call last):
 File "/usr/local/lib/python2.7/dist-packages/bottle.py", line 862, in _handle
return route.call(**args)
 File "/usr/local/lib/python2.7/dist-packages/bottle.py", line 1740, in wrapper
 rv = callback(*a, **ka)
 File "/kubeless.py", line 76, in handler
 raise res
KeyError: 'term' 
```

Enter fullscreen mode Exit fullscreen mode

从 **KeyError** 短语中可以清楚地看出，函数失败是因为错误的键名，所以这可以让您了解出了什么问题。但是在类似生产的环境中，您需要有更复杂的错误处理方法。

**与詹金斯的持续整合**

关于 Kubeless，使用无服务器框架，只有在本地设置了 Kubernetes 集群(minikube)、无服务器框架和 Jenkins 之后，才能设置部署。没有规定使用无服务器框架设置远程部署。但是随着无服务器框架和 Kubeless 的逐渐成熟，这些功能将会被添加进来。在这里，创建文件
以在本地设置部署。

如果您在[https://github . com/shzshi/kubeless-continuous-integration . git](https://github.com/shzshi/kubeless-continuous-integration.git)处克隆存储库，您应该能够使用这个模板在本地设置持续集成。但是，如果安装并配置了无服务器框架，您可以在笔记本电脑上本地运行这些文件。

下面是如何做到这一点:

```
$ git clone https://github.com/shzshi/kubeless-continuous-integration.git
$ cd kubeless-continuous-integration 
```

Enter fullscreen mode Exit fullscreen mode

您应该在这个文件夹中看到六个文件和一个目录，但是在本教程中您不会使用 Dockerfiles 和 Jenkinsfiles。一旦远程部署可以通过无服务器的 Kubeless 框架实现，就可以使用它们，如下面的代码所示:

```
$ npm install
$ npm test
> kubeless-nodejs@1.0.0 test /Users/shashi/Documents/packt/chapter7/kubeless-
continuous-integration
> mocha ./test/*.js
kubelesshello
✓ should return 0 when "Hello Kubeless" is present
1 passing (8ms) 
```

Enter fullscreen mode Exit fullscreen mode

您运行了`npm install`来获得无服务器框架和 Node.js 应用程序和测试所需的依赖关系。发布这篇文章后，您运行了 npm 测试，为此您创建了一个简单的单元测试来检查您的功能的健全性，然后将它们部署到集群中，如下面的代码所示:

```
$ serverless deploy -v
Serverless: Packaging service...
Serverless: Excluding development dependencies...
Serverless: Deploying function kubelesshello...
Serverless: Pods status: {"waiting":{"reason":"PodInitializing"}}
Serverless: Pods status: {"waiting":{"reason":"PodInitializing"}}
Serverless: Function kubelesshello successfully deployed
Serverless: Skipping ingress rule generation 
```

Enter fullscreen mode Exit fullscreen mode

所以，您的 Node.js 函数将被成功部署，您可以在本地或通过 Kubeless UI 调用它并测试它。调用后，您应该得到`Hello Kubeless`的输出，如下面的代码所示:

```
$ serverless invoke -f kubelesshello -l
Serverless: Calling function: kubelesshello...
--------------------------------------------------------------------
Hello Kubeless 
```

Enter fullscreen mode Exit fullscreen mode

**监控无底洞**

您可以使用 Prometheus 监控`kubeless`功能。Prometheus 有一个内置的运行时支持，它会自动收集每个函数的指标。Prometheus 将在默认仪表板上显示这些指标。

普罗米修斯指标可以通过 Grafana 可视化。Grafana dashboard 可以通过 Kubeless
提供的 dashboard JSON 样例文件进行配置，该文件位于[https://github . com/kube less/kube less/blob/master/docs/misc/kube less-Grafana-dashboard . JSON](https://github.com/kubeless/kubeless/blob/master/docs/misc/kubeless-grafana-dashboard.json)。

*如果您对本文感兴趣，您可以探索[无服务器应用的 DevOps](https://www.amazon.com/DevOps-Serverless-Applications-Seamlessly-environment/dp/1788623444)，使用 devo PS 原理为您的无服务器应用建立完整的 CI 和 CD 管道。DevOps for server less Applications 将带您了解不同的 devo PS 相关场景，为您的无服务器部署打下坚实的基础。*