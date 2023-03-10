# Kubernetes:证书、令牌、认证和服务帐户

> 原文：<https://dev.to/danielkun/kubernetes-certificates-tokens-authentication-and-service-accounts-4fj7>

主要是为了个人/学习体验，我创建了相当多的 Kubernetes 集群，比如我的 [Raspberry Pi rack](https://dev.to/danielkun/kubernetes-its-alive-2ndc) 上的集群。我还在来自 [Hetzner Cloud](https://hetzner.cloud) 的超廉价云服务器上创建了两个集群，分别用于生产和暂存环境。幸运的是，这些环境中没有严肃的商业。

**免责声明:**我不是 Kubernetes 专家，也不是安全专家，所以在你依赖他们之前，请确保你在这篇文章中找到的信息是二手的。我只是想发表我在这次旅行中的经历和感悟——谢谢！

## 为什么会幸运？

因为**我不小心泄露了我的管理员访问临时集群的证书**。我试图使用 [CircleCI](https://circleci.com/) 为一个开源项目建立一个 CI/CD 管道。当我一步一步地测试时，我转储了从 BASE64 编码的环境变量创建的`${HOME}/.kube/config`的内容，就像这篇博文中的[所描述的那样。不过这是致命的，因为 a)开源项目的作业日志是公开可见的，b)作业和它们的日志不能被手动删除，我不得不寻求支持。*哎哟！*](https://blog.lwolf.org/post/how-to-create-ci-cd-pipeline-with-autodeploy-k8s-gitlab-helm/)

所以让我们深入了解一下这里发生了什么。

## 创建集群

首先，我使用`kubeadm`手动创建集群，遵循[官方文档](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/)。为此，我创建了一个启用了 RBAC 的集群，并且为我创建了一个`kube-config`,其中包含一个由证书标识的用户。

## 访问集群

在`kubeadm`成功创建集群后，它会指导您如何访问集群:

```
"Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:"

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config 
```

kubeadm 创建的`admin.conf`包含一个由证书标识的用户:

```
- name: kubernetes-admin
  user:
    client-certificate-data: <BASE64 ENCODED X509 CERTIFICATE>
    client-key-data:  <BASE64 ENCODED PRIVATE KEY FOR THE CERTIFICATE> 
```

到目前为止，遵循这些说明是您使用`kubectl`访问该集群的唯一方法，因此您应该现在就开始执行。在你复制了`admin.conf`之后，你就有了`cluster-admin`的权限。可以说你是`root`。

## 这是怎么回事？

`kubeadm`所做的是创建一个新的 CA (Certificate Authority)根证书，它是集群的主证书。大概是这样的:

```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 0 (0x0)
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = kubernetes
        Validity
            Not Before: May 19 11:11:04 2019 GMT
            Not After : May 16 11:11:04 2029 GMT
        Subject: CN = kubernetes
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    <REDACTED>
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment, Certificate Sign
            X509v3 Basic Constraints: critical
                CA:TRUE
    Signature Algorithm: sha256WithRSAEncryption
         <REDACTED> 
```

因此...除了它是一个 CA 和它的 CN (=通用名称)是 Kubernetes 之外，它实际上不包含太多信息。这是因为该证书仅充当群集上用于不同目的的其他证书的根。您可以看一下`/etc/kubernetes/pki`来看一下您的集群中使用的并且已经由 CA:
签名的一些证书

```
daniel@kube-box:~# ls /etc/kubernetes/pki/ -1
apiserver.crt
apiserver-etcd-client.crt
apiserver-etcd-client.key
apiserver.key
apiserver-kubelet-client.crt
apiserver-kubelet-client.key
ca.crt
ca.key
etcd
front-proxy-ca.crt
front-proxy-ca.key
front-proxy-client.crt
front-proxy-client.key
sa.key
sa.pub 
```

允许访问使用 CA 信任的证书对自己进行身份验证的客户端是可能的。这可以通过将`--client-ca-file`参数中的`ca.crt`传递给`kube-controller-manager`来实现。这是[医生](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/)对此的说法:

```
--client-ca-file string
If set, any request presenting a client certificate signed by one of the
authorities in the client-ca-file is authenticated with an identity 
corresponding to the CommonName of the client certificate. 
```

回到您的`kube-config`:您的`admin.conf`中 BASE64 包含的证书就是由这个 CA 签署的。这就是集群信任它的原因。让我们看看证书:

```
grep 'client-certificate-data: ' ${HOME}/.kube/config | \
   sed 's/.*client-certificate-data: //' | \
   base64 -d | \
   openssl x509 --in - --text

Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 3459994011761527671 (0x30045e38cc064b77)
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = kubernetes
        Validity
            Not Before: May 12 10:54:39 2019 GMT
            Not After : May 11 10:54:42 2020 GMT
        Subject: O = system:masters, CN = kubernetes-admin
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    <REDACTED>
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage:
                TLS Web Client Authentication
    Signature Algorithm: sha256WithRSAEncryption
         <REDACTED> 
```

这个证书告诉我们(和集群)什么？

a)它由我们的`kubernetes`集群
发布和信任 b)它标识组织(`O` ) `system:masters`，被 kubernetes 解释为一个组
c)它标识通用名称(`CN` ) `kubernetes-admin`，被 kubernetes 解释为一个用户

换句话说:这个证书以用户`kubernetes-admin`和组`system:masters`的身份登录。这就是为什么您不需要在`kube-config`中提供组名，也是为什么您可以在`kube-config`中随意更改用户名，而不会改变正在登录的实际用户。

## 权限定义在哪里？

在支持 RBAC 的集群中，权限是在`Roles`(每个名称空间)或`ClusterRoles`(所有名称空间)中定义的。然后使用`RoleBindings`和`ClusterRoleBindings`将这些权限授予对象。所以你需要寻找的是授予组`system:masters`或用户`kubernetes-admin`权限的`RoleBindings`和`ClusterRoleBindings`。您可以通过查看

`kubectl -A=true get rolebindings && kubectl -A=true get clusterrolebindings`

`kubeadm`为我创建的默认设置产生了一个搜索结果，名为`cluster-admin`的`ClusterRoleBinding`，它将权限授予了同名的`ClusterRole`。下面是定义:

```
kind: ClusterRole
metadata:
  name: cluster-admin
rules:
- apiGroups:
  - '*'
  resources:
  - '*'
  verbs:
  - '*'
- nonResourceURLs:
  - '*'
  verbs:
  - '*'
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters 
```

所以我们有它！证书授权的组`system:masters`使用所有动词向所有资源授予' * '权限，因此是完全访问。

未验证的假设:我认为这种情况下的用户和组只在证书中定义，新用户可以`created`通过颁发一个新证书，将 CommonName 设置为所需的用户名，将 Organisation 设置为所需的组。这个用户名和组可以在`ClusterRoleBindings`和`RoleBindings`中使用。不过，我没有花时间来验证这一点，通过使用`openssl`发布一个新的证书，用集群的 CA 签名，这是可能的。如果有人能在评论中证实或揭穿这个假设，那就太好了！

## 谜

因此，到目前为止，我已经了解了如何识别用户和组，以及如何将权限授予该用户和组。我当时的猜测是，当我删除`ClusterRoleBinding`，或者更确切地说，从其中删除组`system:masters`时，证书应该不再能够访问集群。如果我这样做了，并且得到了预期的结果，我将失去对集群的所有访问权限，并且会成功地将我永久注销。所以我首先添加了一个`serviceaccount`，并创建了一个`kube-config`，它使用这个`serviceaccount`的令牌登录，并验证访问是否有效。我们将在后面看到如何做到这一点。然后，在将安全网设置到位后，我将`system:masters`对象从`ClusterRoleBinding`中移除。令我惊讶的是，这并没有锁定用户。我仍然可以使用旧的`kube-config`完全访问集群…也许有人可以在评论中解释这种行为？

## 替代方案 1:更换 CA

让泄漏的证书变得无用的一个绝对可靠的方法是替换集群中的 CA。不过，这需要重启集群。这将需要重新颁发我们在上面看到的所有证书，甚至更多。我估计完全搞砸一切并在旅途中浪费数小时的可能性约为 99%，所以我放弃了这个计划。:-)

## 备选方案 2:重建整个集群

幸运的是，这是一个临时集群，所以我有足够的自由。在开始调查之前，我关闭了所有节点的电源。然后，在没有找到一个合适的解决方案来使泄漏的证书无用之后，我使用
杀死了整个集群

```
kubeadm reset
rm -rf /etc/kubernetes
rm -rf /var/lib/kubelet 
```

用 kubeadm 从头开始重建。(顺便说一句，这太棒了！！)

然后我继续做了几十次，读着‘不再打印 kube-config 的内容’，‘让 CI/CD 工作’，‘也许现在它工作了’，‘嗯，什么？’，‘那必须工作’，‘去他妈的 CI/CD’，… :-)

## 经验教训:使用带有令牌的服务帐户

(或者像 OpenID 这样的其他认证方法，正如[这篇牛逼的帖子](//dev.to/petermbenjamin/kubernetes-security-best-practices-hlk)中所推荐的。)

所以我学到的经验是做我在大型托管 kubernetes 提供商那里看到的事情:使用服务帐户和它的访问令牌进行授权。这里我将展示如何设置一个使用令牌而不是证书的超级用户:

```
kubectl -n kube-system create serviceaccount admin 
```

要授予超级用户权限，最简单的方法是创建一个新的`ClusterRoleBinding`，将这个服务帐户绑定到`cluster-admin` `ClusterRole` :

```
kubectl create clusterrolebinding add-on-cluster-admin \
  --clusterrole=cluster-admin \
  --serviceaccount=kube-system:admin 
```

### 使用您的新服务帐户

您的管理员用户现在已经准备就绪。现在我们需要用这个用户登录。我假设你在`${HOME}/.kube/conf`中有`admin.conf`。我们现在想添加一个新用户，用它的令牌来标识，并添加一个使用这个用户的新上下文:

```
TOKENNAME=`kubectl -n kube-system get serviceaccount/admin -o jsonpath='{.secrets[0].name}'`
TOKEN=`kubectl -n kube-system get secret $TOKENNAME -o jsonpath='{.data.token}' | base64 -d`
kubectl config set-credentials admin --token=$TOKEN
kubectl config set-context admin@kubernetes --cluster kubernetes --user admin 
```

现在，继续尝试您的全新闪亮服务帐户:

```
kubectl config use-context admin@kubernetes
kubectl -n kube-system get all 
```

如果一切顺利，您应该继续删除基于证书的用户和相应的上下文:

```
kubectl config unset users.kubernetes-admin
kubectl config delete-context kubernetes-admin@kubernetes 
```

耶。现在我们有了一个`kube-conf`，它只包括基于令牌的访问。这很棒，因为如果此配置可能被泄露或发布，则很容易撤销该令牌。

### 如何使泄露的令牌失效

这很简单！只需删除与用户令牌对应的秘密。我们已经看到了如何找出哪个是正确的秘密:

```
kubectl -n kube-system get serviceaccount/admin -o yaml 
```

您将在“机密”数组中看到一个“名称”字段。这是保存此服务帐户令牌的机密名称。现在直接删除它:

```
kubectl -n kube-system delete secrets/token-admin-xyz123 
```

然后等待几秒钟，并尝试访问您的集群:

```
dainel@kube-box:~# kubectl -n kube-system get all
error: You must be logged in to the server (Unauthorized) 
```

Wohoo！

但是你如何重新获得访问权限呢？如果您在主节点上，只需将`admin.conf`复制回`${HOME}/.kube/conf`并重复从“使用您的新服务帐户”开始的步骤。Kubernetes 现在应该已经创建并分配了一个新令牌。

我希望这有所帮助，我很乐意听到反馈，勘误表等。在评论里！

另外，请务必阅读 Peter Benjamin 的文章[“Kubernetes 安全最佳实践”](//dev.to/petermbenjamin/kubernetes-security-best-practices-hlk)。

非常感谢安德烈亚斯·安东森、维兹基和艾伦·J·卡斯顿圭，他们在官方的 Kubernetes Slack 频道帮助我更好地了解正在发生的事情。