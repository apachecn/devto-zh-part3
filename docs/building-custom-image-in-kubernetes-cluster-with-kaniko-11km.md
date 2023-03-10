# 使用 kaniko 在 kubernetes 集群中构建自定义映像

> 原文：<https://dev.to/ankitbansal/building-custom-image-in-kubernetes-cluster-with-kaniko-11km>

在将基础设施从定制编排引擎迁移到 Kubernetes 时，我们面临的一个挑战是如何构建映像。作为我们自己的编排引擎的一部分，我们过去常常动态构建图像并将其发布到 registry。由于 kubernetes 要求已经构建并发布了图像，这就需要在其上再加一层来构建图像并发布到 registry。

当在您的本地环境中工作时，这个问题不会经常出现，因为您通常在本地有一个 docker 守护进程来构建和发布图像。然而，当作为一个平台工作时，它不会工作，因为可能会有多人同时构建映像，因此您需要考虑构建映像所需的整个基础架构，例如服务器要求、自动扩展、安全性等。这是我们想要避免的，在寻找各种选项时，我们遇到了 kaniko，它提供了在集群中构建映像的功能。

虽然这只是用例之一，但还有其他场景，人们可能会选择这条路线，避免在本地机器上设置 docker。在本文中，将逐步讨论使用 kaniko 构建和发布图像的过程。

**创建构建上下文**

要使用 kaniko 构建映像，第一步是创建构建上下文并将其发布到一个兼容的存储中。构建上下文包含您的源代码、docker 文件等。本质上，它是您用来运行“docker build”命令的源文件夹。您需要将其压缩并发布到兼容的存储。在撰写本文时，kaniko 只支持 GCS/S3 存储。

不幸的是，我们在 openstack 上，所以额外的步骤是创建一个 kubernetes pod，从 openstack 下载应用程序 zip，并创建一个包含 dockerfile 和应用程序 zip 的上下文文件，最后将其推送到 S3。

**设置凭证**

创建映像需要两个凭据。一种是从存储器下载上下文，另一种是将图像推入 docker 注册表。对于 AWS，您可以使用您的密钥 id 和访问权限创建凭证文件。然后，该文件可用于创建机密。

```
[default]
aws_access_key_id=SECRET_ID
aws_secret_access_key=SECRET_TOKEN 
```

Enter fullscreen mode Exit fullscreen mode

> ku bectl create secret generic AWS-secret–from-file = credentials

需要的其他凭证是在注册表中推送图像文件。任何注册表都可以用来发布图片，我们使用 dockerhub 来发布图片。创建一个包含 base64 编码凭据的配置文件，并使用它来创建 configmap。

```
{
    "auths": {
        "https://index.docker.io/v1/": {
            "auth": "BASE_64_AUTH"
        },
    "HttpHeaders": {
        "User-Agent": "Docker-Client/18.06.1-ce (linux)"
    }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

> kubectl create config map docker-config–from-file = config . JSON

**创建部署以发布映像**

Kaniko 不使用 docker 守护进程来创建图像。相反，它有自己的逐行读取 dockerfile 并在完成后创建快照的机制。它在 gcr.io 中发布了自己的图像，并命名为 executor 来完成这个任务。

由于构建和发布图像是一次性的活动，我们创建 kubernetes job 来完成同样的任务。您需要安装 aws-secret 和 docker-config 进行身份验证。需要两个环境变量:AWS_REGION 提供上下文所在的区域名称，DOCKER_CONFIG 指定 DOCKER 凭证路径。Kaniko 将确保在创建快照时忽略这些文件夹。

```
apiVersion: batch/v1
kind: Job
metadata:
  name: image-publisher
spec:
  template:
    spec:
      containers:
      - name: image-publisher
        image: gcr.io/kaniko-project/executor:latest
        args: ["--dockerfile=dockerfile",
               "--context=s3://imagetestbucket123/context.tar.gz",
               "--destination=index.docker.io/ankitbansal/httpserver:1.0"]
        volumeMounts:
        - name: aws-secret
          mountPath: /root/.aws/
        - name: docker-config
          mountPath: /kaniko/.docker/
        env:
        - name: AWS_REGION
          value: us-east-2
        - name: DOCKER_CONFIG
          value: /kaniko/.docker
      restartPolicy: Never
      volumes:
      - name: aws-secret
        secret:
          secretName: aws-secret1
      - name: docker-config
        configMap:
          name: docker-config
  backoffLimit: 2 
```

Enter fullscreen mode Exit fullscreen mode

> kubectl create -f job.yaml

您可以跟踪 pod 日志，并查看创建和发布的图像:

```
INFO[0000] Downloading base image ruby:2.4.5-jessie     
INFO[0002] Unpacking rootfs as cmd RUN mkdir -p /u01/app/ && mkdir -p /u01/data/ && mkdir -p /u01/logs/ && groupadd myuser && groupadd builds && useradd -m -b /home -g myuser -G builds myuser && chown -R myuser:myuser /u01/ && chgrp -hR builds /usr/local requires it. 
INFO[0020] Taking snapshot of full filesystem...        
INFO[0033] Skipping paths under /kaniko, as it is a whitelisted directory 
INFO[0033] Skipping paths under /root/.aws, as it is a whitelisted directory 
INFO[0033] Skipping paths under /var/run, as it is a whitelisted directory 
INFO[0033] Skipping paths under /dev, as it is a whitelisted directory 
INFO[0033] Skipping paths under /proc, as it is a whitelisted directory 
INFO[0033] Skipping paths under /sys, as it is a whitelisted directory 
INFO[0038] ENV APP_HOME=/u01/app                        
INFO[0038] WORKDIR /u01/app                             
INFO[0038] cmd: workdir                                 
INFO[0038] Changed working directory to /u01/app        
INFO[0038] Creating directory /u01/app                  
INFO[0038] Taking snapshot of files...                  
INFO[0038] EXPOSE 8080                                  
INFO[0038] cmd: EXPOSE                                  
INFO[0038] Adding exposed port: 8080/tcp                
INFO[0038] RUN mkdir -p /u01/app/ && mkdir -p /u01/data/ && mkdir -p /u01/logs/ && groupadd myuser && groupadd builds && useradd -m -b /home -g myuser -G builds myuser && chown -R myuser:myuser /u01/ && chgrp -hR builds /usr/local 
INFO[0038] cmd: /bin/sh                                 
INFO[0038] args: [-c mkdir -p /u01/app/ && mkdir -p /u01/data/ && mkdir -p /u01/logs/ && groupadd myuser && groupadd builds && useradd -m -b /home -g myuser -G builds myuser && chown -R myuser:myuser /u01/ && chgrp -hR builds /usr/local] 
INFO[0039] Taking snapshot of full filesystem...        
INFO[0050] Skipping paths under /kaniko, as it is a whitelisted directory 
INFO[0050] Skipping paths under /root/.aws, as it is a whitelisted directory 
INFO[0050] Skipping paths under /var/run, as it is a whitelisted directory 
INFO[0051] Skipping paths under /dev, as it is a whitelisted directory 
INFO[0051] Skipping paths under /proc, as it is a whitelisted directory 
INFO[0051] Skipping paths under /sys, as it is a whitelisted directory 
INFO[0056] Using files from context: [/kaniko/buildcontext/appcontent] 
INFO[0056] ADD appcontent/ /u01/app                     
INFO[0056] Taking snapshot of files...                  
INFO[0056] USER myuser                                   
INFO[0056] cmd: USER                                    
2019/05/12 03:56:32 existing blob: sha256:053381643ee38d023c962f8789bb89be21aca864723989f7f69add5f56bd0472
2019/05/12 03:56:32 existing blob: sha256:e0ac5d162547af1273e1dc1293be3820a8c5b3f8e720d0d1d2edc969456f41aa
2019/05/12 03:56:32 existing blob: sha256:09e4a5c080c5192d682a688c786bffc1702566a0e5127262966fdb3f8c64ef45
2019/05/12 03:56:32 existing blob: sha256:14af2901e14150c042e83f3a47375b29b39f7bc31d8c49ad8d4fa582f4eb0627
2019/05/12 03:56:32 existing blob: sha256:6cc848917b0a4c37d6f00a2db476e407c6b36ce371a07e421e1b3b943ed64cba
2019/05/12 03:56:32 existing blob: sha256:62fe5b9a5ae4df86ade5163499bec6552c354611960eabfc7f1391f9e9f57945
2019/05/12 03:56:32 existing blob: sha256:bf295113f40dde5826c75de78b0aaa190302b3b467a3d6a3f222498b0ad1cea3
2019/05/12 03:56:33 pushed blob sha256:7baebbfb1ec4f9ab9d5998eefc78ebdfc063b9547df4395049c5f8a2a359ee20
2019/05/12 03:56:33 pushed blob sha256:6850b912246a34581de92e13238ac41c3389c136d25155d6cbe1c706baf3bc0e
2019/05/12 03:56:33 pushed blob sha256:0f9697e63b4482512d41f803b518ba3fb97bde20b21bec23c14bccc15f89e9f7
2019/05/12 03:56:42 pushed blob sha256:90e84d259def7af68e106b314e669056cb029d7a5d754d85cf0388419a5f2bcd
2019/05/12 03:56:43 index.docker.io/ankitbansal/httpserver:1.0: digest: sha256:13c218bb98623701eb6fd982b49bc3f90791695ce4306530c75b2094a8fdd468 size: 1896 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成，你应该能够验证注册图像。

**使用映像部署应用**

就是这样。现在，您应该能够使用这个映像来创建部署，并验证该映像工作正常。

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rubyapp-deployment
spec:
  replicas: 1 # tells deployment to run 2 pods matching the template
  selector:
    matchLabels:
      app: rubyapp1
  template: # create pods using pod definition in this template
    metadata:
      # unlike pod-nginx.yaml, the name is not included in the meta data as a unique name is
      # generated from the deployment name
      labels:
        app: rubyapp1
    spec:
      containers:
      - name: httpserverruby
        image: ankitbansal/httpserver:1.0
        imagePullPolicy: Always
        command: [ruby]
        args: ["httpserver.rb"]
        ports:
        - containerPort: 8080
        resources:
          limits:
            memory: "1Gi"
          requests:
            memory: "1Gi"
        env:
        - name: APP_HOME
          value: "/u01/app"
        - name: PORT
          value: "8080" 
```

Enter fullscreen mode Exit fullscreen mode

并使用 curl 验证响应

**为什么不简单地使用 kubernetes docker 守护进程**

既然 kubernetes 已经在其节点上运行了 docker 守护进程，那么问题就来了，为什么不使用相同的 docker 守护进程来构建映像呢？这不是一个很好的选择，因为它要求容器是一个特权容器。特权容器拥有容器的所有功能，并且不再有 cgroup 设置的任何限制。本质上它能做任何宿主能做的事。这可能会造成严重的威胁。