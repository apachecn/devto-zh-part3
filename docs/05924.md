# 立方结构中的资料类型:PriorityQueue

> 原文：<https://dev.to/chuck_ha/data-types-in-kubernetes-priorityqueue-38d2>

忽必烈版本:`v1.13.2`

Kubernetes 调度器( [kube-scheduler](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-scheduler/) )是 Kubernetes 的一个组件，负责将 pod 分配给节点。这被称为调度，因此得名 kube-scheduler。kube-scheduler 有一个特性叫做 [pod 优先级和抢占](https://kubernetes.io/docs/concepts/configuration/pod-priority-preemption/)。

Pod 优先级和抢占允许在 pod 规范中为 pod 分配一个优先级值。在 pod 调度期间，Kubernetes 将考虑优先级。具有较高优先级的单元将被安排在较低优先级单元之前。此外，在低资源情况下，较低优先级的 pod 可以被驱逐，以支持较高优先级的 pod。

首先，让我们看看 kube 调度程序是如何工作的。kube-scheduler [可以访问需要调度的 pod](https://github.com/kubernetes/kubernetes/blob/v1.13.2/pkg/scheduler/scheduler.go#L509)队列。每当创建或修改一个 pod 时，该 pod 都会被添加到 kube-scheduler 的 pod 队列中。kube-scheduler [等待 pod 出现在 pod 队列](https://github.com/kubernetes/kubernetes/blob/v1.13.2/pkg/scheduler/factory/factory.go#L113-L117)中，让 pod 出队并对其进行调度。

kube-scheduler 的 pod 队列可以按照 pod 加入队列的顺序或者根据 pod 的优先级返回 pod。在先进先出的情况下，如果创建了 5 个 pod 并需要进行调度，kube-scheduler 可以调度前 4 个，然后耗尽最后一个 pod 的资源。如果最后一个单元是计划中最重要的单元，那么你就不走运了。您必须删除一些窗格，直到您有足够的资源允许调度程序对其进行调度。

如果 kube-scheduler 的 pod 队列是基于优先级的，那么您可以为 pod 分配优先级。如果同样的 5 个单元被调度，但是每个单元具有不同的优先级，则最高优先级的单元将首先被调度，或者，如果它在其他 4 个单元已经被调度之后到来，并且没有更多的资源，则 kube-scheduler 将尝试移动较低优先级的单元，直到一个节点上有足够的资源来调度最高优先级的单元。

kube-scheduler 能够交换出 pod 队列的实现，因为它被抽象在一个[接口](https://github.com/kubernetes/kubernetes/blob/v1.13.2/pkg/scheduler/internal/queue/scheduling_queue.go#L54)之后。如果 pod 优先级被禁用，那么 kube-scheduler 将使用普通的先进先出队列作为数据结构来满足调度队列接口。然而，在默认情况下，启用了 pod 优先级，kube-scheduler 将使用优先级队列来实现调度队列。

优先级队列与常规队列具有相同的接口:

```
type  Queue  interface  {  // Put an item on the queue  Enqueue(interface{})  // Remove an item from the queue  Dequeue()  interface{}  } 
```

区别完全在于底层实现。

在 Go 中，常见的是使用片或通道作为底层数据结构来实现[先进先出队列。](https://github.com/kubernetes/kubernetes/blob/v1.13.2/staging/src/k8s.io/client-go/tools/cache/fifo.go#L98)

如果队列是优先级队列而不是先进先出队列，那么堆可以用作数据结构来实现创建优先级队列的队列。

Go 有一个内置的堆接口定义在 [`container`](https://golang.org/pkg/container/heap/) 包中。这允许您实现堆接口的一些方法，并让 Go 库作者高效地实现常见的堆操作。

我们可以在 kube-scheduler 的代码中看到两个队列实现:

*   先进先出队列
*   [优先级队列](https://github.com/kubernetes/kubernetes/blob/v1.13.2/pkg/scheduler/internal/queue/scheduling_queue.go#L195)

从由外向内的角度概括一下:

1.  kube-scheduler 依赖于一个调度队列来获取下一个要调度的 pod。
2.  SchedulingQueue 可以通过多种方式实现，但默认情况下使用优先级队列(启用了 pod 优先级)。
3.  优先级队列使用堆来保持它所知道的基于优先级的 pod 顺序。这意味着当 kube-scheduler 得到下一个要调度的 pod 时，具有最高优先级的 pod 首先被返回。

如果你有这种愿望，请给我反馈！我一直在寻求提高我的技术写作技巧。