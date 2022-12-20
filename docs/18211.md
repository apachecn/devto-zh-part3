# 在 Terraform 中启动和运行模板

> 原文：<https://dev.to/jmarhee/up-and-running-with-templates-in-terraform-5g28>

Terraform 有很多很棒的格式化和模板工具，但我特别经常使用的一个是`template_file` [数据源](https://www.terraform.io/docs/providers/template/d/file.html)，在我的例子中，它使从我的云提供商资源(以及当我运行 Terraform 时在本地生成的其他资源，然后在我的远程状态存储中管理)向这些模板传递可变数据变得非常简单，并在我的例子中，当我要求我的提供商创建计算资源时，使用它们与`cloud-init`。

因此，让我们从一个简单的有用用例开始。我的用例是提供一个 Kubernetes 集群，因此对于我的集群节点，cloud-init 脚本(在我的例子中，只是一个 bash 脚本，但这可以用于任何文本资源)需要运行 API 服务器的集群控制器 IP 地址、使用的 Kubernetes 版本和集群令牌。

在这方面，我的脚本非常简单，模板保存为`node.tpl`:

```
#!/bin/bash

function install_docker() {
 apt-get update; \
 apt-get install -y docker.io

 cat << EOF > /etc/docker/daemon.json
 {
   "exec-opts": ["native.cgroupdriver=cgroupfs"]
 } EOF }

function install_kube_tools() {
 apt-get update && apt-get install -y apt-transport-https
 curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
 echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list
 apt-get update
 apt-get install -y kubelet=${kube_version} kubeadm=${kube_version} kubectl=${kube_version}
}

function join_cluster() {
    kubeadm join "${primary_node_ip}:6443" --token "${kube_token}" --discovery-token-unsafe-skip-ca-verification
} 
```

你会看到我调用了像`kube_version`、`kube_token`和`primary_node_ip`这样的变量来传递来自 Terraform 清单的变量值，我们一会儿会讲到。

在`node.tf`清单中，我们定义了计算资源:

```
resource "digitalocean_droplet" "k8s_node" {
  name      = "${format("${var.cluster_name}-node-%02d", count.index)}"
  image     = "ubuntu-16-04-x64"
  count     = "${var.count}"
  size      = "${var.primary_size}"
  region    = "${var.region}"
  private_networking = "true"
  ssh_keys  = "${var.ssh_key_fingerprints}"
  user_data = "${data.template_file.node.rendered}"
} 
```

您将看到`user_data`(cloud-init 的数字海洋提供商习语)，我们引用的是`data.template_file.node.rendered`，它将包含我们上面的模板的渲染版本，但是为了渲染它，我们需要首先创建资源，然后填充那些资源本地的值:

```
data "template_file" "node" {
  template = "${file("${path.module}/node.tpl")}"

  vars {
    kube_token      = "${random_string.kube_init_token_a.result}.${random_string.kube_init_token_b.result}"
    primary_node_ip = "${digitalocean_droplet.k8s_primary.ipv4_address}"
    kube_version    = "${var.kubernetes_version}"
  }
} 
```

因此，您将看到我将我的`vars`设置为各种不同的源，一些引用了来自另一个资源的属性(例如，我的`k8s_primary`节点的`primary_ipv4_address`，和一个用户变量`kubernetes_version`，然后在我的项目中的其他地方生成一个随机字符串)。

然后，当您进入`apply` Terraform 时，`user_data`字段将由渲染模板填充，您提供的数据将替换相关变量。