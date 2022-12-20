# 使用 Kubernetes 作业的 MongoDB 备份

> 原文：<https://dev.to/jmarhee/mongodb-backups-with-kubernetes-jobs-2n62>

我最近开始在 Kubernetes 上运行 RocketChat 这个部署的一个关键组件是 MongoDB 副本集。可靠运行 MongoDB 的最佳实践是复制**和**常规备份，Kubernetes 为这两种方法提供了可访问的接口。

在我的例子中，我想要定期的和一次性的备份能力，Kubernetes `Jobs`资源为我提供了一个快速的方法。我希望我的作业 pod 将这些转储文件写到一个持久的数据存储中，所以我首先设置了一个`PersistentVolume`并伴随着`Claim`到那个卷:

```
kind: PersistentVolume
apiVersion: v1
metadata:
  name: mongo-dump-pv-volume
  labels:
    type: local
    app: mongo-dump
spec:
  storageClassName: manual
  capacity:
    storage: 50Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: "/mnt/kube-data/mongo-dumps"
    type: DirectoryOrCreate
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: mongo-dump-pv-claim
  labels:
    app: postgres
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 50Gi 
```

如果你使用像 AWS 或 Azure 这样的云提供商，PersistentVolume 可以提供块存储，所以如果你的提供商确实做出了[持久性保证](https://aws.amazon.com/ebs/features/)，那么你的数据量就更加持久。上面的示例仅使用了`hostPath`卷，因此将在路径所在的主机的生命周期内持续存在。

您的工作本身将有一个 podspec，很像其他 Kubernetes 资源，如`Deployments`，您可以在这里请求资源，它看起来像这样:

```
apiVersion: batch/v1
kind: Job
metadata:
  name: mongodb-backup
  labels:
    app: mongo-dump
spec:
  backoffLimit: 5
  activeDeadlineSeconds: 100
  template:
    spec:
      containers:
      - name: mongodump
        image: mongo
        command: ["mongodump","--host","mongo-service:27017","--db","your_db"]
        volumeMounts:
          - mountPath: dump
            name: mongo-dumps
      volumes:
      - name: mongo-dumps
        persistentVolumeClaim:
          claimName: mongo-dump-pv-claim
      restartPolicy: OnFailure 
```

您将看到我们正在像平常一样连接卷，然后运行`mongodump`命令，该命令将写入挂载路径`dump`。

如果您在 Mongo 服务上启用了身份验证，或者使用像 MongoDB Atlas 这样的 SaaS，您可以像平常一样使用`Secrets`来传递安全存储的凭证，而不是在作业规范本身中:

```
apiVersion: batch/v1
kind: Job
metadata:
  name: mongodb-backup
  labels:
    app: mongo-dump
spec:
  backoffLimit: 5
  activeDeadlineSeconds: 100
  template:
    spec:
      containers:
      - name: mongodump
        image: mongo
        env:
          - name: MONGO_CONN_STRING
            valueFrom:
              secretKeyRef:
                name: mongo-auth
                key: connstring
          - name: MONGO_DB
            value: "my_db"
        command: ["mongodump","--host","$MONGO_CONN_STRING","--db","$MONGO_DB"]
        volumeMounts:
          - mountPath: dump
            name: mongo-dumps
      volumes:
      - name: mongo-dumps
        persistentVolumeClaim:
          claimName: mongo-dump-pv-claim
      restartPolicy: Never 
```

在您应用此工作规范后，您可以监控您的进度:

```
kubectl get pods -l app=mongo-dump 
```

然后监视该 pod 名称的日志:

```
kubectl logs $POD_NAME 
```

如果你发现你的工作失败了，你可以使用`restartPolicy`来定义这方面的行为；例如，在上面的声明中，它不会重新启动，但是您可以使用`OnFailure`来尝试重试，并使用其他可用选项来定义退休、回退时间等。

CronJobs 是 Kubernetes 目前支持的另一种类型的作业(从 1.7 开始)，在上面的链接中，你会看到规范是相似的，但是包括了你典型的 [Cron 语法](https://crontab.guru/)，用于定义你希望作业何时运行，以及相关的行为。