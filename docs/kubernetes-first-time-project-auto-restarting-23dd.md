# Kubernetes 第一次项目-自动重启

> 原文：<https://dev.to/micahriggan/kubernetes-first-time-project-auto-restarting-23dd>

# Kubernetes 第一次项目-自动重启

今天我第一次给了 kubernetes 一针。我对它的自动重启特性很感兴趣，所以我写了一个简单的 node.js 程序

```
// application that randomly dies, and see how good kubernetes is at restarting it.
console.time('alive');
setTimeout(() => {
  console.timeEnd('alive');
  process.exit(0);
}, Math.random() * 60000);
setInterval(() => {
 console.log('feels good to be alive') 
}, 100) 
```

我下载了 minikube 和虚拟盒子，然后运行了

```
minikube start
minikube dashboard 
```

然后我做了一个超级基础的 Dockerfile

```
FROM node
ADD randomly-die.js .
CMD ["node", "randomly-die.js"] 
```

现在我们必须构建这个 Docker 映像，然后用 kubernetes
创建一个部署

```
eval $(minikube docker-env)
docker build -t randomly-die:1.0 .
kubectl run randomly-die --image randomly-die:1.0 --image-pull-policy=Never 
```

现在，我们可以检查控制面板的部署部分，看到我们有一个名为 random-die 的部署

[![minikube dashboard image](img/949f9bf3b784cdc70f37c91c05586abb.png "minikube dashboard")](https://res.cloudinary.com/practicaldev/image/fetch/s--yf9aRBoI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/micahriggan/blog/raw/master/k8-uptime/dashboard.png)

从这里我们可以看到，我们有一个副本集和一个单独的 pod，它正在运行我们的容器

现在，如果我们检查 pod 日志，我们可以看到我们已经启动并正在运行

```
...
feels good to be alive
feels good to be alive
feels good to be alive
feels good to be alive
feels good to be alive
feels good to be alive
feels good to be alive
feels good to be alive
feels good to be alive
feels good to be alive
alive: 4856.096ms 
```

# 缩放

Kubernetes 使得增加运行的容器数量变得非常容易。对于我的最终测试，我想看到它运行 10 个随机死亡的容器，并看着它重启它们

```
kubectl scale deployment --replicas=10 randomly-die
# deployment.extensions "randomly-die" scaled 
```

# 结论

现在我们有 10 个随机死亡的容器在运行，当 kubernetes 重新启动它们时，我们可以看到“重新启动”计数器递增。
[![minikube scale image](img/e3b54274a030ab28617c5064041e8080.png "kubernetes scaled up")T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--7oKko4Qn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/micahriggan/blog/raw/master/k8-uptime/scaled.png)

相当酷！