# 使用 Terraform 自动化 Kubernetes 节点池

> 原文：<https://dev.to/jmarhee/automating-kubernetes-node-pools-with-terraform-1e5l>

当您想要在 Kubernetes 中添加、删除或调整节点池时，快速引导这些节点并让它们重新加入集群的能力非常重要，能够以可重复、省时的方式完成。

Terraform 管理状态的方式使得将您的节点部署为池的一部分是我喜欢使用它来帮助自动化集群操作的一个原因。例如，在我的 Terraform 变量中，我可能有像`kube_version`或`count`这样可以改变的变量，当您计划和应用您的新状态时，Terraform 将尝试与您的提供者协调这种状态改变。

在部署到 DigitalOcean 的 Terraform 项目示例中:

[https://bitbucket.org/jmarhee/cousteau/src](https://bitbucket.org/jmarhee/cousteau/src)

你可以在这里阅读更多关于[使用回购的信息，在`kube-node.tf`中，我们可以修改它来定义一个池，如下所示:](https://dev.to/jmarhee/deploying-kubernetes-and-digitalocean-cloud-controller-manager-on-digitalocean-with-terraform--371)

```
data "template_file" "node" {
  template = "${file("${path.module}/node.tpl")}"

  vars {
    kube_token      = "${random_string.kube_init_token_a.result}.${random_string.kube_init_token_b.result}"
    primary_node_ip = "${digitalocean_droplet.k8s_primary.ipv4_address}"
    kube_version    = "${var.kubernetes_version}"
  }
}

resource "digitalocean_droplet" "k8s_node" {
  name               = "${format("pool1-%02d", count.index)}"
  image              = "ubuntu-16-04-x64"
  count              = "${var.count}"
  size               = "${var.primary_size}"
  region             = "${var.region}"
  private_networking = "true"
  ssh_keys           = "${var.ssh_key_fingerprints}"
  user_data          = "${data.template_file.node.rendered}"
} 
```

创建一个节点池，大小为`${var.count}`个节点，名称为`pool1`。

当您运行`terraform plan`时，您会得到一个反映这一点的状态:

```
 + digitalocean_droplet.k8s_node_standby
      id:                   <computed>
      backups:              "false"
      disk:                 <computed>
      image:                "ubuntu-16-04-x64"
      ipv4_address:         <computed>
      ipv4_address_private: <computed>
      ipv6:                 "false"
      ipv6_address:         <computed>
      ipv6_address_private: <computed>
      locked:               <computed>
      memory:               <computed>
      monitoring:           "false"
      name:                 "poolv1-00"
      price_hourly:         <computed>
      price_monthly:        <computed>
      private_networking:   "true"
      region:               "tor1"
      resize_disk:          "true"
      size:                 "4gb"
      ssh_keys.#:           "1"
      ssh_keys:  ""
      status:               <computed>
      user_data:            ""
      vcpus:                <computed>
      volume_ids.#:         <computed> 
```

并且当您在调整了`count`变量的情况下计划和应用时，它会相应地添加或删除该类型的资源。

然而，Kubernetes 的一个很好的特性是能够隔离和排出您希望从循环中删除的节点，所以假设您希望使用新的`kube_version`值部署新的节点池(即从 1.11 版升级到 1.12 版)。在`node.tf`中，添加一个新的池，像这样叫它`poolv2`:

```
resource "digitalocean_droplet" "k8s_node" {
  name               = "${format("pool2-%02d", count.index)}"
  image              = "ubuntu-16-04-x64"
  count              = "${var.count}"
  size               = "${var.primary_size}"
  region             = "${var.region}"
  private_networking = "true"
  ssh_keys           = "${var.ssh_key_fingerprints}"
  user_data          = "${data.template_file.node.rendered}"
} 
```

规划并应用，您将拥有两组节点，一组名为 pool1，另一组名为 pool2:

```
# kubectl get nodes
NAME                              STATUS     ROLES    AGE     VERSION
digitalocean-k8s-pool1-00          Ready      <none>   4m45s   v1.12.3
digitalocean-k8s-pool1-01          Ready      <none>   4m42s   v1.12.3
digitalocean-k8s-pool1-02          Ready      <none>   4m48s   v1.12.3
digitalocean-k8s-pool1-03          Ready      <none>   4m54s   v1.12.3
digitalocean-k8s-pool1-04          Ready      <none>   4m51s   v1.12.3
digitalocean-k8s-pool2-00          NotReady   <none>   3m27s   v1.13.1
digitalocean-k8s-primary           Ready      master   7m1s    v1.13.1 
```

此时，您可以在 Kubernetes 集群上执行如下操作:

```
kubectl cordon digitalocean-k8s-pool1-00 
```

在所有的`pool1`节点上，然后，一旦它们都被封锁，清空它们以将工作负载移动到新的池，从而避免不得不强制 Kubernetes 重新调度资源(尽管这也是一种有效的模式):

```
kubectl drain digitalocean-k8s-node-00 --ignore-daemonsets 
```

当你做这样的操作时:

```
kubectl describe node digitalocean-k8s-node-00 
```

您应该看到 Pods 终止，或者完全消失，并看到它们在 pool2 节点上被调度。

此时，您可以将`node.tf`中的`count`参数重新设置为 0，然后计划并应用 Terraform 来终止该池。

为了使这种配置更加健壮，让我们以蓝/绿部署为例，假设我们总是至少有两个池，并修改`node.tf`如下所示:

```
data "template_file" "node" {
  template = "${file("${path.module}/node.tpl")}"

  vars {
    kube_token      = "${random_string.kube_init_token_a.result}.${random_string.kube_init_token_b.result}"
    primary_node_ip = "${digitalocean_droplet.k8s_primary.ipv4_address}"
    kube_version    = "${var.kubernetes_version}"
  }
}

resource "digitalocean_droplet" "k8s_node_pool_blue" {
  name               = "${format("${var.cluster_name}-node-blue-%02d", count.index)}"
  image              = "ubuntu-16-04-x64"
  count              = "${var.count_blue}"
  size               = "${var.primary_size}"
  region             = "${var.region}"
  private_networking = "true"
  ssh_keys           = "${var.ssh_key_fingerprints}"
  user_data          = "${data.template_file.node.rendered}"
}

resource "digitalocean_droplet" "k8s_node_pool_green" {
  name               = "${format("${var.cluster_name}-node-green-%02d", count.index)}"
  image              = "ubuntu-16-04-x64"
  count              = "${var.count_green}"
  size               = "${var.primary_size}"
  region             = "${var.region}"
  private_networking = "true"
  ssh_keys           = "${var.ssh_key_fingerprints}"
  user_data          = "${data.template_file.node.rendered}"
} 
```

并更新`vars.tf`以更敏感地处理此问题:

```
-variable "count" {
-  default     = "3"
-  description = "Number of nodes."
+variable "count_blue" {
+  description = "Number of nodes in pool blue."
+}
+
+variable "count_green" {
+  description = "Number of nodes in pool green." 
```

因此，我们有一个`count_blue`和`count_green`来管理这些池的大小，并在您的`terraform.tfvars`文件中管理这些大小以进行缩放。例如，您的新流程将是，如果`pool_blue`在 3 个节点上是活动的，那么`pool_green`也扩展到 3 个，封锁并清空`pool_blue`的节点，验证服务在`pool_green`上是启动的，然后将`pool_blue`扩展到 0 并应用。

例如，这将是一个很好重构候选；池可以作为 Terraform 模块进行管理，然后您可以运行多种类型的池，例如，使用不同的实例类型(高 CPU、高性能存储等。)并将应用于我们示例中所有节点池的许多选项放入一个新的模板状态。

## 进一步阅读

[安全排空节点，同时遵守应用程序 SLOs](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)
[编写可重用的 Terraform 模块](https://www.nearform.com/blog/writing-reusable-terraform-modules/)
[在具有 Terraform 的数字海洋上部署 Kubernetes(和数字海洋云控制器管理器)](https://dev.to/jmarhee/deploying-kubernetes-and-digitalocean-cloud-controller-manager-on-digitalocean-with-terraform--371)
[Cousteau-Manage Kubernetes+在具有 Terraform 的数字海洋上执行 CCM](https://bitbucket.org/jmarhee/cousteau/src/master/)