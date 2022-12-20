# Kubernetes 的 OpenStack 网络和安全组考虑事项

> 原文：<https://dev.to/jmarhee/openstack-networks-and-security-group-considerations-for-kubernetes-34ki>

面向 Kubernetes 的 [OpenStack 云提供商](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/#openstack)允许在本地配置资源，如 Cinder volumes 和 LBaaS，以提供 Kubernetes 持久存储和负载平衡器服务。

如果您在 OpenStack 提供的云服务上运行 Kubernetes，并且像我一样在您的项目中拥有 API 访问权限，那么下面的配置允许您创建一个仅支持集群的网络，并具有适当的安全组。

如果您的计算机上当前没有安装 OpenStack CLI 工具，您可以使用以下工具:

```
pip install --upgrade --requirement \
http://raw.githubusercontent.com/platform9/support-locker/master/openstack-clients/requirements.txt \
--constraint \
http://raw.githubusercontent.com/openstack/requirements/stable/newton/upper-constraints.txt 
```

来安装它们。

我对您的网络配置做了如下假设:如果您有一个由您的基础架构提供商(或者如果您自己操作 OpenStack，则由您的基础架构团队)提供的[物理上游网络连接的](https://docs.openstack.org/install-guide/launch-instance-networks-provider.html) IPv4 子网，这将被配置为一个浮动 IP 池，我们将在`public`网络对象中将该子网称为`public_subnet`。我还假设你正在使用中子网络。

创建一个路由器，我们将用于上述浮动 IP 池和您的专用网络:

```
neutron router-create router-name 
```

并将其设置为您的`public`网络的默认网关:

```
neutron router-gateway-set router-name public 
```

我们将创建一个专用网络，用于 Kubernetes 节点，作为浮动 IP 池的锚点，我们将很快创建:

```
neutron net-create kube_private_network && \
neutron subnet-create --name private_subnet kube_private_network 192.168.200.0/24 && \
neutron router-interface-add router-name private_subnet 
```

因此，当创建连接到该网络的实例时，它们将从 192.168.200.2-254 获得一个地址。您将从您的`public`网络子网提供浮动 IP 作为一次性任务:

```
neutron floatingip-create public 
```

并在创建时将它们关联起来。

对于您的 Kubernetes `cloud.conf`，在 Kubernetes 中创建资源时，您需要您希望用于负载平衡器对象的 IP 范围的子网 ID:

```
~ » openstack subnet list                                                                                         2 ↵
+--------------------------------------+----------------+--------------------------------------+------------------+
| ID                                   | Name           | Network                              | Subnet           |
+--------------------------------------+----------------+--------------------------------------+------------------+
| 05a55a42-ff5e-4b53-a6a7-e9f4d1cc5a56 | subnet1        | 0048fce6-c715-4106-a810-473620326cb0 | 192.168.100.0/24   |
| ecc841d0-1ac4-419f-9a2e-a9fffbd44922 | private_subnet | a133a2c0-e16a-4a23-ac8e-df1e270169ed | 192.168.200.0/24 |
+--------------------------------------+----------------+--------------------------------------+------------------+ 
```

举例来说，我想使用我的`192.168.100.0/24`子网，所以稍后我将使用该子网 ID 的节点。

然后，您可以继续创建安全组:

```
openstack security group create kubernetes 
```

您只需要 Openstack RC 文件中的项目 ID，就可以继续创建您的规则:

```
openstack security group rule create \                                                                        
--dst-port 6443 \
--ingress \
--project $PROJECT_ID \
--src-ip 192.168.200.0/24 \
kubernetes 
```

例如，这允许集群中的所有节点访问 API 服务器端口。

一旦所有这些都完成，通常与您使用的 Kube 部署方法无关，您将需要填充您的`cloud.conf`，正如我之前提到的，对于 Kubernetes 中的 [OpenStack 云提供商](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/#typical-configuration)，使用之前提到的 OpenStack 子网 ID，您可以像这样填充它:

```
[Global]
username=${user_name}
tenant-id=${tenant_id}
password=${password}
auth-url=$AUTH_URL
[LoadBalancer]
subnet-id=${lb_subnet_id}
floating-network-id=${floating_network_id}
lb-method=ROUND_ROBIN
lb-provider=amphora
[BlockStorage]
bs-version=auto 
```

在您完成其余的设置之前。对于 Kubeadm 用户来说，以下指南是为 Kubernetes 设置 Openstack 云提供商的绝佳资源:

[Kubernetes 与 OpenStack 云提供商合作](https://medium.com/@arthur.souzamiranda/kubernetes-with-openstack-cloud-provider-current-state-and-upcoming-changes-part-1-of-2-48b161ea449a)