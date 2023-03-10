# Oracle Kubernetes 引擎中带有对象存储的集中式日志记录

> 原文：<https://dev.to/ankitbansal/centralized-logging-in-oracle-kubernetes-engine-with-object-storage-1ael>

在 kubernetes 中有多种方法可以捕获日志。最简单的机制之一是拥有 pod 级脚本，可以将日志上传到目标系统。当您尝试 kubernetes 时，这种方法是可行的，但是一旦您决定在生产中使用 kubernetes 并部署多个应用程序，您就会发现需要一个独立于 pod 生命周期的集中式日志记录机制。

我们需要为我们的 kubernetes 集群设置日志。集群构建在 [Oracle Kubernetes 引擎(OKE)](https://docs.cloud.oracle.com/iaas/Content/ContEng/Concepts/contengoverview.htm) 上，我们希望将日志保存在 [OCI 对象存储](https://docs.cloud.oracle.com/iaas/Content/Object/Concepts/objectstorageoverview.htm)中。第一步是找到捕获日志的有效方法。考虑到多个选项，我们发现 Daemonset 是一个很好的选项，原因如下:

*   Kubernetes 确保每个节点都运行 Pod 的一个副本。在集群中添加任何新节点时，kubernetes 会自动确保在该节点上创建一个 pod。这可以进一步定制为仅根据选择标准选择节点。
*   它避免了更改单个应用程序的部署。如果以后想要更改日志记录机制，只需更改 Daemonset
*   由于日志捕获是在 pod 之外运行的，因此对应用程序的性能没有影响。

完成后，下一步是配置 Daemonset 从 OKE 捕获日志，并将它们发布到 OCI 对象存储。我们以前使用过 fluentd，我们决定继续使用它。Fluentd 已经有了用于配置 daemonset 并上传到 s3 的[映像](https://github.com/fluent/fluentd-kubernetes-daemonset/blob/master/docker-image/v1.3/debian-s3/Dockerfile)。因为对象存储与 S3 API 兼容，所以我们能够通过对 fluent.conf 进行一些定制来使用它

**设置集群角色**

Fluentd daemonset 要求在 kube 系统中运行。第一步是创建一个新帐户，并为其提供所需的权限。

创建服务账户:

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: fluentd
  namespace: kube-system 
```

Enter fullscreen mode Exit fullscreen mode

> ku bectl create-f service account . YAML

创建集群角色:

```
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: fluentd
  namespace: kube-system
rules:
- apiGroups:
  - ""
  resources:
  - pods
  - namespaces
  verbs:
  - get
  - list
  - watch 
```

Enter fullscreen mode Exit fullscreen mode

> ku bectl create-f cluster role . YAML

为具有帐户的群集角色创建绑定:

```
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: fluentd
roleRef:
  kind: ClusterRole
  name: fluentd
  apiGroup: rbac.authorization.k8s.io
subjects:
- kind: ServiceAccount
  name: fluentd
  namespace: kube-system 
```

Enter fullscreen mode Exit fullscreen mode

> 多维数据集创建-f 群集 rolebinding.yaml

**创建 Daemonset**

接下来，我们创建配置映射来提供定制的流畅配置:

```
--------
apiVersion: v1
kind: ConfigMap
metadata:
  name: fluent-config
  namespace: kube-system
data:
  fluent.conf: |
    @include "#{ENV['FLUENTD_SYSTEMD_CONF'] || '../systemd'}.conf"
    @include "#{ENV['FLUENTD_PROMETHEUS_CONF'] || '../prometheus'}.conf"
    @include ../kubernetes.conf
    @include conf.d/*.conf

    <match **>
      @type s3
      @id out_s3
      @log_level info
      s3_bucket "#{ENV['S3_BUCKET_NAME']}"
      s3_endpoint "#{ENV['S3_ENDPOINT']}"
      s3_region "#{ENV['S3_BUCKET_REGION']}"
      s3_object_key_format %{path}%Y/%m/%d/cluster-log-%{index}.%{file_extension}
      <inject>
        time_key time
        tag_key tag
        localtime false
      </inject>
      <buffer>
        @type file
        path /var/log/fluentd-buffers/s3.buffer
        timekey 3600
        timekey_use_utc true
        chunk_limit_size 256m
      </buffer>
    </match> 
```

Enter fullscreen mode Exit fullscreen mode

> 立方结构 create -f configmap.yaml

现在，我们可以继续创建 daemonset:

```
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: fluentd
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
    version: v1
    kubernetes.io/cluster-service: "true"
spec:
  template:
    metadata:
      labels:
        k8s-app: fluentd-logging
        version: v1
        kubernetes.io/cluster-service: "true"
    spec:
      serviceAccount: fluentd
      serviceAccountName: fluentd
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd
        image: fluent/fluentd-kubernetes-daemonset:v1.3.3-debian-s3-1.3
        env:
          - name: AWS_ACCESS_KEY_ID
            value: "#{OCI_ACCESS_KEY}"
          - name: AWS_SECRET_ACCESS_KEY
            value: "#{OCI_ACCESS_SECRET}"
          - name: S3_BUCKET_NAME
            value: "#{BUCKET_NAME}"
          - name: S3_BUCKET_REGION
            value: "#{OCI_REGION}"
          - name: S3_ENDPOINT
            value: "#{OBJECT_STORAGE_END_POINT}"
          - name: FLUENT_UID
            value: "0"
          - name: FLUENTD_CONF
            value: "override/fluent.conf"
          - name: FLUENTD_SYSTEMD_CONF
            value: "disable"
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log/
        - name: u01data
          mountPath: /u01/data/docker/containers/
          readOnly: true
        - name: fluentconfig
          mountPath: /fluentd/etc/override/
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log/
      - name: u01data
        hostPath:
          path: /u01/data/docker/containers/
      - name: fluentconfig
        configMap:
          name: fluent-config 
```

Enter fullscreen mode Exit fullscreen mode

有几点需要注意:

*   我们通过配置映射提供一个自定义的 fluent.conf，并将其安装在 daemonset 中。这是提供显式 s3_endpoint 所必需的，因为默认情况下，映像无法提供自定义 s3_endpoint
*   下面是我们需要配置的 env 变量。S3 地区是 oci 地区，例如 us-ashburn-1。S3 端点是对象存储端点，例如 https://# { tenant name } . compat . object storage . us-ash burn-1 . Oracle cloud . com . AWS _ ACCESS _ KEY _ ID 和 AWS_SECRET_ACCESS_KEY 是您的用户的客户密钥。如果不存在，参考[文件](https://docs.cloud.oracle.com/iaas/Content/Object/Tasks/s3compatibleapi.htm)生成。S3 存储桶名称是用于存储日志的对象存储桶。

让我们继续创建 daemonset:

> kubectl create -f daemonset.yaml

配置完成后，您应该能够看到对象存储中的日志。如果 bucket 不存在，它将创建它。