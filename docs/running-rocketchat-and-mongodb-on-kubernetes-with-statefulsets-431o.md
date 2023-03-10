# 运行中的火箭。Kubernetes 上的聊天和 MongoDB

> 原文：<https://dev.to/jmarhee/running-rocketchat-and-mongodb-on-kubernetes-with-statefulsets-431o>

Rocket Chat 可以在内部运行，有一组非常精简的要求；该应用程序只需要一个 MongoDB 数据库就可以启动并运行，如果您将它们的 Docker 映像用于该应用程序，这将非常快。我计划为我最近开始工作的一个社区项目运行一个非常活跃的实例，并决定 Kubernetes 将提供一种快速、灵活的方式来运行服务器，这被证明是对 StatefulSet 资源的一个很好的使用。

创建一个 MongoDB 副本集非常简单 StatefulSet 允许您使用一致的命名，这对于您的 Rocket Chat 服务器部署非常有用，稍后我将介绍这一点。

首先为 rocketchat-mongo-service 创建一个服务以及 StatefulSet:

```
---
kind: Service
apiVersion: v1
metadata:
  name: rocketchat-mongo-service
spec:
  selector:
    app: rocketchat-mongo
  ports:
  - protocol: TCP
    port: 27017
    targetPort: 27017

---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: rocketchat-mongo
spec:
  selector:
    matchLabels:
      app: rocketchat-mongo 
  serviceName: "rocketchat-mongo"
  replicas: 3
  template:
    metadata:
      labels:
        app: rocketchat-mongo 
    spec:
      terminationGracePeriodSeconds: 10
      containers:
      - name: mongodb
        image: mongo:3.2
        ports:
        - containerPort: 27017
          name: web
        command: ["mongod"]
        args: ["--replSet","rs0","--smallfiles","--oplogSize","128","--storageEngine=mmapv1"]
        volumeMounts:
        - name: mongo-persistent-storage
          mountPath: /data/db
  volumeClaimTemplates:
  - name: mongo-persistent-storage
    annotations:
      volume.beta.kubernetes.io/storage-class: "fast"
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 100Gi 
```

因此，这将为`rocketchat-mongo-service-{0,1,2}.default`创建一个副本集和 DNS 条目，对于您的部署，您将需要使用副本集的连接字符串:

```
mongodb://rocketchat-mongo-service-0.default,rocketchat-mongo-service-1.default,rocketchat-mongo-service-2.mongo:27017 
```

并在您的部署中使用它:

```
---
kind: Service
apiVersion: v1
metadata:
  name: rocketchat-server-service
spec:
  selector:
    app: rocketchat-server
  ports:
  - protocol: TCP
    port: 33000
    targetPort: 3000
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rocketchat-server-deployment
  labels:
    app: rocketchat-server
spec:
  replicas: 1
  selector:
    matchLabels:
      app: rocketchat-server
  template:
    metadata:
      labels:
        app: rocketchat-server
    spec:
      containers:
      - name: rocketchat-server
        image: rocketchat/rocket.chat:latest
        env:
          - name: PORT
            value: "3000"
          - name: ROOT_URL
            value: "[http://localhost:3000](http://localhost:3000)"
          - name: MONGO_URL
            value: "mongodb://rocketchat-mongo-service-0.default,rocketchat-mongo-service-1.default,rocketchat-mongo-service-2.mongo:27017/rocketchat" 
          - name: MONGO_OPLOG_URL
            value: "mongodb://rocketchat-mongo-service-0.default,rocketchat-mongo-service-1.default,rocketchat-mongo-service-2.mongo:27017/local"
        ports:
        - containerPort: 3000 
```

它使用 MONGO_URL 和 MONGO_OPLOG_URL 的副本集连接字符串。

请记住，部署将依赖于 MongoDB StatefulSet，因此请按以下顺序运行:

```
kubectl create -f rocketchat-mongodb.yaml 
```

一旦输出来自:

```
kubectl get pods -l app=rocketchat-mongo 
```

表示 3/3 的 pod 正在运行，继续应用 rocketchat-server 部署，一旦应用，您就可以通过[http://$ LB _ ADDRESS:$ SERVICE _ PORT](http://%24LB_ADDRESS:%24SERVICE_PORT)访问您的 Rocket Chat UI。

关于 Rocket Chat 的 Docker 映像的更多信息可以在基于 Docker Compose 的部署文档中找到:

[https://rocket . chat/docs/installation/docker-containers/docker-compose/](https://rocket.chat/docs/installation/docker-containers/docker-compose/)