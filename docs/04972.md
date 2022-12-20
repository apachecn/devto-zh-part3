# ARM 上的 Kubernetes:案例研究

> 原文：<https://dev.to/nmiculinic/kubernetes-on-arm-a-case-study-5ha4>

在 KrakenSystems，我们正在与各种物联网设备合作。它们是我们收集数据并将其发送到进一步聚合管道的主要基础设施。目前，它们被实现为 Beaglebone black 设备、armv7l 硬浮点 CPU、AM335x 1GHz ARM Cortex-A8 和仅 512MB RAM。在这篇博文中，我们将介绍在这种功能不足的设备上使用 kubernetes 的用例及基本原理。

这些设备执行简单的服务。读取 Modbus 寄存器或 xbee 协议，或连接到 OBD(车辆的车载诊断)，解析数据，以 protobuf 格式序列化并在消息总线上发送。

## 设计标准&实施

### 部署格式

我们希望将软件部署为不可变的二进制/容器。由于在 KrakenSystems 的 C++构建过程的神秘设置，以及过多的共享库依赖关系，容器设置对这个用例来说是最有意义的。静态二进制也是一个可行的替代方案，但这需要重构 C++我们当前的构建系统编写为 bash/Makefile 脚本集合，从 0 开始运行大约 15 分钟，缓存后在 CI 上运行大约几分钟。

另一个解决方案是部署服务裸机。在这种遗留设置中，每个服务都有一个专用的共享库文件夹，我们在共享库版本管理上耍了 `LD_LIBRARY_PATH`花招，从一开始就击败了共享库。然而，由于构建系统当前状态，生成静态二进制文件非常耗时。

Kuberentes 及其容器管理解决方案非常适合我们的用例。Nomad 或 plain old docker/cri-o/rkt 也将满足这一设计标准。如果在当前的代码库状态下很容易实现的话，带有 systemd 的静态二进制文件也是一个令人满意的选择。

### 监控

节点和服务可用性监控至关重要。我们需要在节点上运行一些代理，并向一些系统发送我还活着，以及一个成熟的警报管道。咨询是一种解决办法。Kubernetes 有这个现成的，加上普罗米修斯警告规则似乎是一个自然的适合。我们还在整个基础设施中使用 Prometheus/grafana/alert manager，这使得该选项更具吸引力。

此外，活性和就绪性健康检查对于边缘设备不是特别有用，因为进程崩溃发出了问题的信号。它们不是需要接受客户端连接的服务器组件。

然而，在将来，我们计划在服务上引入活性检查，作为一种故障保护机制，以防服务不在消息总线上发送数据——这是它的主要目的。

剩下的 Ascalia 基础设施在 kubernetes 上，因此为我们的边缘设备重用这些相同的工具和设置是有意义的。尽管 kubernetes 操作起来并不简单，但运动部件的差异越小越好，并能简化操作。

### 更新

边缘设备不是永远静止在太平洋中的孤岛。代码经常改变，配置甚至更频繁。

这些服务旨在简化。他们的配置保存为`inotify`下的 YAML 文件，注意更改。因此，任何更新机制在未来都有可能成为 sidecar，但是要控制开发的复杂性。此外，它更容易调试。

每个边缘设备配置存储在 RDBMS 中，在本例中为 Postgres。让 100 个或 1000 个边缘设备轮询 RDBMS 寻找简单的键/值对不会有好结果。此外，在密钥更新时没有来自 RDBMS 的推送式通知。因此，我们需要一些额外的层。

我们正在重用 kubernetes API 服务器及其支持的键/值 etcd 存储。我们将每个边缘设备定义为 CRD(自定义资源定义)对象，支持丰富的特定于域的信息。kubernetes 还充当原始库存管理，补充支持操作的真实 Django(也就是说，我不关心 Django 做什么，只要更新 kubernetes API 中正确的 REST 端点)

在未来，边缘服务可能会监视支持键/值存储本身，无论是 kube api 服务器、etcd、consul、riak、redis 还是任何其他常见的键/值实现。

最后，我们需要异步更新。在应用更新时，设备可能处于离线状态。这排除了所有非基于代理的配置管理解决方案。Ansible 是我们最喜欢的配置管理工具，因为它简单而强大，只用于初始设置，不用于更新过程(即服务更新)。

### Wireguard VPN 设置

由于我们使用 wireguard VPN 解决方案，我们需要保持客户端服务器 IP/公钥列表异步同步。这需要在边缘设备上安装一个额外的代理，您必须监控、跟踪并确保它处于活动状态。

我们还需要存储离线设备的公钥，并方便检查这些密钥/设置。kubernetes CRDs 非常适合这个角色。我们重用 etcd 后备存储，在这些对象上有很好的 RBAC，并且我们定义了自定义打印机列，以便更轻松地管理 VPN 节点。

我们使用了以下开源内部工具:

*   [wg-cni](https://github.com/KrakenSystems/wg-cni)
*   [WG-操作员](https://github.com/KrakenSystems/wg-operator)
*   [wg-quick-go](https://github.com/nmiculinic/wg-quick-go)

长话短说，我们用 wg-cni 责任角色启动了 wireguard VPN。这也为我们的 kubernetes 集群安装了基于 wireguard 的 CNI。

wg-cni 角色创建了我们的自定义 CRD 清单，代表 wireguard VPN 拓扑中的客户端/服务器。

在应用清单之后，我们启动了 wireguard 操作符 daemonset，使节点与进一步的添加/删除保持同步。

### 初始部署

这不是没有问题。我们使用 [kubespray](https://github.com/kubernetes-sigs/kubespray) 作为成熟的 kubernetes 部署解决方案。这是裸机部署的唯一完整解决方案。由于是基于 ansible 的，我们对它很熟悉，如果需要的话可以很容易地扩展它....这是必要的。

我们遇到了无数的问题:

*   臂上缺少支撑
*   默认暂停图像不支持 arm
*   缺少 cpuset(内核更新到 4.19 LTS 解决了它)
*   遇到几次空间问题
*   法兰绒在 kubespray 中缺少多拱支持(在我们永久过渡到 wireguard CNI 之前)
*   ...

其中大部分在以下问题/PRs 中进行了跟踪:

问题:

*   [移植到 ARM (arm7l)时跟踪间隙](https://github.com/kubernetes-sigs/kubespray/issues/4294)
*   [Kubeadm 在 armv7l 上下载失败](https://github.com/kubernetes-sigs/kubespray/issues/4065)

PRs:

*   [增加对 hyperkube、kubeadm 和 cni_binary 的 arm 镜像支持](https://github.com/kubernetes-sigs/kubespray/pull/4261)
*   [法兰绒十字平台支架](https://github.com/kubernetes-sigs/kubespray/pull/4299)

在成功应用默认的容器运行时 docker 之后，是时候进行基本的性能分析了。

## 初始性能分析

### 基本检查表

*   eMMC 是没有时间的
*   使用 armhf 二进制文件(`readelf -A $(which kubelet | grep Tag_ABI_VFP_args`)
*   猫`/sys/devices/system/cpu/cpu0/cpufreq/scaling_governor`

### 使用

```
debian@bbb-test:~$ vmstat 1
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 4  0      0   6088  19916 271064    0    0    25    20   17   32 18 12 69  0  0
 0  0      0   6120  19916 271064    0    0     0     0 1334 4098 13 20 67  0  0
 0  0      0   6120  19916 271064    0    0     0     0 1554 4046 13 19 68  0  0
 0  0      0   6120  19924 271056    0    0     0    16  929 2443 10  8 81  1  0
 0  0      0   6120  19924 271064    0    0     0     0 1611 4128 24 20 56  0  0
 0  0      0   6120  19924 271064    0    0     0     0  919 2443  6 11 83  0  0
 0  0      0   5996  19924 271064    0    0     0     0 1240 3312 29 28 42  0  0
 0  0      0   5996  19924 271064    0    0     0     0  958 2417 13  9 77  0  0
 3  0      0   5996  19924 271064    0    0     0     0 1915 5693 28 25 46  0  0
 0  0      0   5996  19924 271064    0    0     0     0 1089 3296 12 18 70  0  0 
```

```
debian@bbb-test:~$ pidstat 30 1
Linux 4.19.9-ti-r5 (bbb-test)   02/25/2019      _armv7l_        (1 CPU)

04:59:26 PM   UID       PID    %usr %system  %guest    %CPU   CPU  Command

04:59:56 PM     0     26749    3.54    1.62    0.00    5.16     0  dockerd
04:59:56 PM     0     26754    0.44    0.37    0.00    0.81     0  docker-containe
04:59:56 PM     0     26784    0.00    0.07    0.00    0.07     0  kworker/u2:2-flush-179:0
04:59:56 PM     0     28814   10.08   10.79    0.00   20.88     0  kubelet
04:59:56 PM     0     29338    0.51    1.15    0.00    1.65     0  kube-proxy
04:59:56 PM   997     29734    1.42    0.37    0.00    1.79     0  consul
04:59:56 PM     0     30867    0.03    0.00    0.00    0.03     0  docker-containe
04:59:56 PM     0     30885    0.47    0.67    0.00    1.15     0  flanneld
04:59:56 PM  1000     31776    0.30    0.07    0.00    0.37     0  mosh-server 
```

大约 30%的 CPU 在 kubernetes 上，没有任何有意义的工作。

```
iostat -xz 1
sar -n DEV 1
sar -n TCP,ETCP 1 
```

不要表现出明显的网络压力。Speedtest-cli 显示了 30 兆位的下载/上传速度，这对于我们的用例来说绰绰有余。

总之，CPU 使用率高，而磁盘、内存和网络使用率低。

## 性能分析

Stracing kubelet 显示大约 66%的时间花在船闸上:

```
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 65.66    1.671983        5006       334        60 futex
 11.77    0.299775         967       310           epoll_wait
  9.24    0.235263         364       647           nanosleep
  2.58    0.065766          31      2136           clock_gettime
  1.75    0.044623          38      1180        68 read
...
------ ----------- ----------- --------- --------- ----------------
100.00    2.546516                 10290       356 total 
```

虽然使用 pprof 和跟踪配置文件显示了更多有用的信息:

```
debian@bbb-test:~$ wget http://127.0.0.1:10248/debug/pprof/profile?seconds=120
debian@bbb-test:~$ wget http://127.0.0.1:10248/debug/pprof/trace?seconds=120 
```

由此我们得出结论:

*   25%的时间花在家务上
*   从默认的 10 秒开始，更换内务管理间隔= 10 米
*   增加节点更新周期不会显著影响 CPU 的使用

这种内务处理主要是针对容器指标的，我们并不真的每 10 秒钟就需要它们，偶尔一次对我们的用例来说非常好。

```
GODEBUG=gctrace=1,schedtrace=1000
gc 80 @1195.750s 0%: 0.070+217+0.19 ms clock, 0.070+57/63/59+0.19 ms cpu, 24->24->12 MB, 25 MB goal, 1 P

SCHED 1196345ms: gomaxprocs=1 idleprocs=1 threads=18 spinningthreads=0 idlethreads=6 runqueue=0 [0] 
```

在 kubelet 进程中，go 的 GC 和调度器都没有大问题，因此没有进一步分析。
o

```
debian@bbb-test:~$ sudo perf stat -e task-clock,cycles,instructions,branches,branch-misses,instructions,cache-misses,cache-references
^C
 Performance counter stats for 'system wide':

         16,203.12 msec task-clock                #    1.000 CPUs utilized
     4,332,572,389      cycles                    # 267393.223 GHz                    (71.42%)
       911,023,486      instructions              #    0.21  insn per cycle           (71.40%)
        98,098,648      branches                  # 6054350.923 M/sec                 (71.41%)
        30,116,184      branch-misses             #   30.70% of all branches          (71.44%)
       885,259,275      instructions              #    0.20  insn per cycle           (71.45%)
         6,967,361      cache-misses              #    1.836 % of all cache refs      (57.16%)
       379,417,471      cache-references          # 23416495.155 M/sec                (57.14%)

      16.202385758 seconds time elapsed 
```

我们观察到库伯莱过程中 30+%的分支预测失误率。经过进一步分析，这是全系统的。这种廉价的 ARM 处理器具有可怕的分支预测算法。

### 改进

我们进行了以下改进:

*   偷走了 docker，用 CRI 插件取而代之。具体来说，我们使用了 containerd
*   将内务间隔从 10s 增加到 10m
*   扔掉法兰绒的 wireguard CNI(这是本地路由大部分)

```
Average:        0         9    0.00    0.14    0.00    0.24    0.14     -  ksoftirqd/0
Average:        0        10    0.00    0.17    0.00    0.35    0.17     -  rcu_preempt
Average:        0       530    0.00    0.03    0.00    0.00    0.03     -  jbd2/mmcblk1p1-
Average:        0       785    0.00    0.07    0.00    0.21    0.07     -  haveged
Average:        0       818    0.03    0.00    0.00    0.00    0.03     -  connmand
Average:        0       821    4.64    4.47    0.00    0.00    9.12     -  kubelet
Average:        0      1416    0.14    0.07    0.00    0.03    0.21     -  fail2ban-server
Average:        0      1760    0.42    0.69    0.00    0.35    1.11     -  kube-proxy
Average:        0      3436    1.70    0.90    0.00    0.00    2.60     -  containerd
Average:        0      4274    0.07    0.03    0.00    0.07    0.10     -  systemd-journal
Average:        0     17442    0.00    0.38    0.00    0.17    0.38     -  kworker/u2:2-events_unbound
Average:        0     19070    0.00    0.03    0.00    0.00    0.03     -  kworker/0:2H-kblockd
Average:        0     26772    0.00    0.24    0.00    0.28    0.24     -  kworker/0:1-wg-crypt-wg0
Average:        0     28212    0.00    0.31    0.00    0.28    0.31     -  kworker/0:3-events_power_efficient 
```

在稳定状态下，我们有大约 15%的 CPU 使用率用于监控。尽管适宜居住，但还是有不少。也许 cri-o 的开销会更低，尽管 containerd 的开销也很小。
我们将研究如何通过关闭不需要的功能来优化 kubelet，从而降低资源消耗。

## 总结

总而言之，在边缘设备上运行 kubernetes 是明智的选择吗？也许吧。

到目前为止，对我们来说一切都很好，尽管还可以生活，但一切都有相当大的开销。

例如，试图只安装 Prometheus node_exporter，会在每次刮擦时损坏您的 CPU，并在那几百毫秒内使一切变慢。

这种硬件动力不足，并且不良的分支预测使得在其上运行的任何软件都比在可比较的 armv8 或 x86_64 架构上运行的软件弱。

在未来，我们将尝试进一步优化，希望将 kubelet 的 CPU 开销降低到一个更合理的百分比。我们已经尝试了 rancher 的 k3s，没有太大的差异(实际上性能更差，因为我们不能改变内务间隔))

还有一个看起来对物联网上的 kubernetes 很有希望的 KubeEdge 项目。

## 参考文献

*   [https://www . cnx-software . com/2013/04/22/how-to-detect-if-an-arm-elf-binary-is-hard-float-armhf-or-soft-float-armel/](https://www.cnx-software.com/2013/04/22/how-to-detect-if-an-arm-elf-binary-is-hard-float-armhf-or-soft-float-armel/)
*   [https://kubrintes . io/blog/2018/05/24/kubrintes 集装箱一体化-goes-ga/](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
*   [https://github.com/rancher/k3s](https://github.com/rancher/k3s)
*   [https://kubrites . io/blog/2016/12/container runtime interface-CRI-in-kubrites/](https://kubernetes.io/blog/2016/12/container-runtime-interface-cri-in-kubernetes/)