# Kubernetes、PodSecurityPolicy 和 Kubeadm

> 原文：<https://dev.to/simplytunde/kubernetes-podsecuritypolicy-and-kubeadm-26i3>

我假设您对 PodSecurityPolicy 有一点了解，现在是时候设置启用 PodSecurityPolicy 准入控制器的集群了。对于我们的设置，这里是 kubeadm 配置；

```
kind: InitConfiguration
apiVersion: kubeadm.k8s.io/v1beta1
kind: InitConfiguration
---
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
featureGates:
  AppArmor: true
cpuManagerPolicy: static
systemReserved:
  cpu: 500m
  memory: 256M
kubeReserved:
  cpu: 500m
  memory: 256M
---
apiVersion: kubeadm.k8s.io/v1beta1
kind: ClusterConfiguration
apiServer:
  extraArgs:
    enable-admission-plugins:  PodSecurityPolicy,LimitRanger,ResourceQuota,AlwaysPullImages,DefaultStorageClass 
```

让我们开始集群，

```
$ sudo kubeadm init --config kubeadm.json 
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
#join a worker node to the master
$ sudo kubeadm join 10.100.11.231:6443 --token 6yel1a.ce3le6eel3kfnxsz --discovery-token-ca-cert-hash sha256:99ee2e4ea
302c5270f2047c7a0093533b69105a8c91bf20f48b230dce9fd3f3a
$ kubectl get no
NAME               STATUS     ROLES    AGE    VERSION
ip-10-100-11-199   NotReady   <none>   109s   v1.13.1
ip-10-100-11-231   NotReady   master   3m3s   v1.13.1 
```

正如你所看到的，我们的集群还没有准备好，因为我们需要安装一个网络插件，如果你描述一下这个节点，你就会看到这个插件。

```
$ kubectl describe no ip-10-100-11-231
...
Conditions:
  Type             Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----             ------  -----------------                 ------------------                ------                       -------
  MemoryPressure   False   Fri, 31 May 2019 22:50:36 +0000   Fri, 31 May 2019 22:46:39 +0000   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure     False   Fri, 31 May 2019 22:50:36 +0000   Fri, 31 May 2019 22:46:39 +0000   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure      False   Fri, 31 May 2019 22:50:36 +0000   Fri, 31 May 2019 22:46:39 +0000   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready            False   Fri, 31 May 2019 22:50:36 +0000   Fri, 31 May 2019 22:46:39 +0000   KubeletNotReady              runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:docker: network plugin is not ready: cni config uninitialized
Addresses:
... 
```

我们将使用 calico 作为我们的网络插件，你可以按照安装说明正确[这里](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/)

```
$ kubectl apply -f https://docs.projectcalico.org/v3.7/manifests/calico.yaml
configmap/calico-config created
customresourcedefinition.apiextensions.k8s.io/felixconfigurations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamblocks.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/blockaffinities.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamhandles.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamconfigs.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/bgppeers.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/bgpconfigurations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ippools.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/hostendpoints.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/clusterinformations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/globalnetworkpolicies.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/globalnetworksets.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/networkpolicies.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/networksets.crd.projectcalico.org created
clusterrole.rbac.authorization.k8s.io/calico-kube-controllers created
clusterrolebinding.rbac.authorization.k8s.io/calico-kube-controllers created
clusterrole.rbac.authorization.k8s.io/calico-node created
clusterrolebinding.rbac.authorization.k8s.io/calico-node created
daemonset.extensions/calico-node created
serviceaccount/calico-node created
deployment.extensions/calico-kube-controllers created
serviceaccount/calico-kube-controllers created 
```

令我们惊讶的是，kube 系统中没有像我们预期的那样运行 pod。让我们看看印花布节点 daemonset

```
$ kubectl describe daemonset calico-node  -n kube-system
Name:           calico-node
Selector:       k8s-app=calico-node
Node-Selector:  beta.kubernetes.io/os=linux
Labels:         k8s-app=calico-node
...
Events:
  Type     Reason        Age                  From                  Message
  ----     ------        ----                 ----                  -------
  Warning  FailedCreate  20s (x15 over 102s)  daemonset-controller  Error creating: pods "calico-node-" is forbidden: no providers available to validate pod request 
```

好的，它说没有提供者，这意味着您没有定义任何 PSP 来验证 daemonset pods。这是一个非常令人困惑的错误消息。现在，我们将在下面应用这个 PSP。

```
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: calico-psp
  annotations:
    seccomp.security.alpha.kubernetes.io/allowedProfileNames: '*'
spec:
  privileged: false
  allowPrivilegeEscalation: true
  allowedCapabilities:
  - '*'
  volumes:
  - '*'
  hostNetwork: true
  hostPorts:
  - min: 0
    max: 65535
  hostIPC: true
  hostPID: true
  runAsUser:
    rule: 'RunAsAny'
  seLinux:
    rule: 'RunAsAny'
  supplementalGroups:
    rule: 'RunAsAny'
  fsGroup:
    rule: 'RunAsAny' 
```

```
$ kubectl apply -f calico-psp.yaml 
podsecuritypolicy.policy/calico-psp created
$ kubectl get psp
NAME             PRIV    CAPS   SELINUX    RUNASUSER   FSGROUP    SUPGROUP   READONLYROOTFS   VOLUMES
calico-psp   false RunAsAny   RunAsAny    RunAsAny   RunAsAny   false            * 
```

好了，现在一切都好了吧？好吧，如果你描述 daemonset，你还是会看到和上面一样的信息。您需要删除并重新应用 calico 插件清单。在这之后，让我们继续描述我们的 daemonset。

```
$ kubectl describe daemonset calico-node  -n kube-system
Name:           calico-node
Selector:       k8s-app=calico-node
Node-Selector:  beta.kubernetes.io/os=linux
Labels:         k8s-app=calico-node
...
    Type:          HostPath (bare host directory volume)
    Path:          /var/lib/cni/networks
    HostPathType:  
Events:
  Type     Reason        Age                From                  Message
  ----     ------        ----               ----                  -------
  Warning  FailedCreate  4s (x12 over 14s)  daemonset-controller  Error creating: pods "calico-node-" is forbidden: unable to validate against any pod security policy: [] 
```

这个错误意味着我们有一个 psp，但是我们不能用它来验证我们的 daemonset。这是因为我们的 daemonset pod 容器无法使用我们的 PSP，我们需要修改 daemonset clusterrole 以便能够使用此特定 PSP 并添加此规则。是的，你需要重建。

```
# Include a clusterrole for the calico-node DaemonSet,
# and bind it to the calico-node serviceaccount.
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: calico-node
rules:
  - apiGroups: ["extensions"]
    resources:
       - podsecuritypolicies
    resourceNames:
       - calico-psp
    verbs:
       - use
  # The CNI plugin needs to get pods, nodes, and namespaces.
  - apiGroups: [""]
    resources:
      - pods
      - nodes
      - namespaces
    verbs:
      - get
---
... 
```

在修改了 clusterrole 之后，我们将遇到另一个错误；

```
...
   host-local-net-dir:
    Type:          HostPath (bare host directory volume)
    Path:          /var/lib/cni/networks
    HostPathType:  
Events:
  Type     Reason        Age               From                  Message
  ----     ------        ----              ----                  -------
  Warning  FailedCreate  3s (x11 over 9s)  daemonset-controller  Error creating: pods "calico-node-" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
... 
```

我们的 calico pods 被禁止，因为它使用的 PSP 禁止特权容器，而 calico 需要一个来处理网络策略。现在，让我们继续在我们的 PSP 中通过更新来解决这个问题；

```
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: calico-psp
  annotations:
    seccomp.security.alpha.kubernetes.io/allowedProfileNames: '*'
spec:
  privileged: true #update this to true
  allowPrivilegeEscalation: true
... 
```

一旦应用了更新，calico 将创建 pod，我们的节点将变得健康。

```
...
Events:
  Type     Reason            Age                     From                  Message
  ----     ------            ----                    ----                  -------
  Warning  FailedCreate      3m10s (x16 over 5m54s)  daemonset-controller  Error creating: pods "calico-node-" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
  Normal   SuccessfulCreate  26s                     daemonset-controller  Created pod: calico-node-hwb2b
  Normal   SuccessfulCreate  26s                     daemonset-controller  Created pod: calico-node-gtrm2

$ kubectl get no
NAME               STATUS   ROLES    AGE     VERSION
ip-10-100-11-199   Ready    <none>   7m2s    v1.13.1
ip-10-100-11-231   Ready    master   7m33s   v1.13.1 
```

耶！！！我们的节点准备好了。