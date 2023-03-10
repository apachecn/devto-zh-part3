# 具有 SaltStack 和 EC2 的自源 Cassandra 集群

> 原文：<https://dev.to/dmfay/a-self-sourcing-cassandra-cluster-with-saltstack-and-ec2-48kd>

出于许多非常好的理由，任何对生产 Cassandra 集群感兴趣的人都被建议首先在测试环境中进行尝试。以下是如何有效地利用这些环境的方法。

我们试图对 Cassandra 集群做的有趣的事情实际上是两件事:从 v2 升级到 v3，同时将 Cassandra 本身从当前运行 Cassandra 以及其他一些重要东西的 EC2 服务器组中分离出来。我们有一个“宠物”的情况，并希望有一个“牛”的情况，根据比尔·贝克:宠物有名字，你深深地关心每个人的福祉，而牛，不要太客气地说，是可替代的。如果我们能够将新的专用节点引入集群，随着复制的进行开始移除原始节点，并最终升级 Theseus 的数据库，这将是一个重大的进步——甚至不需要停机！但这需要大量的测试，更不用说真正管理新节点了。

除了数据管道，我们已经使用 SaltStack 来监控和管理我们基础设施的其他领域，SaltStack 包括一个可以与 EC2 一起工作的“盐云”模块。我宁愿有一个单一的基础设施作为代码的解决方案，所以这部分都是好的。不是:官方的 Cassandra 公式更适合于单节点实例或一些需要组装的集群，供应是一个单独的问题。我希望能够随心所欲地创建和销毁集群，所以我需要尽可能自动化。

## 盐云配置

连接 salt-cloud 的第一步是设置一个提供者和配置文件。在 Salt master 上，它们位于/etc/cloud.providers.d 和/etc/cloud.profiles.d 中。我们将所有内容保存在源代码控制中，并对这些目录进行符号链接。

我们的云资源托管在 AWS 上，所以我们使用的是 [EC2 提供商](https://docs.saltstack.com/en/latest/topics/cloud/aws.html)。这部分基本上是股票，但在配置文件中，我们确实需要为 Cassandra 节点本身定义一个模板。

### etc/cloud . profiles . d/ec2 . conf

```
cassandra_node:
  provider: [your provider from etc/cloud.providers.d/ec2.conf]
  image: ami-abc123
  ssh_interface: private_ips
  size: m5.large
  securitygroup:
    - default
    - others 
```

### cassandra-test.map

使用配置文件配置中定义的`cassandra_node`模板，我们可以在*映射文件*中建立集群布局。文件名不重要；我的名字是 cassandra-test.map。需要注意的一点是，我们正在为我们的节点建立一个命名约定:`cassandra-*`。每个节点也被定义为`t2.small`大小，覆盖了默认的`m5.large`——我们在测试时不需要所有的马力！`t2.micro`然而，事实证明，运行卡珊德拉的动力不足。

```
cassandra_node:
  - cassandra-1:
      size: t2.small
      cassandra-seed: true
  - cassandra-2:
      size: t2.small
      cassandra-seed: true
  - cassandra-3:
      size: t2.small 
```

`cassandra-seed`(就此而言还有`size`)是*粮*，这是每个盐管“宠臣”自己都知道的事实。当 Cassandra 出现在多节点配置中时，每个节点从“种子”节点列表中寻找加入集群的帮助。没有种子，什么都不能加入集群；然而，只有非种子会在加入时从种子引导数据，所以让所有东西都成为种子并不是一个好主意。而且种子布局需要进行拓扑排序:如果 A 有 B 和 C 做种子，B 有 A 和 C，C 有 A 和 B，和没有种子的情况一样。如果两个实例知道它们在某种程度上是特殊的，我们可以使用粒度匹配来专门针对它们。

## 矿柱和矿井

Salt“支柱”是存储在主服务器上的集中式配置数据库。Minions 在初始化时制作本地副本，它们的缓存可以用`salt minion-name saltutil.refresh_pillar`更新。支柱可以基于名称、粒度或其他标准定位节点，通常用于存储配置。我们有很多配置，其中大部分对于所有节点都是相同的，所以使用柱子是很自然的。

### [T1】SRV/salt/pillar/top . SLS](#srvsaltpillartopsls)

就像 Salt 本身的`top.sls`一样，支柱`top.sls`定义了新爪牙的*高状态*或默认状态。首先，我们声明我们正在添加的支柱属于名字与模式`cassandra-*`匹配的奴才。

```
base:
  'cassandra-*':
    - system-user-ubuntu
    - mine-network-info
    - java
    - cassandra 
```

### SRV/salt/pillar/system-user-Ubuntu . SLS

这里没有什么特别的，只是一个用户，所以我们可以 ssh 和戳的东西。用户的私钥在云提供商配置中定义。

```
system:
  user: ubuntu
  home: /home/ubuntu 
```

### [T1】SRV/salt/pillar/mine-network-info . SLS](#srvsaltpillarminenetworkinfosls)

Salt“mine”是另一个集中的数据库，这个数据库存储谷物信息，因此爪牙可以从主人那里检索关于其他爪牙的事实，而不是处理点对点的通信。Minions 使用一个`mine_functions`支柱(或者 salt-minion 配置，但是我们坚持使用这个支柱)来决定是否存储以及存储什么。对于 Cassandra 节点，我们想要内部网络配置和公共 DNS 名称，每个节点必须通过询问 AWS 它在哪里来获得后者。

```
mine_functions:
  network.interfaces: [eth0]
  network.ip_addrs: [eth0]
  # ask amazon's network config what we're public as
  public_dns:
    - mine_function: cmd.run
    - 'curl -s http://169.254.169.254/latest/meta-data/public-hostname' 
```

### [T1】SRV/salt/pillar/Java . SLS](#srvsaltpillarjavasls)

Cassandra 要求安装 Java 8([对 Java 9 的预期支持变成了对 Java 11 的预期支持](https://issues.apache.org/jira/browse/CASSANDRA-9608)，并在 Cassandra 4 中到期)。这个支柱相应地建立了[官方 Java 公式](https://github.com/saltstack-formulas/sun-java-formula)。

```
java:
  # vitals
  release: '8'
  major: '0'
  minor: '202'
  development: false

  # tarball
  prefix: /usr/share/java # unpack here
  version_name: jdk1.8.0_202 # root directory name
  source_url: https://download.oracle.com/otn-pub/java/jdk/8u202-b08/1961070e4c9b4e26a04e7f5a083f551e/server-jre-8u202-linux-x64.tar.gz
  #source_hash: sha256=9efb1493fcf636e39c94f47bacf4f4324821df2d3aeea2dc3ea1bdc86428cb82
  source_hash: sha256=61292e9d9ef84d9702f0e30f57b208e8fbd9a272d87cd530aece4f5213c98e4e
  dl_opts: -b oraclelicense=accept-securebackup-cookie -L 
```

### [T1】SRV/salt/pillar/Cassandra . SLS](#srvsaltpillarcassandrasls)

最后，Cassandra 支柱定义了集群中所有节点共有的属性。我的升级计划是在 2.2.12 上启动一切，切换中心支柱定义，然后在升级过程中通过刷新支柱为每个 minion 提供新的版本号。

```
cassandra:
  version: '2.2.12'
  cluster_name: 'Test Cluster'
  authenticator: 'AllowAllAuthenticator'
  endpoint_snitch: 'Ec2Snitch'
  twcs_jar:
    '2.2.12': 'TimeWindowCompactionStrategy-2.2.5.jar'
    '3.0.8': 'TimeWindowCompactionStrategy-3.0.0.jar' 
```

`twcs_jar`字典解释了我不使用官方公式的原因之一:我们使用的是[TimeWindowCompactionStrategy](http://thelastpickle.com/blog/2016/12/08/TWCS-part1.html)。从 3.0.8 或 3.8 开始，TWCS 被集成到 Cassandra 中，但对于早期版本，它必须单独编译和安装。TWCS 的预集成版本也有不同的包名(`com.jeffjirsa`而不是`org.apache`)。3.0.8 是共同点，内置了`org.apache` TWCS，但也是`com.jeffjirsa` TWCS 的有效编译目标。升级到 3.0.8 后，我将能够在继续之前通过`ALTER TABLE`应用`org.apache`版本。

有了提供商、配置文件、地图文件和 pillar 设置，我们现在就可以启动一个 Ubuntu 虚拟机的准系统集群，并从盐矿中检索集中存储的网络信息:

```
sudo salt-cloud -m cassandra-test.map

sudo salt 'cassandra-1' 'mine.get' '*' 'public_dns' 
```

我们不能做太多别的事情，因为我们还没有在节点上安装任何东西，但这是一个进步！

## 卡珊德拉状态

状态定义包括 Cassandra 节点*为了成为集群的一部分而需要*具备的一切:已安装的二进制文件、`cassandra`组和用户、配置文件、数据目录和正在运行的 SystemD 单元。这个定义本身有点像 YAML 和金贾的大毒蛇:

### SRV/salt/Cassandra/defaults . YAML

首先，有一个非常普通的 YAML 文件，带有一些默认值。这些可以很容易地在我们上面建立的支柱中(或者支柱配置可以都在这个文件中)；主要区别似乎在于您是想通过`saltutil.refresh_pillar`来传播变更，还是通过直接或通过 highstate(重新)应用 Cassandra 状态。这肯定比现在需要的更复杂，但是考虑到这是我的第一个主要的 SaltStack 项目，我还不知道应该站在哪一边，或者是否最终有必要将默认文件与 pillar 配置结合起来。

```
cassandra:
  dc: dc1
  rack: rack1 
```

### SRV/salt/Cassandra/map . jinja

地图模板加载默认文件并将它们与柱子合并，用我们设置的所有 Cassandra 参数创建一个`server`字典。

```
{% import_yaml "cassandra/defaults.yaml" as default_settings %}

{% set server = salt['pillar.get']('cassandra', default=default_settings.cassandra, merge=True) %} 
```

### SRV/salt/Cassandra/init . SLS

最后，Cassandra state entry point init . SLS 是另一个 Jinja 模板，碰巧看起来很像 YAML 文件，并呈现 YAML 文件，这对于 SaltStack 来说已经足够好了。这里需要 Jinja，因为来自`server`字典的值，如服务器版本或 TWCS JAR 文件名，需要在应用状态时进行插值。

当卡珊德拉状态应用于一个新的宠臣时:

1.  `wget`将被安装
2.  一个`CASSANDRA_VERSION`环境变量将被设置为支柱中定义的值
3.  将创建名为`cassandra`的用户和群组
4.  一旦满足以上三个条件，名为`install.sh`的脚本将下载并提取 Cassandra 本身
5.  名为`cassandra.yaml`的节点配置文件将从 Jinja 模板生成并安装到`/etc/cassandra`
6.  如果需要，TWCS jar 将被添加到 Cassandra lib 目录中
7.  目录`/var/lib/cassandra`将被创建并提供给`cassandra`用户
8.  一旦所有先决条件就绪，Cassandra 的 SystemD 单元将被安装并启动

```
{% from "cassandra/map.jinja" import server with context %}

wget:
  pkg.installed

cassandra:
  environ.setenv:
    - name: CASSANDRA_VERSION
    - value: {{ server.version }}

  cmd.script:
    - require:
      - pkg: wget
      - user: cassandra
      - environ: CASSANDRA_VERSION
    - source: salt://cassandra/files/install.sh
    - user: root
    - cwd: ~

  group.present: []

  user.present:
    - require:
      - group: cassandra
    - gid_from_name: True
    - createhome: False

  service.running:
    - enable: True
    - require:
      - file: /etc/cassandra/cassandra.yaml
      - file: /etc/systemd/system/cassandra.service
{%- if server.twcs_jar[server.version] %}
      - file: /opt/cassandra/lib/{{ server.twcs_jar[server.version] }}
{%- endif %}

# Main configuration
/etc/cassandra/cassandra.yaml:
  file.managed:
    - source: salt://cassandra/files/{{ server.version }}/cassandra.yaml
    - template: jinja
    - makedirs: True
    - user: cassandra
    - group: cassandra
    - mode: 644

# Load TWCS jar if necessary
{%- if server.twcs_jar[server.version] %}
/opt/cassandra/lib/{{ server.twcs_jar[server.version] }}:
  file.managed:
    - require:
      - user: cassandra
      - group: cassandra
    - source: salt://cassandra/files/{{ server.version }}/{{ server.twcs_jar[server.version] }}
    - user: cassandra
    - group: cassandra
    - mode: 644
{%- endif %}

# Data directory
/var/lib/cassandra:
  file.directory:
    - user: cassandra
    - group: cassandra
    - mode: 755

# SystemD unit
/etc/systemd/system/cassandra.service:
  file.managed:
    - source: salt://cassandra/files/cassandra.service
    - user: root
    - group: root
    - mode: 644 
```

### SRV/salt/Cassandra/files/install . sh

这个脚本下载并提取 Cassandra 的目标版本，并将符号链接`/opt/cassandra`指向它。如果目标版本已经存在，它只更新符号链接，因为其他的都已经设置好了。

```
#!/bin/bash

update_symlink() {
  rm /opt/cassandra
  ln -s "/opt/apache-cassandra-$CASSANDRA_VERSION" /opt/cassandra

  echo "Updated symlink"
}

# already installed?
if [-d "/opt/apache-cassandra-$CASSANDRA_VERSION"]; then
  echo "Cassandra $CASSANDRA_VERSION is already installed!"

  update_symlink

  exit 0
fi

# download and extract
wget "https://archive.apache.org/dist/cassandra/$CASSANDRA_VERSION/apache-cassandra-$CASSANDRA_VERSION-bin.tar.gz"
tar xf "apache-cassandra-$CASSANDRA_VERSION-bin.tar.gz"
rm "apache-cassandra-$CASSANDRA_VERSION-bin.tar.gz"

# install to /opt and link /opt/cassandra
mv "apache-cassandra-$CASSANDRA_VERSION" /opt
update_symlink

# create log directory
mkdir -p /opt/cassandra/logs

# set ownership
chown -R cassandra:cassandra "/opt/apache-cassandra-$CASSANDRA_VERSION"
chown cassandra:cassandra /opt/cassandra 
```

使用“纯”Salt(通过使用`server`字典将`install.sh`呈现为 Jinja 模板，可以消除环境变量),或许可以完成大部分工作，至少是符号链接杂耍和目录管理，但是脚本完成了我想要它做的事情，并且它已经是幂等的和集中管理的了。

### SRV/salt/Cassandra/files/Cassandra . service

这是一个基本的 SystemD 单元，定制了一些系统限制以给 Cassandra 足够的运行空间。它会启动在/opt/cassandra 找到的 Cassandra 可执行文件，因此在升级过程中符号链接发生变化后，要恢复操作，只需重新启动该服务。

```
[Unit]
Description=Apache Cassandra database server
Documentation=http://cassandra.apache.org
Requires=network.target remote-fs.target
After=network.target remote-fs.target

[Service]
Type=forking
User=cassandra
Group=cassandra
ExecStart=/opt/cassandra/bin/cassandra -Dcassandra.config=file:///etc/cassandra/cassandra.yaml
LimitNOFILE=100000
LimitNPROC=32768
LimitMEMLOCK=infinity
LimitAS=infinity

[Install]
WantedBy=multi-user.target 
```

### SRV/salt/Cassandra/files/2 . 2 . 12/Cassandra . YAML

完整的`cassandra.yaml`是巨大的，所以我不会在这里完全复制它。有趣的部分是 Salt 自动插值的地方。像卡珊德拉状态一样，这实际上是一个 Jinja 模板，*呈现给*一个 YAML 文件。

首先，我们从盐矿获取一个对应于`cassandra-seed`爪牙的内部 IP 地址列表，并构建一个`known_seeds`列表。

```
{%- from 'cassandra/map.jinja' import server with context -%}
{% set known_seeds = [] %}
{% for minion, ip_array in salt['mine.get']('cassandra-seed:true', 'network.ip_addrs', 'grain').items() if ip_array is not sameas false and known_seeds|length < 2 %}
{% for ip in ip_array %}
{% do known_seeds.append(ip) %}
{% endfor %}
{% endfor %} 
```

这成为节点在尝试加入集群时寻找的种子列表。

```
seed_provider:
    - class_name: org.apache.cassandra.locator.SimpleSeedProvider
      parameters:
          - seeds: "{{ known_seeds|unique|join(',') }}" 
```

监听和广播地址是按节点配置的。由于我们的网络配置需要，广播地址有点特殊:每个节点都必须从盐矿中获取其公共 dns 名称。与自定义颗粒或在渲染时捕获运行 Salt 模块的输出相比，这可能有点过于复杂，但它确实存在，并且可以工作，在这一点上对它进行处理并不是很浪费时间。

```
listen_address: {{ grains['fqdn'] }}
broadcast_address: {{ salt['mine.get'](grains['id'], 'public_dns').items()[0][1] }}
rpc_address: {{ grains['fqdn'] }}
broadcast_rpc_address: {{ salt['mine.get'](grains['id'], 'public_dns').items()[0][1] }} 
```

集群名称和其他中心设置是从 pillar+defaults `server`字典中插入的。

```
cluster_name: "{{ server.cluster_name }}"
...
authenticator: "{{ server.authenticator }}"
...
endpoint_snitch: "{{ server.endpoint_snitch }}" 
```

对 Cassandra 3.0.8 配置的更改是相同的。

### SRV/salt/Cassandra/files/2 . 2 . 12/TimeWindowCompactionStrategy-2 . 2 . 5 . jar

参见上的这篇帖子，了解建造 TWCS 罐子的方法。

## 高状态

最后，Salt highstate 需要确保我们的`cassandra-*`节点应用了 Java 和 Cassandra 状态。然而，由于 Salt-Cloud minions 是经过配置的，所以我们必须确保默认的`salt.minion`状态被排除在我们的 Cassandra 节点之外，否则高状态将会破坏特定于云的配置。

### srv/salt/top.sls 变化

```
base:
  'not cassandra-*':
    - match: compound
    - salt.minion
  'cassandra-*':
    - sun-java
    - sun-java.env
    - cassandra 
```

## 启动！

用`-c`将 Salt 配置目录设置为`etc`，用`-m` :
传入映射文件

```
sudo salt-cloud -c etc -m cassandra-test.map 
```

要清理:

```
sudo salt-cloud -d cassandra-1 cassandra-2 cassandra-3 
```