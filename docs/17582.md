# 在 Kubernetes 上使用 MySQL 8.0 的技巧

> 原文：<https://dev.to/yoshiyukikato/tips-to-use-mysql-80-on-kubernetes-m3l>

[MySQL 8.0](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/) 从 5.x 更新了许多功能。它们非常棒，但是，有些功能可能会给从 5.x 升级到 8.0 带来一些麻烦。默认认证插件的改变就是这样一个麻烦的特性。5.x 之前，默认插件是`mysql_native_password`。从 8.0 开始，默认插件是`caching_sha2_password`。这种差异可能会导致数据库登录失败，因为它需要一个 mysql 客户端来支持`caching_sha2_password`。具体来说，你可能会得到如下消息:

```
ERROR 2059 (HY000): Authentication plugin 'caching_sha2_password' cannot be loaded 
```

要解决这个错误，您必须在服务器启动之前设置一个参数来配置身份验证插件。您可以通过放置一个包含以下行的配置文件来设置该参数:

```
[mysqld]
default-authentication-plugin=mysql_native_password 
```

那么，如何为运行在 kubernetes 上的 **mysql 设置参数呢？这是这篇文章的主题。要将配置文件放入 kubernetes mysql 容器，可以使用`ConfigMap`。**

`ConfigMap`如下。如代码所示，可以将 mysql config 的内容直接描述到`ConfigMap`的 yaml 中。

```
# config-map.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  ## name of ConfigMap. This will be referred from volume definition
  name: mysql-config
  labels:
    app: mysql
data:
  ## default_auth is the name of config. This will be referred from volume mount definition
  default_auth: |
    [mysqld]
    default_authentication_plugin= mysql_native_password 
```

要将`default_auth`数据作为配置文件放入 k8s 容器，请在 mysql pod 配置中定义`volumes`和`volumeMounts`。

```
# pod.yaml
kind: Pod
apiVersion: v1
metadata:
  name: mysql
  labels:
    app: mysql
spec:
  containers:
    - name: mysql
      image: mysql:8.0
      imagePullPolicy: Always
      ports:
        - containerPort: 3306
          name: mysql
      env:
      - name: MYSQL_ROOT_PASSWORD # use only for debugging
        value: "root_password"
      - name: MYSQL_DATABASE
        value: "your_database"
      - name: MYSQL_USER
        value: "your_user"
      - name: MYSQL_PASSWORD
        value: "your_password"
      volumeMounts:
      - name: mysql-config-volume ## specify volume name
        mountPath: /etc/mysql/conf.d/default_auth.cnf ## path to mount file
        subPath: default_auth ## name of config
  volumes:
    - name: mysql-config-volume ## volume name
      configMap:
        name: mysql-config ## name of ConfigMap 
```

好吧！然后，让我们在 k8s 集群上创建这些资源，并检查是否正确应用了默认 auth 插件的配置。

```
$ kubectl apply -f config-map.yaml
$ kubectl apply -f pod.yaml 
```

检查是否有吊舱正在运行。

```
$ kubectl get pods

table
NAME      READY   STATUS    RESTARTS   AGE
mysql   1/1     Running   0          5s 
```

潜入分离舱。

```
$ kubectl exec -it mysql /bin/bash 
```

检查插件设置的值。

```
root@mysql:/# mysql -u root -D mysql -proot_password

mysql > SELECT user, plugin FROM user WHERE user="your_user"; 
```

你会得到以下表格格式的结果。

```
| your_user | mysql_native_password | 
```

看起来 good☺️

是时候检查它是否能够从 pod 外部连接 mysql 服务器了。为了便于连接，为 mysql 创建一个`Service`。这使得同一名称空间中的 pods 能够通过主机名`mysql`访问 mysql 服务器。

```
# server.yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  ports:
    - port: 3306
      name: mysql
  clusterIP: None
  selector:
    app: mysql 
```

```
$ kubectl apply -f service.yaml 
```

要连接服务器，请创建一个 mysql 客户端 pod。

```
# client-pod.yaml
kind: Pod
apiVersion: v1
metadata:
  name: mysql-client
  labels:
    name: mysql-client
spec:
  containers:
  - name: mysql-client
    image: ellerbrock/alpine-mysql-client
    command: ["sleep", "1000"] 
```

```
$ kubectl apply -f client-pod.yaml 
```

确认`mysql-client`舱是否运行，并潜入舱中。

```
$ kubectl exec -it mysql-client /bin/ash 
```

登录到 mysql 服务器。

```
~ $ mysql -h mysql -u your_user -D your_database -pyour_password

MySQL [your_database]> 
```

完整！🙌

本文中显示的所有源代码都发表在我的 Github 资源库中。如果你感兴趣，可以随意克隆并尝试一下！

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [吉行加藤](https://github.com/YoshiyukiKato)/[MySQL 8-k8s-举例](https://github.com/YoshiyukiKato/mysql8-k8s-example)

### 在 kubernetes 上使用 mysql8.0 的示例

<article class="markdown-body entry-content container-lg" itemprop="text">

# MySQL 8-k8s-示例

在 [my dev.to post](https://raw.githubusercontent.com/YoshiyukiKato/mysql8-k8s-example/master/) 中使用的示例代码。

## 使用

### 创建所有资源

```
./create
```

### 登录 mysql 服务器

```
$ kubectl exec -it mysql-client /bin/ash
$ mysql -h mysql -u your_user -D your_database -pyour_password

MySQL [your_database]> 
```

### 删除所有资源

```
$ ./delete
```

</article>

[View on GitHub](https://github.com/YoshiyukiKato/mysql8-k8s-example)