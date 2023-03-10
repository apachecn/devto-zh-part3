# Kubernetes InitContainers 和卷预填充入门

> 原文：<https://dev.to/jmarhee/getting-started-with-kubernetes-initcontainers-and-volume-pre-population-j83>

[Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)中的`InitContainer`资源是一个有趣且非常有用的资源；在许多情况下，您会看到它用于在`Pod`部署中创建容器之前在卷中预填充数据，因此在旋转容器时，卷数据已经初始化。

在我的例子中，我有一个简单的 web 前端，只有一个静态页面，它使用标准的`nginx` Docker 映像启动，提供一个清单文件:

```
FROM nginx

COPY index.html /usr/share/nginx/html/index.html
COPY smartos.ipxe /usr/share/nginx/html/smartos.ipxe 
```

这个映像构建和下载非常快，这很棒，但部分原因是它是无状态的；例如，`smartos.ipxe`文件引用发布数据，在启动应用程序时，这些数据需要可用，否则这些引用将无法按预期工作(抽象为`404` HTTP 响应):

```
#!ipxe
dhcp
set base-url http://sdc-ipxe.east.gourmet.yoga
kernel ${base-url}/smartos/smartos/platform/i86pc/kernel/amd64/unix -B smartos=true,console=ttyb,ttyb-mode="115200,8,n,1,-"
module ${base-url}/smartos/smartos/platform/i86pc/amd64/boot_archive type=rootfs name=ramdisk
boot 
```

然而，这些文件不是应用映像的一部分，因为，例如，它经常更新，所以每次我们推出新版本时，我们都希望在卷中提供最新版本，因为我们不需要在映像中维护这些文件，否则存储在我们的注册表中会非常大且成本高昂，我们可以在 Pod 中的容器上安装一个卷来提供它们。

因此，基本上，我们需要一种方法来预填充我们要挂载到`/usr/share/nginx/html/smartos`的卷。

使用`InitContainer`资源，我们可以指定要运行的命令，并且像`Pod`中的任何其他容器一样，我们可以分配要挂载的卷，所以让我们从 Kubernetes 清单开始，如下所示:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sdc-ipxe-deployment
  labels:
    app: sdc-ipxe
spec:
  replicas: 2
  selector:
    matchLabels:
      app: sdc-ipxe
  template:
    metadata:
      labels:
        app: sdc-ipxe
    spec:
      initContainers:
      - name: config-data
        image: ubuntu:xenial
        command: ["/bin/sh","-c"]
        args: ["apt update; apt install -y wget tar; wget https://us-east.manta.joyent.com/Joyent_Dev/public/SmartOS/platform-latest.tgz; tar xvf platform-latest.tgz -C /data; mkdir /data/smartos; mv /data/platform* /data/smartos/platform"]
        volumeMounts:
        - mountPath: /data
          name: sdc-data
      volumes:
      - name: sdc-data
        hostPath:
          path: /mnt/kube-data/sdc-ipxe/
          type: DirectoryOrCreate 
```

因此，在这一点上，我们正在配置卷`sdc-data`，在`/data`将它挂载到一个名为`config-data`的`initContainer`，并运行:

```
"apt update; apt install -y wget tar; wget https://us-east.manta.joyent.com/Joyent_Dev/public/SmartOS/platform-latest.tgz; tar xvf platform-latest.tgz -C /data; mkdir /data/smartos; mv /data/platform* /data/smartos/platform" 
```

将数据下载并提取到卷中。现在，我们向清单添加一个`containers:`键，并再次附加该卷，预填充的数据将可用:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sdc-ipxe-deployment
  labels:
    app: sdc-ipxe
...
      containers:
      - name: sdc-ipxe
        image: coolregistryusa.bix/jmarhee/sdc-ipxe:latest
        imagePullPolicy: Always
        ports:
        - containerPort: 80
        volumeMounts:
        - mountPath: /usr/share/nginx/html/smartos
          name: sdc-data
... 
```

将卷安装在容器中应用程序期望的位置，`/usr/share/nginx/html/smartos`使用相同的`sdc-data`卷。

如果您的应用程序依赖于具有可变需求的配置，这种模式也是有用的；也许你得到了一个令牌，或者一个地址是相当动态的，这需要通过磁盘上的文件传递，而不是通过环境(即负载平衡器、web 服务器或带有配置文件的数据库客户端，由于它们更改的频率很高，不容易通过 Secret 或 ConfigMap 处理)，这提供了一个有点容易编程的接口，用于预填充或完成传递给容器的数据的模板化。

## 进一步阅读

[使用 InitContainers 在 Kubernetes 中预先填充卷数据](https://medium.com/@jmarhee/using-initcontainers-to-pre-populate-volume-data-in-kubernetes-99f628cd4519)