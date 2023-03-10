# 像变魔术一样调整 Kubernetes 中持久卷的大小

> 原文：<https://dev.to/bzon/resizing-persistent-volumes-in-kubernetes-like-magic-4f96>

## 立方盘扩展

因此，您的工作负载的持久卷几乎已满。怎么能扩大呢？有了 [Kubernetes 1.11 和更高版本](https://kubernetes.io/blog/2018/07/12/resizing-persistent-volumes-using-kubernetes/)，现在只需更新持久卷声明存储规范就可以轻松做到这一点。

我在这篇文章中的例子是在谷歌的 Kubernetes 引擎中测试的。

我们开始吧！

### 启用存储类的卷扩展

要启用磁盘大小调整功能，请确保您的工作负载的持久卷声明所使用的存储类配置有`allowVolumeExpansion: true`。

```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: standard
parameters:
  type: pd-standard
provisioner: kubernetes.io/gce-pd
allowVolumeExpansion: true # ensure that this is true 
```

### 状态集工作负载的补充说明

如果您想使用如下配置的`volumeClaimTemplates`为 StatefulSet 调整磁盘大小，

```
 volumeClaimTemplates:
  - metadata:
      name: postgresql-storage
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 1Gi
      storageClassName: standard 
```

您只需修改它已提供的永久卷声明。

### 调整持久卷声明的大小

找到要调整大小的永久卷声明。

```
kubectl get pvc -l app=postgresql 
```

假设您想将 PostgreSQL-storage-PostgreSQL-02-1 从 **1Gi** 更新为 **10Gi**

```
NAME                                 STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
postgresql-storage-postgresql-01-0   Bound    pvc-613c74c3-66b5-11e9-a602-42010a8400c0   10Gi       RWO            standard       6m12s
postgresql-storage-postgresql-01-1   Bound    pvc-88f897b1-60d4-11e9-a602-42010a8400c0   5Gi        RWO            standard       7d11h
postgresql-storage-postgresql-02-0   Bound    pvc-727c167c-6089-11e9-a602-42010a8400c0   10Gi       RWO            standard       7d20h
postgresql-storage-postgresql-02-1   Bound    pvc-80421dab-6089-11e9-a602-42010a8400c0   1Gi        RWO            standard       7d20h 
```

```
kubectl edit pvc postgresql-storage-postgresql-02-1 
```

在你的文本编辑器中，将 **1Gi** 改为**10Gi**T4】

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  annotations:
    pv.kubernetes.io/bind-completed: "yes"
    pv.kubernetes.io/bound-by-controller: "yes"
    volume.beta.kubernetes.io/storage-provisioner: kubernetes.io/gce-pd
  creationTimestamp: "2019-04-16T20:52:09Z"
  finalizers:
  - kubernetes.io/pvc-protection
  labels:
    app: postgresql
  name: postgresql-storage-postgresql-02-1
  namespace: default
  resourceVersion: "29892621"
  selfLink: /api/v1/namespaces/default/persistentvolumeclaims/postgresql-storage-postgresql-02-1
  uid: 80421dab-6089-11e9-a602-42010a8400c0
spec:
  accessModes:
  - ReadWriteOnce
  dataSource: null
  resources:
    requests:
      storage: 1Gi # change this to 10Gi
  storageClassName: standard
  volumeName: pvc-80421dab-6089-11e9-a602-42010a8400c0
status:
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 1Gi # change this to 10Gi
  phase: Bound 
```

成功修改永久卷声明后，观察其状态。

```
kubectl get pvc postgresql-storage-postgresql-02-1 -o yaml 
```

这应该是示例输出。观察 YAML 最末端的状态。

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  annotations:
    pv.kubernetes.io/bind-completed: "yes"
    pv.kubernetes.io/bound-by-controller: "yes"
    volume.beta.kubernetes.io/storage-provisioner: kubernetes.io/gce-pd
  creationTimestamp: "2019-04-16T20:52:09Z"
  finalizers:
  - kubernetes.io/pvc-protection
  labels:
    app: postgresql
  name: postgresql-storage-postgresql-02-1
  namespace: default
  resourceVersion: "33540657"
  selfLink: /api/v1/namespaces/default/persistentvolumeclaims/postgresql-storage-postgresql-02-1
  uid: 80421dab-6089-11e9-a602-42010a8400c0
spec:
  accessModes:
  - ReadWriteOnce
  dataSource: null
  resources:
    requests:
      storage: 10Gi
  storageClassName: standard
  volumeName: pvc-80421dab-6089-11e9-a602-42010a8400c0
status:
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 1Gi
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2019-04-24T17:30:51Z"
    message: Waiting for user to (re-)start a pod to finish file system resize of
      volume on node.
    status: "True"
    type: FileSystemResizePending
  phase: Bound 
```

### 重启吊舱

下一步是按照 Kubernetes 的建议重启 Pod。

```
kubectl delete pod postgresql-02-1 
```

然后等待，直到吊舱再次开始运行。

### 观察变化

在成功重启 Pod 后，再次检查永久卷声明的状态，您应该看到存储已经更新。

```
kubectl get pvc postgresql-storage-postgresql-02-1 -o yaml 
```

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  annotations:
    pv.kubernetes.io/bind-completed: "yes"
    pv.kubernetes.io/bound-by-controller: "yes"
    volume.beta.kubernetes.io/storage-provisioner: kubernetes.io/gce-pd
  creationTimestamp: "2019-04-16T20:52:09Z"
  finalizers:
  - kubernetes.io/pvc-protection
  labels:
    app: postgresql
  name: postgresql-storage-postgresql-02-1
  namespace: default
  resourceVersion: "33541444"
  selfLink: /api/v1/namespaces/default/persistentvolumeclaims/postgresql-storage-postgresql-02-1
  uid: 80421dab-6089-11e9-a602-42010a8400c0
spec:
  accessModes:
  - ReadWriteOnce
  dataSource: null
  resources:
    requests:
      storage: 10Gi
  storageClassName: standard
  volumeName: pvc-80421dab-6089-11e9-a602-42010a8400c0
status:
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 10Gi
  phase: Bound 
```

对 Pod 执行`kubectl exec`操作，并验证磁盘已调整大小！

运行`df -h`。

```
# df -h
Filesystem      Size  Used Avail Use% Mounted on
overlay         5.7G  4.7G  1.1G  83% /
tmpfs           1.9G     0  1.9G   0% /dev
tmpfs           1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/sda1       5.7G  4.7G  1.1G  83% /etc/hosts
shm              64M     0   64M   0% /dev/shm
/dev/sdc        9.9G  1.3G  8.6G  13% /var/lib/postgresql
tmpfs           1.9G   12K  1.9G   1% /run/secrets/kubernetes.io/serviceaccount
tmpfs           1.9G     0  1.9G   0% /sys/firmware 
```

神奇！