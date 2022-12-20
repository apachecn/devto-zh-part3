# Kubernetes 性能和 CPU 管理器

> 原文：<https://dev.to/simplytunde/kubernetes-performance-and-cpu-manager-4ea9>

因此，您有一个对 CPU 敏感的工作负载，并且希望通过为您的工作流提供更好的 CPU 性能来优化工作，CPU 管理器可以提供帮助。现在，这对你有多大帮助？在我们谈论这个之前，让我们试着理解 CFS(完全公平调度)俚语；

### CFS 份额

不，这不像股票市场份额，我们在这里谈论的是 CPU。想想每个人都想分一杯羹的固定时间。CPU 份额简单地暗示了您可以使用多少系统 CPU 时间。

*   CPU 份额:这决定了在过载情况下分配给一个 CPU 内核的功率。假设两个进程(A 和 B)跳到一个 CPU 内核上，并且它们都被分配了 1024 个份额(默认分配，除非您进行更改)，这意味着它们在时间分配方面具有相同的权重，每个都获得 1/2 的 CPU 内核时间。现在，如果我们把事情变得有趣，让进程 B 份额更新为 512，就意味着 B 得到了(512/(1024+512)) = 1/3 的 CPU 时间。现在，还有一点需要记住，如果进程 A 空闲，进程 B 可以使用一些 CPU 时间，前提是我们在内核上只有 A 和 B。
*   CPU 周期:这是 CFS 带宽控制的一部分，它决定了周期对 CPU 的意义。什么是经期？可以把它想象成代表一个 CPU 周期时间，通常，对于大多数系统来说是 100ms(100，000)，它被表示为`cfs_period_us`。

*   CPU 配额:配额为 20 毫秒(20，000)的进程将在 100 毫秒的 CPU 周期内获得 1/5 的时间。所以配额基本上就是，你能访问多少时间片？你看这个变量表示为`cfs_quota_us`。

好了，行话说够了，kubernetes 怎么把一个有 100m(0.1 CPU)的容器翻译成份额和配额？下面可以看到答案。

这个 kubernetes go 代码为那些对 kubernetes 如何做这些事情感兴趣的人解释了一切。

```
// milliCPUToShares converts milliCPU to CPU shares
func milliCPUToShares(milliCPU int64) int64 {
    if milliCPU == 0 {
        // Return 2 here to really match kernel default for zero milliCPU.
        return minShares
    }
    // Conceptually (milliCPU / milliCPUToCPU) * sharesPerCPU, but factored to improve rounding.
    shares := (milliCPU * sharesPerCPU) / milliCPUToCPU
        // for example, share := (100m/1024) * 1000 = (100/1000) * 1024 = 102.4 shares
    if shares < minShares {
        return minShares
    }
    return shares
}

// milliCPUToQuota converts milliCPU to CFS quota and period values
func milliCPUToQuota(milliCPU int64) (quota int64, period int64) {
    // CFS quota is measured in two values:
    //  - cfs_period_us=100ms (the amount of time to measure usage across)
    //  - cfs_quota=20ms (the amount of cpu time allowed to be used across a period)
    // so in the above example, you are limited to 20% of a single CPU
    // for multi-cpu environments, you just scale equivalent amounts
    if milliCPU == 0 {
        return
    }

    // we set the period to 100ms by default
    period = quotaPeriod

    // we then convert your milliCPU to a value normalized over a period
    quota = (milliCPU * quotaPeriod) / milliCPUToCPU

    // quota needs to be a minimum of 1ms.
    if quota < minQuotaPeriod {
        quota = minQuotaPeriod
    }

    return 
```

##### CPU 管理器和调度

很高兴我们已经完成了所有这些条款。问题是，CPU 管理器如何帮助我处理 CPU 敏感的工作负载。所以基本上，它使用 linux 中的 cpuset 特性将容器放置在特定的 cpu 上。它占用 cpu 的一部分，等于容器中指定的请求(或限制)，将其分离并分配给你的容器，从而防止上下文切换和噪音邻居问题。让我们看看盖子下面；它创建了不同的内存池，如下所示

*   共享内存池:这是每个调度容器被分配到的内存池，直到决定将它们移动到其他地方。
*   保留池:还记得你的 kubelet 可以保留 cpu 吧？是的，就是这些家伙。简单来说，就是你在共享池里不能碰的 cpu。
*   可分配的:这是满足独占性要求的容器获取 CPU 的地方。它们从为 kubelet 移除保留池后剩余的 CPU 中获取。一旦它们被分配到容器，它们就会从共享池中删除。
*   独家分配；该池包含分配给容器的 cpuset。

下一个问题，谁有资格获得可分配的池？任何具有整数 CPU 的保证容器(请求=限制)。对，整数！如下图所示的容器；

```
apiVersion: v1
kind: Pod
metadata:
  name: memory-demo
  namespace: mem-example
spec:
  containers:
  - name: memory-demo-ctr
    image: polinux/stress
    resources:
      limits:
        cpu: 1
        memory: "200Mi" 
      requests:
        cpu: 1
        memory: "200Mi"
    command: ["stress"] 
```

好的，我提到每个人首先被分配到共享池。是什么让他们进入专属泳池？kubelet 通过每隔一段时间检查共享池中的容器，并将那些合格的容器移动到独占池中，来完成我们所说的重新同步(可配置的 kubelet 选项)。这意味着，在下一次重新同步之前，您的 pod 可能在共享池中。另外，请注意，kubelet 或系统进程可能会在专用 CPU 集上运行，因为管理器只保证 pod 的专用性。系统中的其他进程，那不是 kubelet 的事情。

### 把钱拿出来

理论够了，让我们弄脏吧。我们如何在我们的 kubelet 上启用这个特性？只需启用特性门 CPUManager 并传入静态策略。我在我的测试 kubeadm 设置中使用了这个

```
kind: KubeletConfiguration
featureGates:
  CPUManager: true
cpuManagerPolicy: static
systemReserved:
  cpu: 500m
  memory: 256M
kubeReserved:
  cpu: 500m
  memory: 256M 
```

例如，让我们继续创建这个 pod

```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox
    resources:
      requests:
        memory: "24Mi"
        cpu: "150m"
      limits:
        memory: "28Mi"
        cpu: "160m"
    command: ['sh', '-c', 'echo  Hello  Kubernetes!  &&  sleep  3600'] 
```

在启用了 CPU 管理器功能门的集群上创建 pod 后，它将被调度到节点上。此时，它是一个具有 153(150/1000 * 1024)个 CPU 份额和 16000 个 CPU 配额(160/1000 * 100000)的可突发 pod。您可以通过查看容器组来确认这一点。

```
cat /sys/fs/cgroup/cpu,cpuacct/kubepods/burstable/podf19e6b4b-6eb0-11e9-898e-062ad3dc4fe4/138208e13ba
73882fc0a5c06862b7b0bc7f6d3f43116d61ecf2488fae11d6004/cpu.shares 
153
cat /sys/fs/cgroup/cpu,cpuacct/kubepods/burstable/podf19e6b4b-6eb0-11e9-898e-062ad3dc4fe4/138208e13ba
73882fc0a5c06862b7b0bc7f6d3f43116d61ecf2488fae11d6004/cpu.cfs_quota_us 
16000 
```

上面的 pod 是一个可突发的 pod，但是为了测试 CPU 管理器，我们需要一个有保证的 pod，它有**个整数**个 CPU。一旦 pod 被调度，kubelet 应该配置我们的容器运行时，使用 cpuset 在特定的内核上运行 pod。

```
apiVersion: v1
kind: Pod
metadata:
  name: guaranteed-myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox
    resources:
      requests:
        memory: "38Mi"
        cpu: "1"
      limits:
        memory: "38Mi"
        cpu: "1"
    command: ['sh', '-c', 'echo  Hello  Kubernetes!  &&  sleep  3600'] 
```

我们可以在集装箱码头检查和集装箱组中看到这一点。

```
#  docker inspect 59964c06d765 | grep -i cpu
            "CpuShares": 1024,
            "NanoCpus": 0,
            "CpuPeriod": 100000,
            "CpuQuota": 100000,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "1",
            "CpusetMems": "",
            "CpuCount": 0,
            "CpuPercent": 0,
# cat /sys/fs/cgroup/cpuset/kubepods/pod273e9d00-706c-11e9-a529-062ad3dc4fe4/59964c06d7657face0585c9db37
5d8773dcb1b351a2d7e87204e89a2e47c2b97/cpuset.effective_cpus
1 
```

如果你看看其他可突发的容器，它们将被限制为共享核心。

```
# docker inspect 6ca5ea998f0d | grep -i cpu
            "CpuShares": 153,
            "NanoCpus": 0,
            "CpuPeriod": 100000,
            "CpuQuota": 16000,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "0,2-3",
            "CpusetMems": "",
            "CpuCount": 0,
            "CpuPercent": 0,
# cat /sys/fs/cgroup/cpuset/kubepods/burstable/podd024aac6-706b-11e9-a529-062ad3dc4fe4/539b9b1c4ec3e49fa
49b84b22ff4a06058a1c0bd6db57667cb30786927d3a380/cpuset.cpus
0,2-3 
```

### 有用链接

*   [https://stalled-Goodall-e282f 7 . net lify . com/contributors/design-proposals/node/CPU-manager/](https://stupefied-goodall-e282f7.netlify.com/contributors/design-proposals/node/cpu-manager/)
*   [https://goldmann . pl/blog/2014/09/11/resource-management-in-docker/# _ CPU](https://goldmann.pl/blog/2014/09/11/resource-management-in-docker/#_cpu)
*   [https://software . Intel . com/en-us/blogs/2018/08/07/CPU-manager-for-performance-sensitive-applications](https://software.intel.com/en-us/blogs/2018/08/07/cpu-manager-for-performance-sensitive-applications)
*   [https://medium . com/@ betz . mark/understanding-resource-limits-in-kubernetes-CPU-time-9 eff 74d 3161 b](https://medium.com/@betz.mark/understanding-resource-limits-in-kubernetes-cpu-time-9eff74d3161b)
*   [https://medium . com/@ mcastelino/kubernetes-resource-management-deep-dive-b 337 ba 15359 c](https://medium.com/@mcastelino/kubernetes-resource-management-deep-dive-b337ba15359c)