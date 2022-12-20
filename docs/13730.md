# Kubernetes:使用配置图在 Pod 中挂载一个文件

> 原文：<https://dev.to/cmendibl3/kubernetes-mount-a-file-in-your-pod-using-a-configmap-50of>

最近我一直在学习围棋，本周我开始了一个名为 kube-夏洛克的业余项目。这个小程序的目的是列出任何没有贵组织要求的**标签**的 pod。

对于[库贝-夏洛克](https://github.com/cmendible/kube-sherlock)，我创建了一个 [dockerfile](https://github.com/cmendible/kube-sherlock/blob/master/dockerfile) ，其中程序(库贝-夏洛克)和默认配置(config.yaml)都放在 **app** 文件夹:

```
FROM golang:1.11.5 AS build
WORKDIR /src
ADD go.mod go.sum ./
RUN go get -v
ADD kube-sherlock.go config.yaml ./
RUN CGO_ENABLED=0 GOOS=linux go build -a -ldflags '-w'

FROM alpine:3.7
COPY --from=build src/config.yaml app/config.yaml
COPY --from=build src/kube-sherlock app/kube-sherlock
WORKDIR /app
CMD ./kube-sherlock

# Metadata
ARG BUILD_DATE
ARG VCS_REF
LABEL org.label-schema.build-date=$BUILD_DATE \
    org.label-schema.name="kube-sherlock" \
    org.label-schema.description="Check if labels are applied to your containers" \
    org.label-schema.url="https://github.com/cmendible/kube-sherlock" \
    org.label-schema.vcs-ref=$VCS_REF \
    org.label-schema.vcs-url="https://github.com/cmendible/kube-sherlock" \
    org.label-schema.schema-version="0.1" 
```

Enter fullscreen mode Exit fullscreen mode

那么如果要替换默认配置呢？

您可以在一个**配置映射**的帮助下实现这一点，用您的自定义值
创建一个新的**配置. yaml**

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: sherlock-config
  namespace: default
data:
  config.yaml: |
    namespaces:
      - default
    labels:
      - "app"
      - "owner" 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:我用文件名作为**键**。

然后创建一个 pod 定义，引用**配置图** :

```
apiVersion: v1
kind: Pod
metadata:
  name: kube-sherlock
spec:
  serviceAccountName: kube-sherlock
  containers:
    - name: kube-sherlock
      image: cmendibl3/kube-sherlock:0.1
      volumeMounts:
      - name: config-volume
        mountPath: /app/config.yaml
        subPath: config.yaml
  volumes:
    - name: config-volume
      configMap:
        name: sherlock-config
  restartPolicy: Never 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:卷引用**配置图**(夏洛克-配置)，卷挂载指定**挂载路径**为你要替换的文件(/app/config.yaml)**子路径**属性用于通过 key 引用文件(config . YAML)

希望有帮助。