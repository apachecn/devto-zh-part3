# 使用 Python Azure 函数和 SignalR 构建无服务器物联网解决方案

> 原文：<https://dev.to/azure/building-a-serverless-iot-solution-with-python-azure-functions-and-signalr-4ljp>

关注我 [Twitter](https://twitter.com/dglover) 、[项目源代码](https://github.com/gloveboxes/Go-Serverless-with-Python-Azure-Functions-and-SignalR)、 [Powerpoint 幻灯片](https://github.com/gloveboxes/Go-Serverless-with-Python-Azure-Functions-and-SignalR/blob/master/docs/Python%20Serverless%20with%20Azure%20Functions.pptx)、 [PDF 幻灯片](https://github.com/gloveboxes/Go-Serverless-with-Python-Azure-Functions-and-SignalR/blob/master/docs/Python%20Serverless%20with%20Azure%20Functions.pdf)

## 解决方案概述

[![solution overview](img/f5432a4a0ba5073c218539a8fd341b2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ArEcQdSf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Go-Serverless-with-Python-Azure-Functions-and-SignalR/master/docs/resources/solution-architecture.png)

该解决方案图概述了一个典型的物联网解决方案。 [Azure 物联网中心](https://docs.microsoft.com/en-us/azure/iot-hub?WT.mc_id=devto-blog-dglover)负责互联网规模、安全、与设备和后端服务的双向通信。

Azure IoT Hub 可以将遥测数据[路由](https://docs.microsoft.com/en-us/azure/iot-hub/tutorial-routing?WT.mc_id=devto-blog-dglover)到各种服务，也可以以 [Apache Avro](https://avro.apache.org/docs/current/) 或 JSON 格式存储，用于审计、集成或驱动机器学习过程等目的。

这篇文章截取了这一场景的一部分，讲述了通过 Python Azure 函数和 Azure SignalR 对来自 Azure IoT Hub 的遥测数据进行直接的[无服务器](https://en.wikipedia.org/wiki/Serverless_computing)处理，以实现近乎实时的仪表板。

### Azure 服务

该解决方案中使用了以下 Azure 服务，这些服务在免费层中可用: [Azure 物联网中心](https://docs.microsoft.com/en-us/azure/iot-hub?WT.mc_id=devto-blog-dglover)、 [Azure 功能](https://docs.microsoft.com/en-us/azure/azure-functions?WT.mc_id=devto-blog-dglover)、 [Azure 信号员](https://docs.microsoft.com/en-us/azure/azure-signalr?WT.mc_id=devto-blog-dglover)、 [Azure 存储](https://docs.microsoft.com/en-us/azure/storage?WT.mc_id=devto-blog-dglover)、 [Azure 存储静态网站](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-static-website?WT.mc_id=devto-blog-dglover)

你可以注册一个[免费 Azure 账户](https://azure.microsoft.com/en-au/free?WT.mc_id=devto-blog-dglover)，如果你是学生，那么一定要注册[学生 Azure](https://azure.microsoft.com/en-au/free/students?WT.mc_id=devto-blog-dglover)，不需要信用卡。

## 开发 Python Azure 函数

## 从哪里开始

查看 Azure Functions Python 工人指南。有关于以下主题的信息:

*   [创建你的第一个 Python 函数](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-function-python?WT.mc_id=devto-blog-dglover)
*   [开发者指南](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-python?WT.mc_id=devto-blog-dglover)
*   [绑定 API 引用](https://docs.microsoft.com/en-us/python/api/azure-functions/azure.functions?view=azure-python&WT.mc_id=devto-blog-dglover)
*   [使用 VS 代码开发](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-function-vs-code?WT.mc_id=devto-blog-dglover)
*   [使用自定义 docker 映像在 Linux 上创建一个 Python 函数](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-function-linux-custom-image?WT.mc_id=devto-blog-dglover)

## 解决方案组件(包含在本 GitHub repo 中)

1.  Python Azure 函数。这个 Azure 函数处理成批的遥测数据，然后校准和验证遥测数据，并更新设备状态 Azure 存储表，然后将遥测数据传递给 Azure SignalR 服务，以进行近乎实时的 web 客户端更新。

2.  天蓝色信号机。NET Core Azure 函数(用 C#编写，直到 Python SignalR 绑定可用)。这个 Azure 函数负责将遥测数据传递给 SignalR 服务，以发送到 SignalR Web 客户端仪表板。

3.  [网络仪表盘](https://enviro.z8.web.core.windows.net/enviromon.html)。这款单页 Web 应用作为静态网站托管在 Azure Storage 上。所以它也是无服务器的。

## 设计注意事项

### 乐观并发

首先，理解[事件中枢触发缩放](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-event-iot#trigger---scaling?WT.mc_id=devto-blog-dglover)以及如何启动额外的功能实例来处理事件是很有用的。

我想在设备状态表中维护一个计数，记录设备发送遥测数据的次数。该解决方案实现了 [Azure Storage/CosmosDB 开放式并发](https://azure.microsoft.com/en-us/blog/managing-concurrency-in-microsoft-azure-storage-2?WT.mc_id=devto-blog-dglover)。

[乐观并发(OCC)](https://en.wikipedia.org/wiki/Optimistic_concurrency_control) 假设多个事务可以频繁完成而不会互相干扰。运行时，事务使用数据资源，但不获取这些资源的锁。在提交之前，每个事务都会验证没有其他事务修改过它所读取的数据。OCC 通常用于数据争用较少的环境中。

如果有多个函数实例更新并且有冲突，我已经实现了指数回退并添加了一个随机因子来允许重试。

```
Pseudo code: random(occBase, min(occCap, occBase * 2 ^ attempt)) 
```

```
def calcExponentialFallback(attempt):
    base = occBase * pow(2, attempt)
    return random.randint(occBase, min(occCap, base)) / 1000.0 
```

从我有限的测试来看，指数后退是有效的。

## 遥测处理

“updateDeviceState”首先检查实体是否已经在存储表中。如果实体存在，则调用 merge_entity 时会使用“etag”。如果在合并时 etag 与存储中实体的 etag 相匹配，则 merge_entity 调用成功。

```
def updateDeviceState(telemetry):
    mergeRetry = 0

    while mergeRetry < 10:
        mergeRetry += 1

        try:
            # get existing telemetry entity
            entity = table_service.get_entity(
                deviceStateTable, partitionKey, telemetry.get('deviceId', telemetry.get('DeviceId')))
            etag = entity.get('etag')
            count = entity.get('Count', 0)
        except:
            entity = {}
            etag = None
            count = 0

        count += 1

        updateEntity(telemetry, entity, count)
        calibrator.calibrateTelemetry(entity)

        if not validateTelemetry(entity):
            break

        try:
            if etag is not None:    # if etag found then record existed
                # try a merge - it will fail if etag doesn't match
                table_service.merge_entity(
                    deviceStateTable, entity, if_match=etag)
            else:
                table_service.insert_entity(deviceStateTable, entity)

            return entity

        except:
            interval = calcExponentialFallback(mergeRetry)
            logging.info("Optimistic Consistency Backoff interval {0}".format(interval))
            time.sleep(interval)

    else:
        logging.info('Failed to commit update for device {0}'.format(
            entity.get('DeviceId'))) 
```

### 遥测校准优化

您可以在设备上或云中校准数据。我更喜欢校准云端。校准数据可以用 Azure 函数数据绑定加载，但我更喜欢延迟加载校准数据。可能会有大量校准数据，因此在触发该功能时一次性加载所有数据是没有意义的。

```
def getCalibrationData(deviceId):
    if deviceId not in calibrationDictionary:
        try:
            calibrationDictionary[deviceId] = table_service.get_entity(
                calibrationTable, partitionKey, deviceId)
        except:
            calibrationDictionary[deviceId] = None

    return calibrationDictionary[deviceId] 
```

### 遥测验证

物联网解决方案应验证遥测技术，以确保数据在合理的范围内，以允许故障传感器。

```
def validateTelemetry(telemetry):
    temperature = telemetry.get('Celsius')
    pressure = telemetry.get('hPa')
    humidity = telemetry.get('Humidity')

    if temperature is not None and not -40 <= temperature <= 80:
        return False
    if pressure is not None and not 600 <= pressure <= 1600:
        return False
    if humidity is not None and not 0 <= humidity <= 100:
        return False
    return True 
```

## Azure SignalR 集成

没有服务端 Azure SignalR SDK。要从 Event Hub Trigger Azure 函数向 Dashboard Web 客户端发送遥测数据，您需要调用一个绑定到 SignalR 服务的 HTTP Azure 函数。然后，这个 SignalR Azure 函数通过 SignalR 发送遥测数据，就好像数据来自客户端应用程序一样。

Azure SignalR 集成的流程如下:

1.  Web 客户端对' **negotiate** '进行 REST 调用，其中，将信号' **Hubname** '返回给客户端。
2.  Web 客户端然后对' **getdevicestate** '进行 REST 调用，这个 HTTP 触发器从设备状态表中检索所有设备的状态。数据通过 SignalR 经由从对' **negotiate** 的调用返回的相同' **Hubname** 返回给客户端。
3.  当新的遥测数据通过物联网集线器到达时，触发“**环境事件触发器**”触发器，遥测数据在设备状态表中更新，并对“**发送信号消息**”进行 REST 调用，遥测数据被发送到所有在“**集线器名称**”信道上监听的信号客户端。

[![](img/04c1449ea1b2e65599f1793ce9094b99.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1sCJccYb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Go-Serverless-with-Python-Azure-Functions-and-SignalR/master/docs/resources/service-side-signalr.png)

## 设置概述

这个实验室使用 Azure 上的免费服务。需要设置以下内容:

1.  Azure 物联网中心和 Azure 物联网设备
2.  蓝色信号服务
3.  部署 Python Azure 函数
4.  展开信号器。NET Core Azure 函数

### 第一步:按照 Raspberry Pi 模拟器指南设置 Azure 物联网中心

虽然 Python Azure 中的函数是预览版，但它们只在有限的地方可用。现在是“西特斯”和“西欧洲”。我建议您在其中一个位置创建所有项目资源。

[设置 Raspberry Pi 模拟器](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started?WT.mc_id=devto-blog-dglover)

[![raspberry Pi Simulator](img/eb2c127481db398f6f4669b57649c6b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QOkeHfbG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://docs.microsoft.com/en-us/azure/iot-hub/media/iot-hub-raspberry-pi-web-simulator/3_banner.png%3FWT.mc_id%3Ddevto-blog-dglover)

### 第二步:创建 Azure 资源组

[阿兹集团创建](https://docs.microsoft.com/en-us/cli/azure/group?view=azure-cli-latest#az-group-create&WT.mc_id=devto-blog-dglover)

```
az group create -l westus -n enviromon-python 
```

### 第三步:创建 Azure 信号服务

*   创建 az signalr 服务
*   [az signalr 键列表](https://docs.microsoft.com/en-us/cli/azure/signalr/key?view=azure-cli-latest#az-signalr-key-list&WT.mc_id=devto-blog-dglover)返回您需要的 signalr 的连接字符串。NET Core Azure 函数。

```
az signalr create -n <Your SignalR Name> -g enviromon-python --sku Free_DS2 --unit-count 1
az signalr key list -n <Your SignalR Name> -g enviromon-python 
```

### 第四步:创建存储账户

[az 存储账户创建](https://docs.microsoft.com/en-us/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create&WT.mc_id=devto-blog-dglover)

```
az storage account create -n enviromonstorage -g enviromon-python -l westus --sku Standard_LRS --kind StorageV2 
```

### 第五步:克隆项目

```
git clone https://github.com/gloveboxes/Go-Serverless-with-Python-Azure-Functions-and-SignalR.git 
```

### 第六步:部署信号员。NET Core Azure 函数

```
cd Go-Serverless-with-Python-Azure-Functions-and-SignalR

cd dotnet-signalr-functions

cp local.settings.sample.json local.settings.json

code . 
```

### 第七步:更新 local.settings.json

```
{  "IsEncrypted":  false,  "Values":  {  "AzureWebJobsStorage":  "<The Storage Connection String for enviromonstorage>",  "FUNCTIONS_WORKER_RUNTIME":  "dotnet",  "StorageConnectionString":"<The Storage Connection String for enviromonstorage>",  "AzureSignalRConnectionString":  "<The SignalR Coonection String from Step 3>"  },  "Host":  {  "LocalHttpPort":  7071,  "CORS":  "http://127.0.0.1:5500,http://localhost:5500,https://azure-samples.github.io",  "CORSCredentials":  true  }  } 
```

### 第八步:部署信号员。NET Core Azure 函数

1.  在 Visual Studio 中打开一个终端窗口。从主菜单中，选择视图->终端
2.  部署 Azure 功能

```
func azure functionapp publish --publish-local-settings <Your SignalR Function Name>
func azure functionapp list-functions <Your SignalR Function Name> 
```

```
Functions in mysignal-signalr:
    getdevicestate - [httpTrigger]
        Invoke url: https://mysignal-signalr.azurewebsites.net/api/getdevicestate

    negotiate - [httpTrigger]
        Invoke url: https://mysignal-signalr.azurewebsites.net/api/negotiate

    SendSignalrMessage - [httpTrigger]
        Invoke url: https://mysignal-signalr.azurewebsites.net/api/sendsignalrmessage?code=DpfBdeb9TV1FCXXXXXXXXXXXXX9Mo8P8FPGLia7LbAtZ5VMArieo20Q== 
```

**您需要将 SendSignalrMessage 调用 url 复制并粘贴到方便的地方。

### 第九步:用 Visual Studio 代码打开 Python 函数项目

切换到您克隆到项目的目录，切换到 iothub-python-functions 目录，然后启动 Visual Studio 代码。

从 Linux 和 macOS 上的终端，或 Windows 上的 Powershell。

```
 cd Go-Serverless-with-Python-Azure-Functions-and-SignalR

cd iothub-python-functions

cp local.settings.sample.json local.settings.json

code . 
```

### 第十步:更新 local.settings.json

```
{  "IsEncrypted":  false,  "Values":  {  "FUNCTIONS_WORKER_RUNTIME":  "python",  "AzureWebJobsStorage":  "<The Storage Connection String for enviromonstorage>",  "IoTHubConnectionString":  "<The IoT Hub Connection String>",  "PartitionKey":  "<Storage Partition Key - arbitrary - for example the name of city/town>",  "StorageConnectionString":  "<The Storage Connection String for enviromonstorage>",  "SignalrUrl":  "<SendSignalrMessage Invoke URL>"  }  } 
```

### 步骤 11:部署 Python Azure 功能

1.  在 Visual Studio 中打开一个终端窗口。从主菜单中，选择视图->终端
2.  部署 Azure 功能

```
func azure functionapp publish enviromon-python --publish-local-settings --build-native-deps 
```

### 步骤 12:为 Azure 存储启用静态网站

仪表板项目包含静态网站项目。

遵循 Azure 存储中的[静态网站托管指南。](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-static-website?WT.mc_id=devto-blog-dglover)

用于此示例的页面是 enviromon.html。确保在网页 javascript 中修改“apiBaseUrl”URL，以指向您的 SignalR Azure 函数实例。

将仪表板项目的内容复制到静态网站。

### 第十三步:使能信号员的 CORS。NET Core Azure 函数

[az functionapp cors add](https://docs.microsoft.com/en-us/cli/azure/functionapp/cors?view=azure-cli-latest#az-functionapp-cors-add&WT.mc_id=devto-blog-dglover)

```
az functionapp cors add -g enviromon-python -n <Your SignalR Function Name> --allowed-origins <https://my-static-website-url> 
```

### 第十四步:启动仪表盘

从您的网络浏览器中，导航到[https://your-start-web-site/enviro mon . html](https://your-start-web-site/enviromon.html)

来自 Raspberry Pi 模拟器的遥测数据将显示在仪表板上。

[![dashboard](img/4f69350fb17061daadb2c80d486412f2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vg9eeqjs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Go-Serverless-with-Python-Azure-Functions-and-SignalR/master/docs/resources/dashboard.png)