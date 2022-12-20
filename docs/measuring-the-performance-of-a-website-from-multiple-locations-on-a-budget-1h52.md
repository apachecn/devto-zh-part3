# 根据预算从多个位置衡量网站的性能

> 原文：<https://dev.to/goenning/measuring-the-performance-of-a-website-from-multiple-locations-on-a-budget-1h52>

## 全球访问者网站的全球指标

在当前互联网的全球规模下，从全球多个位置测量网站的性能至关重要。在大多数情况下，你的访客不仅来自你的国家，还来自其他国家。从加拿大到澳大利亚，从智利到俄罗斯，访问你的网站的人比你想象的要多。

人们很容易忘记这一事实，简单地从本地机器上测量网站的性能，因为本地机器通常靠近网站数据中心。不仅如此，这台机器可能也是一头野兽，它可以在半秒钟内打开谷歌 Chrome 的多个标签，加载任何含有大量 JavaScript 的页面。

但实际上，您的访问者使用的是 10 年前的笔记本电脑，安装的是 Windows 7，并通过 3G 网络连接到互联网。您的数据中心位于旧金山，这些访问者来自菲律宾。甚至你的智能手机也比他们的速度快 10 倍，延迟更低。

有许多付费服务可以让你从世界各地的多个地方持续监控你的网站性能。

但也许你是个极客，想自己动手？

在这篇文章中，我想向你展示如何使用开源工具以较低的月成本从多个地点收集网站性能。

## 网页-计时

[goenning/网页计时](https://github.com/goenning/webpage-timing)是 Node.js 应用程序，使用 Headless Chrome 从网页收集性能指标。

这个应用程序也可以作为 Docker 镜像使用，所以你可以这样运行它:

```
$ docker run goenning/webpage-timing 
```

Enter fullscreen mode Exit fullscreen mode

继续，试一试，在你的终端上执行这个。

您应该会得到类似如下的响应:

```
{  "start":  "2019-02-10T16:32:25.655Z",  "start_ts":  1549816345655,  "duration":  430,  "origin":  "iMac.local",  "request_url":  "https://example.org",  "metrics":  {  "Timestamp":  300473.456095,  "Documents":  2,  "Frames":  1,  "JSEventListeners":  0,  "Nodes":  39,  "LayoutCount":  1,  "RecalcStyleCount":  2,  "LayoutDuration":  0.053402,  "RecalcStyleDuration":  0.000584,  "ScriptDuration":  0.000015,  "TaskDuration":  0.068091,  "JSHeapUsedSize":  2108152,  "JSHeapTotalSize":  3936256  },  "entries":  [  {  "name":  "https://example.org/",  "entryType":  "navigation",  "startTime":  0,  "duration":  427.6149999932386,  "initiatorType":  "navigation",  "nextHopProtocol":  "h2",  "workerStart":  0,  "redirectStart":  0,  "redirectEnd":  0,  "fetchStart":  0.2899999963119626,  "domainLookupStart":  7.634999987203628,  "domainLookupEnd":  8.984999964013696,  "connectStart":  8.984999964013696,  "connectEnd":  313.34999995306134,  "secureConnectionStart":  0,  "requestStart":  313.6149999918416,  "responseStart":  414.7049999446608,  "responseEnd":  415.8099999767728,  "transferSize":  800,  "encodedBodySize":  606,  "decodedBodySize":  1270,  "serverTiming":  [],  "unloadEventStart":  0,  "unloadEventEnd":  0,  "domInteractive":  426.6899999929592,  "domContentLoadedEventStart":  426.7049999907613,  "domContentLoadedEventEnd":  426.7049999907613,  "domComplete":  427.6049999753013,  "loadEventStart":  427.6149999932386,  "loadEventEnd":  427.6149999932386,  "type":  "navigate",  "redirectCount":  0  },  {  "name":  "first-paint",  "entryType":  "paint",  "startTime":  491.65999999968335,  "duration":  0  },  {  "name":  "first-contentful-paint",  "entryType":  "paint",  "startTime":  491.68499995721504,  "duration":  0  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

这是一个`Timing`对象，它包含一些从容器内部执行的无头 Chrome 收集的信息。上例中，加载 https://example.org**[用了 **430ms** 。如果这个页面有 CSS/JavaScript/Image 文件，它也会下载它，并且持续时间会更长。](https://example.org)**

您还可以指定一些自定义参数，这允许您从任何页面收集指标。

```
$ docker run -e REQUEST_URL=https://github.com/docker goenning/webpage-timing 
```

Enter fullscreen mode Exit fullscreen mode

您还会注意到,`entries`数组现在有了更多的条目，包括浏览器必须下载的所有 CSS/JavaScript/Image 文件。

另一个有用的参数是`MONGO_URL`，它允许您将`Timing`对象存储到 MongoDB 集合中，而不是打印到 stdout。当您想要保存一段时间内的执行历史并执行进一步的分析时，这是非常有用的。

```
# Replace the connection string below with your own
$ docker run -e MONGO_URL=mongodb://user:pass@your-server:port/db -e REQUEST_URL=https://github.com/docker goenning/webpage-timing 
```

Enter fullscreen mode Exit fullscreen mode

## 在全球基础设施上运行 it

到目前为止，我们已经看到，我们可以使用本地 Docker 容器从 web 页面收集指标，并将其存储在 MongoDB 中。

为了更进一步，我们将使用云上的机器从多个区域运行这个容器。您可以在您喜欢的云提供商上启动多个虚拟机，并在 cron 上调度这个脚本。但这并不十分经济高效，而且您还必须承担维护数十台虚拟机的负担。

但是有更好的方法😀

Azure 有一个名为 [Azure 容器实例](https://azure.microsoft.com/en-us/services/container-instances/)的服务。它允许您在云上运行 Docker 容器，而不必担心它背后的基础设施。最棒的？你只需按执行时间付费。如果你启动一个运行 5 秒钟的容器，你将支付 0.000080 美元。在这篇文章中，我将向你展示如何在 Azure 上执行这个操作，但是如果你更喜欢 AWS，搜索`AWS Fargate`，这是一个类似的服务，所以你可以应用这里提出的相同的想法。

我们要做的是在 17 个区域中的每一个区域创建几十个 Azure 容器实例，并配置它使用我们的自定义参数执行`goenning/webpage-timing`。我们还需要存储所有位置的数据，以便以后查询。在这个例子中，我将使用 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) ，因为它有一个免费层，也可以托管在 Azure 上。

假设您有一个 Azure 帐户，并且安装了 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) ，运行以下命令。

```
$ az login
$ az account set --subscription "<YOUR_SUBSCRIPTION NAME>" # you can skip this if your account has only one subscription 
```

Enter fullscreen mode Exit fullscreen mode

创建一个包含以下内容的文件【T0:】T1

```
apiVersion: 2018-06-01
type: Microsoft.ContainerInstance/containerGroups
location: $location$
name: $name$
properties:
  containers:
  - name: $name$-01
    properties:
      image: goenning/webpage-timing
      environmentVariables:
        - name: 'ORIGIN'
          value: '$location$'
        - name: 'REQUEST_URL'
          value: '$request_url$'
        - name: 'MONGO_URL'
          secureValue: '$mongo_url$'
      resources:
        requests:
          cpu: 0.4
          memoryInGb: 0.7
  - name: $name$-02
    properties:
      image: goenning/webpage-timing
      environmentVariables:
        - name: 'ORIGIN'
          value: '$location$'
        - name: 'REQUEST_URL'
          value: '$request_url$'
        - name: 'MONGO_URL'
          secureValue: '$mongo_url$'
      resources:
        requests:
          cpu: 0.4
          memoryInGb: 0.7
  - name: $name$-03
    properties:
      image: goenning/webpage-timing
      environmentVariables:
        - name: 'ORIGIN'
          value: '$location$'
        - name: 'REQUEST_URL'
          value: '$request_url$'
        - name: 'MONGO_URL'
          secureValue: '$mongo_url$'
      resources:
        requests:
          cpu: 0.4
          memoryInGb: 0.7
  - name: $name$-04
    properties:
      image: goenning/webpage-timing
      environmentVariables:
        - name: 'ORIGIN'
          value: '$location$'
        - name: 'REQUEST_URL'
          value: '$request_url$'
        - name: 'MONGO_URL'
          secureValue: '$mongo_url$'
      resources:
        requests:
          cpu: 0.4
          memoryInGb: 0.7
  - name: $name$-05
    properties:
      image: goenning/webpage-timing
      environmentVariables:
        - name: 'ORIGIN'
          value: '$location$'
        - name: 'REQUEST_URL'
          value: '$request_url$'
        - name: 'MONGO_URL'
          secureValue: '$mongo_url$'
      resources:
        requests:
          cpu: 0.4
          memoryInGb: 0.7
  osType: Linux
  restartPolicy: OnFailure 
```

Enter fullscreen mode Exit fullscreen mode

该模板将被加载到 Azure 容器服务中，它将使用相同的图像和参数创建 5 个容器。它还被配置为使用高达 0.4 的 CPU 和仅 700MB 的内存。

创建另一个文件`aci.sh`，内容如下:

```
# The name of the resource group to be used on Azure
resource_group="webtiming-rg"

# The list of locations from where the test will be executed
locations=( "westus" "eastus" "westeurope" "westus2" "northeurope" "southeastasia" "eastus2" "centralus" "australiaeast" "uksouth" "southcentralus" "centralindia" "southindia" "northcentralus" "eastasia" "canadacentral" "japaneast" )

# The URL of the webpage we want to test
request_url="https://github.com/docker"

# The connection string to a MongoDB instance
mongo_url="mongodb+srv://webtiming:webtiming@cluster0-s5g8l.azure.mongodb.net/webtiming?retryWrites=true"

if [ $1 == "init" ]
then az group create --name $resource_group --location eastus
fi

if [ $1 == "run" ]
then rm -rf ./out
  mkdir ./out
  for loc in "${locations[@]}"
  do cat ./template.yaml | 
    sed 's|\$name\$|'$loc'-wt|' | 
    sed 's|\$request_url\$|'$request_url'|' | 
    sed 's|\$location\$|'$loc'|' | 
    sed 's|\$mongo_url\$|'$mongo_url'|' | 
    sed 's|\$location\$|'$loc'|' > "./out/$loc-wt.yaml"

    status=$(az container show -n $loc-wt --resource-group $resource_group --query containers[0].instanceView.currentState.state 2>/dev/null)
    if [ $? -eq 0 ]
    then az container start -g $resource_group --name "$loc-wt" &
      echo "$loc-wt has started..."
    else az container create -g $resource_group --location $loc --file "./out/$loc-wt.yaml" --no-wait
      echo "$loc-wt has been created..."
    fi
  done
fi

if [ $1 == "clean" ]
then az group delete --name $resource_group --yes
fi 
```

Enter fullscreen mode Exit fullscreen mode

第一行是参数，根据给定的注释进行配置并执行:

```
$ ./aci.sh init 
```

Enter fullscreen mode Exit fullscreen mode

第一步只是根据配置的名称创建一个资源组。如果您喜欢通过 Azure Portal 手动完成，可以跳过这一步。

```
$ ./aci.sh run 
```

Enter fullscreen mode Exit fullscreen mode

这是剧本最重要的部分。它基本上会遍历每个已配置的位置，并基于`template.yaml`创建一个 YAML 文件。

在执行结束时，您应该在 Azure 门户上看到类似这样的内容。

[![](img/0c4c52d1f52a6d71d1d1c1ccc5f4ff22.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5A1QDkI2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nuxlgkp14rcr0pohjys5.png)

请注意，有 17 个容器组(每个区域一个)，每个组有 5 个容器。其中一些已经完成处理，而另一些仍在进行中。这个过程可能需要额外的几秒钟，因为 Azure 需要首先从 Docker Hub Registry 获取图像。

几秒钟后，所有组都应处于“成功”状态。在此过程结束时，这些实例将保留在 Azure 上，直到您将其删除。您可以通过执行`./aci.sh clean`来做到这一点，这将删除资源组及其所有容器实例。

但是如果你计划定期执行这个，你可以保留 Azure 上的资源，然后简单地再次执行`./aci.sh run`。如果容器已经存在于 Azure 上，这个脚本足够智能来重启容器。您可以根据需要多次重复此过程。

## 结果

我们现在应该在 MongoDB 数据库上有一些文档，所以我们现在可以查看数据并执行一些分析。我们可以将任何 BI 工具连接到这个 MongoDB 实例，提取数据并绘制一些图表。

但是还有 [MongoDB 图表](https://www.mongodb.com/products/charts)，这是一个数据可视化工具，用于创建我们的 MongoDB 数据的可视化表示。在写这篇文章的时候，这项服务还在测试阶段，可以免费使用，所以我决定试一试。这是我从行刑中得到的。

[![](img/4db7507901731297a3bb4ea4cee8edaf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aixEo8ev--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fjhtfqpbiw8c3ratdx82.png)

正如我们所看到的，我们收集了 595 个计时，全球平均值为 4165 毫秒。图表显示亚洲人加载`https://github.com/docker`比美国人慢 2 秒。

您可以更进一步，实际分析`entries`数组，找出哪些 HTTP 资源需要更长的加载时间。

## 何去何从？

如果你喜欢这个，并想让它更上一层楼，这里有一些想法:

1.  计划此脚本每 X 小时执行一次
2.  缩小 Docker 图像尺寸。图像越小，在云上执行的速度就越快，这意味着越少💸
3.  分叉项目，用你需要的额外时间信息增强它
4.  使用木偶师来模拟一个较慢的网络和 CPU
5.  将脚本更改为多步骤流程。如果你有一个电子商务，你想衡量一个用户找到一个产品并购买它需要多长时间。您可以使用这个脚本作为起点，并包括这个过程的额外步骤
6.  尽情发挥，在每个地区部署数百个容器😁请记住，它还会增加你的💸

## 就这样！🎉

对此你怎么看？如果您有任何建议或反馈，请留下您的评论。