# 使用 TerraForm 构建 Solr 集群——第 1 部分

> 原文：<https://dev.to/documentednerd/building-a-solr-cluster-with-terraform-part-1-2cm2>

所以毫不奇怪，我一直在谈论 TerraForm 有多神奇，最近我对 Solr 以及如何构建一个可扩展的 Solr 集群进行了大量研究。

因此，鉴于 kubernetes 模板，我想尝试类似的东西。该项目的目标如下:

1.  构建一个通用模板，用分布式 shard 创建 Solr 云集群。
2.  构建扩展集群的能力，现在使用 TerraForm 手动触发集群大小的增加。
3.  使节点自动将自己添加到集群中。

我可以用 bash 脚本和 packer 来完成。而是想尝试一下云初始化。

但这将是最终的结果，我想演练一下，我到最后所经历的各个步骤。真正的第一步是在要实现的 linux 机器上安装 Solr。

所以我们先从“Solr 是什么？”答案是 Solr 是一个开源软件解决方案，它提供了创建搜索引擎的方法。它与 ElasticSearch 和其他技术是一脉相承的。Solr 已经存在很长时间了，一些实施搜索的大公司使用它来处理客户的搜索请求。其中一些名字是网飞和凯业必达。请参见下面的链接:

*   【CareerBuilder 如何用 Apache Lucene/Solr 执行语义和多语言策略？
*   [从 FAST 到 Solr？](https://www.rosette.com/case-studies/careerbuilder/)
*   [Solr 的性能优化](https://www.youtube.com/watch?v=8L80ABTedj4&feature=youtu.be&t=935)

所以我决定尝试一下，创建我的第一个 Solr 集群，并回顾了入门指南。

所以我最终对它进行了更深入的研究，并构建了以下脚本来创建一个“入门”solr 集群。

```
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-commonsudo apt-get install -y gnupg-curlsudo wget https://www.apache.org/dist/lucene/solr/8.0.0/solr-8.0.0.zip.asc | sudo apt-key addsudo apt-get update -ysudo apt-get install unzipsudo wget http://mirror.cogentco.com/pub/apache/lucene/solr/8.0.0/solr-8.0.0.zipsudo unzip -q solr-8.0.0.ziplssudo mv solr-8.0.0 /usr/local/bin/solr-8.0.0 -fsudo rm solr-8.0.0.zip -fsudo apt-get install -y default-jdksudo chmod +x /usr/local/bin/solr-8.0.0/bin/solrsudo chmod +x /usr/local/bin/solr-8.0.0/example/cloud/node1/solrsudo chmod +x /usr/local/bin/solr-8.0.0/example/cloud/node2/solrsudo /usr/local/bin/solr-8.0.0/bin/bin/solr -e cloud -noprompt 
```

上面将配置一个“入门 solr 集群”,它利用了所有的系统默认设置，几乎不是一个生产实现。所以我的下一步将是改变这一点。但是为了让某些东西运行起来，我使用下面的 json 将上面的脚本移到一个打包模板中。上面的脚本是“../scripts/Solr/provision.sh "

```
{ "variables": { "deployment\_code": "", "resource\_group": "", "subscription\_id": "", "location": "", "cloud\_environment\_name": "Public" }, "builders": [{ "type": "azure-arm", "cloud\_environment\_name": "{{user `cloud_environment_name`}}", "subscription\_id": "{{user `subscription_id`}}", "managed\_image\_resource\_group\_name": "{{user `resource_group`}}", "managed\_image\_name": "Ubuntu\_16.04\_{{isotime \"2006\_01\_02\_15\_04\"}}", "managed\_image\_storage\_account\_type": "Premium\_LRS", "os\_type": "Linux", "image\_publisher": "Canonical", "image\_offer": "UbuntuServer", "image\_sku": "16.04-LTS", "location": "{{user `location`}}", "vm\_size": "Standard\_F2s" }], "provisioners": [{ "type": "shell", "script": "../scripts/ubuntu/update.sh" }, { "type": "shell", "script": "../scripts/Solr/provision.sh" }, { "execute\_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'", "inline": [ "/usr/sbin/waagent -force -deprovision+user &amp;&amp; export HISTSIZE=0 &amp;&amp; sync"], "inline\_shebang": "/bin/sh -e", "type": "shell" }]} 
```

唯一提到的另一个脚本是“update.sh ”,它包含以下逻辑，用于安装 cli 和更新 ubuntu 映像:

```
#! /bin/bashsudo apt-get update -ysudo apt-get upgrade -y#Azure-CLIAZ\_REPO=$(sudo lsb\_release -cs)sudo echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ\_REPO main" | sudo tee /etc/apt/sources.list.d/azure-cli.listsudo curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -sudo apt-get install apt-transport-httpssudo apt-get update &amp;&amp; sudo apt-get install azure-cli 
```

所以上面的内容让我了解了一个很好的地方，可以用它来创建一个图像。

对于接下来的步骤，我将执行以下操作:

*   在脚本中构建一个更加“生产友好”的 Solr 实现。
*   调查利用 cloud init 而不是 Packer 的“黄金映像”体验。
*   使用 Zookeeper 构建模板来管理节点。