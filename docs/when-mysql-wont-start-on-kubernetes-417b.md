# 当你的 mysql 图片拒绝在 Kubernetes 上启动时

> 原文：<https://dev.to/viniciusccarvalho/when-mysql-wont-start-on-kubernetes-417b>

k8s 中最常见的服务部署之一一直是一个简单的 mysql 服务。你可以在谷歌上找到上千个例子。

但是最近我开始用 [debezium](https://debezium.io) 做实验，它需要一个带有自定义`my.cnf`文件的 mysql 来启用完整的二进制日志。

首先，我在我的`minikube`上毫无问题地部署了这个服务:

```
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  ports:
  - port: 3306
  clusterIP: None
  selector:
    app: mysql
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
  labels:
    app: mysql
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
---
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: mysql
spec:
  selector:
    matchLabels:
      app: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - image: debezium/example-mysql:0.10      
        name: mysql
        env:
          # Use secret in real usage
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-pass
              key: password
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pvc 
```

但令我惊讶的是，它不会从 GKE 开始。相反，我会得到这个:

```
> [ERROR] --initialize specified but the data directory has files in it. Aborting. 
```

什么？Kubernetes 不就是为了让我们能够轻松地将工作负载移植到不同的提供商身上而不会出现问题吗？

经过大量的谷歌搜索，结果是 GKE 持久性卷提供者用一个不为空的 **lost+found** 目录挂载了`/var/lib/mysql`卷。这不会发生在迷你库贝。mysql 不喜欢这样(至少当你没有运行默认的 my.cnf 时是这样)。

因此，要解决这个问题，您需要将`--ignore-db-dir=lost+found`传递给容器的命令行参数，如下面的代码片段:

```
spec:
      containers:
      - image: debezium/example-mysql:0.10
        args:
          - "--ignore-db-dir=lost+found"
        name: mysql 
```

因为这花了我一段时间来寻找，我认为这将是值得分享给其他人可能会碰到这一点。

快乐编码