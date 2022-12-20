# 在 Kubernetes 上运行 Atlassian Bitbucket 服务器

> 原文：<https://dev.to/jmarhee/running-atlassian-bitbucket-server-on-kubernetes-58ko>

Atlassian 为他们的 Bitbucket 服务器产品提供了一个 Docker 映像，这使得运行该产品变得异常简单，但是，我想创建一个持久的、可扩展的版本来在我的 Kubernetes 集群上运行。我使用服务器的标准部署资源实现了这一点，并使用 PersistentVolume 运行 Postgres 来存储服务器数据。

#### **创建位桶部署和服务**

我们将从为应用程序数据定义我们的 Bitbucket 部署的持久卷开始:

```
kind: PersistentVolume
apiVersion: v1
metadata:
  name: bitbucket-pv-volume
  labels:
    type: local
    app: bitbucket
spec:
  storageClassName: manual
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: "/mnt/kube-data/bitbucket"
    type: Directory
--------
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: bitbucket-pv-claim
  labels:
    app: bitbucket
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi 
```

Enter fullscreen mode Exit fullscreen mode

然后我们将在我们的部署中使用这个 PersistentVolumeClaim:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bitbucket-deployment
  labels:
    app: bitbucket
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bitbucket
  template:
    metadata:
      labels:
        app: bitbucket
    spec:
      containers:
      - name: bitbucket
        image: atlassian/bitbucket-server
        ports:
        - containerPort: 7990
          name: bitbucket-http
        - containerPort: 22
          name: bitbucket-ssh
        volumeMounts:
        - mountPath: /var/atlassian/application-data/bitbucket
          name: bitbucket-data
        env:
        - name: SERVER\_SECURE
          value: "true"
        - name: SERVER\_SCHEME
          value: "https"
        - name: SERVER\_PROXY\_PORT
          value: "443"
        - name: SERVER\_PROXY\_NAME
          value: ""
      volumes:
        - name: bitbucket-data
          persistentVolumeClaim:
            claimName: bitbucket-pv-claim 
```

Enter fullscreen mode Exit fullscreen mode

我们的部署使用 env 键来设置一些变量，以启用对此应用程序的 HTTPS 支持，因此如果您也希望这样做，可以在 SERVER_PROXY_NAME 中设置您的 FQDN。您还会注意到，我们正在使用我们在此部署中创建的 PersistentVolume，因此您将在后续重新创建时将此卷重新连接到此部署的 pod(而不是裸主机路径，在此实例中，裸主机路径的持久性稍差)。

最后要做的事情是创建一个服务，将服务器 UI 暴露给 web:

```
kind: Service
apiVersion: v1
metadata:
  name: bitbucket-service
spec:
  selector:
    app: bitbucket
  ports:
  - name: bitbucket-http
    port: 7990
    targetPort: bitbucket-http
  - name: bitbucket-ssh
    port: 2222
    targetPort: bitbucket-ssh
  type: LoadBalancer 
```

Enter fullscreen mode Exit fullscreen mode

一旦服务器安装完成，这将允许您通过 HTTP 或 SSH 进行推和拉。

**注意:**设置发生在 UI 中，不在本教程的讨论范围之内，所以在您完成本指南后，请坚持使用以下链接来完成设置:_

[https://confluence . atlassian . com/bitbucketserver/install-a-bit bucket-server-trial-867192384 . html](https://confluence.atlassian.com/bitbucketserver/install-a-bitbucket-server-trial-867192384.html)

#### 创建 Postgres 部署和服务

您需要做的第一件事是将您的 Postgres 证书存储在一个秘密中:

```
apiVersion: v1
kind: Secret
metadata:
  name: bitbucket-postgres
type: Opaque
data:
  username: <base64-encoded string>
  password: <base64-encoded string>
  postgres\_db: bitbucket 
```

Enter fullscreen mode Exit fullscreen mode

我更喜欢使用 SealedSecret 资源，这样我可以将所有这些资源签入版本控制，但是一个标准的秘密就足够了:

[bitnami-labs/sealed-secrets](https://github.com/bitnami-labs/sealed-secrets)

您还需要一个新的持久卷，我将再次使用相同的方法:

```
kind: PersistentVolume
apiVersion: v1
metadata:
  name: postgres-pv-volume
  labels:
    type: local
    app: postgres
spec:
  storageClassName: manual
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: "/mnt/kube-data/postgres"
    type: Directory
--------
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: postgres-pv-claim
  labels:
    app: postgres
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将创建部署，您将看到我们正在将解码后的机密数据导入到 Pod 的环境变量中:

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: postgres
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
        - name: postgres
          image: postgres:10.4
          imagePullPolicy: "IfNotPresent"
          ports:
            - containerPort: 5432
          env:
          - name: POSTGRES\_DB
            valueFrom:
              secretKeyRef:
                name: bitbucket-postgres
                key: dbname
          - name: POSTGRES\_ADMIN
            valueFrom:
              secretKeyRef:
                name: bitbucket-postgres
                key: username
          - name: POSTGRES\_PW
            valueFrom:
              secretKeyRef:
                name: bitbucket-postgres
                key: password
          volumeMounts:
            - mountPath: /var/lib/postgresql/data
              name: postgredb
      volumes:
        - name: postgredb
          persistentVolumeClaim:
            claimName: postgres-pv-claim 
```

Enter fullscreen mode Exit fullscreen mode

这将在单个实例中运行(为了具有一定的持久性，数据由一个卷支持)，我不建议在生产中使用，但对于您的 Bitbucket 服务器试用版(或低成本实例)，这应该足够了。您可以将其升级为 StatefulSet，以使 Postgres 基础设施更加健壮/可靠:

[https://kubrintes . io/blog/2017/02/PostgreSQL cluster-kubrintes-state fulsets/](https://kubernetes.io/blog/2017/02/postgresql-clusters-kubernetes-statefulsets/)

一旦所有这些都部署完毕，请记下您的 Postgres 证书。为了让 Bitbucket 服务器应用程序访问 Postgres 端点，最后一部分是服务定义:

```
apiVersion: v1
kind: Service
metadata:
  name: postgres-service
spec:
  selector:
    app: postgres
  ports:
  - protocol: TCP
    port: 5432
    targetPort: 5432 
```

Enter fullscreen mode Exit fullscreen mode

这将允许使用内部 DNS 上的主机名将端口 5432 连接到 postgres 部署箱:

```
postgres-service.default.svc.cluster.local 
```

Enter fullscreen mode Exit fullscreen mode

当您继续到 Bitbucket 服务器 UI 的负载平衡器地址以完成设置时，您将使用它。

#### 岗位设置

在 Docker 容器文档中，Atlassian 指出，为了避免版本之间向后兼容的问题，您应该将您的部署固定到一个特定的发布标签，而不是最新的。我使用 latest 部署以确保最新的版本，但是一旦我完成了，我就可以使用:
获取当前的 ID

```
kubectl describe pod -l app=bitbucket-server 
```

Enter fullscreen mode Exit fullscreen mode

在响应中，您会看到一个类似于
的字段

```
$ kubectl describe pod bitbucket-deployment-54f9cbff55-cmjrs
Name: bitbucket-deployment-54f9cbff55-cmjrs
Namespace: default
Priority: 0
PriorityClassName: <none>
Node: <node ID>/<node IP>
Start Time: Wed, 28 Nov 2018 18:02:50 +0000
Labels: app=bitbucket
                    pod-template-hash=54f9cbff55
Annotations: <none>
Status: Running
IP:
Controlled By: ReplicaSet/bitbucket-deployment-54f9cbff55
Containers:
  bitbucket:
    Container ID: docker://af14c60d72a8301c37be73a9864c6a96cc6171a4741b0504b620d003938717ff
    Image: atlassian/bitbucket-server
    Image ID: docker-pullable://atlassian/bitbucket-server@sha256:061d88d1116c3b99cb8dba592631cde947c7d1719cccdd08180dabb020da44f8 
```

Enter fullscreen mode Exit fullscreen mode

您需要获取 ImageID 字段值:

```
atlassian/bitbucket-server@sha256:061d88d1116c3b99cb8dba592631cde947c7d1719cccdd08180dabb020da44f8 
```

Enter fullscreen mode Exit fullscreen mode

并将其应用到您的部署中:

```
kubectl set image deployment/bitbucket-deployment bitbucket=atlassian/bitbucket-server@sha256:061d88d1116c3b99cb8dba592631cde947c7d1719cccdd08180dabb020da44f8 
```

Enter fullscreen mode Exit fullscreen mode

为了防止 pod 回收事件将最新的一次标记为不同的版本，并停止与您现有的安装一起工作。

一旦完成所有这些步骤，您就可以进入位于您的负载平衡器地址(或主机名，如果您启用了 HTTPS)的 UI，并完成安装:

[安装 Bitbucket 服务器试用版- Atlassian 文档](https://confluence.atlassian.com/bitbucketserver/install-a-bitbucket-server-trial-867192384.html)