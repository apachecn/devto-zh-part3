# 使用扩展 Kubernetes 部署。网络核心

> 原文：<https://dev.to/cmendibl3/scale-a-kubernetes-deployment-with-net-core-2ihp>

让我们开始:

## 1。为新项目创建一个文件夹

* * *

打开命令行提示安润:

```
mkdir kuberenetes.scale 
```

Enter fullscreen mode Exit fullscreen mode

## 2。创建项目

* * *

```
cd kuberenetes.scale
dotnet new api 
```

Enter fullscreen mode Exit fullscreen mode

## 3。添加对 KubernetesClient 的引用

* * *

```
dotnet add package KubernetesClient -v 1.5.18
dotnet restore 
```

Enter fullscreen mode Exit fullscreen mode

## 4。使用以下代码创建一个 PodsController.cs

* * *

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using k8s;
using k8s.Models;
using Microsoft.AspNetCore.JsonPatch;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;

namespace kubernetes.scale
{
    [Route("api/[controller]")]
    [ApiController]
    public class PodsController : ControllerBase
    {
        private KubernetesClientConfiguration k8sConfig = null;

        public PodsController(IConfiguration config)
        {
            // Reading configuration to know if running inside a cluster or in local mode.
            var useKubeConfig = bool.Parse(config["UseKubeConfig"]);
            if (!useKubeConfig)
            {
                // Running inside a k8s cluser
                k8sConfig = KubernetesClientConfiguration.InClusterConfig();
            }
            else
            {
                // Running on dev machine
                k8sConfig = KubernetesClientConfiguration.BuildConfigFromConfigFile();
            }
        }

        [HttpPatch("scale")]
        public IActionResult Scale([FromBody]ReplicaRequest request)
        {
            // Use the config object to create a client.
            using (var client = new Kubernetes(k8sConfig))
            {
                // Create a json patch for the replicas
                var jsonPatch = new JsonPatchDocument<V1Scale>();
                // Set the new number of repplcias
                jsonPatch.Replace(e => e.Spec.Replicas, request.Replicas);
                // Creat the patch
                var patch = new V1Patch(jsonPatch);

                // Patch the "minions" Deployment in the "default" namespace
                client.PatchNamespacedDeploymentScale(patch, request.Deployment, request.Namespace);

                return NoContent();
            }
        }
    }

    public class ReplicaRequest
    {
        public string Deployment { get; set; }
        public string Namespace { get; set; }
        public int Replicas { get; set; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 5。替换 appsettings 的内容。开发. json 文件

注意 **UseKubeConfig** 属性被设置为**真**。

```
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  },
  "UseKubeConfig": true
} 
```

Enter fullscreen mode Exit fullscreen mode

## 6。替换 appsettings.json 文件的内容

注意 **UseKubeConfig** 属性被设置为**假**。

```
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*",
  "UseKubeConfig": false
} 
```

Enter fullscreen mode Exit fullscreen mode

## 7。测试应用程序

* * *

从您的开发机器上测试应用程序，运行:

```
dotnet run
curl -k -i -H 'Content-Type: application/json' -d '{"Deployment": "<YOUR DEPLOYMENT NAME>", "Namespace": "<YOUR NAMESPACE>", "Replicas": 3}' -X PATCH https://localhost:5001/api/pods/scale 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:您必须有一个有效的配置文件才能连接到 k8s 集群。

请在此找到本帖[中使用的所有代码。](https://github.com/cmendible/dotnetcore.samples/tree/master/kubernetes.scale)

希望有帮助！