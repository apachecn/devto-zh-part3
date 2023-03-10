# Keycloak SSO 第 2 部分:设置 Keycloak

> 原文：<https://dev.to/just_insane/keycloak-sso-part-2-setting-up-keycloak-e8p>

在本系列的第二部分中，我们将在两台 CentOS 7 服务器上设置 Keycloak 的独立 ha 部署。

Keycloak 有三种不同的部署类型:独立、独立高可用性和域集群。独立部署是单个服务器，这对于开发或测试环境来说很好，但是对于生产使用来说不是很有用。独立 HA 是一台或多台服务器，它们都可以用来处理身份验证请求。这种方法需要一个共享数据库，并且每个服务器都是手动配置的。在域部署中，有一个称为域控制器的主服务器，以及一个或多个为身份验证请求提供服务的主机控制器。当域控制器上的配置发生变化时，这种模式允许所有主机控制器都拥有更新的配置，从而大大减少了多台服务器的管理开销。

## 安装

硬件要求以及分发目录结构和运行模式信息可以在[https://www . key cloak . org/docs/latest/server _ installation/index . html # installation](https://www.keycloak.org/docs/latest/server_installation/index.html#installation)找到

我使用 Ansible 来部署 Keycloak 的文件夹结构，并确保所有依赖项都设置正确。这让我可以轻松地更新并确保我所有的 Keycloak 服务器都部署正确。我的剧本调用了[https://github.com/andrewrothstein/ansible-keycloak](https://github.com/andrewrothstein/ansible-keycloak)，对配置文件做了一些修改。

## 配置

阅读和理解官方文档对于以安全的方式安装 Keycloak 至关重要，我强烈建议您遵循那里的信息并使用我的配置作为指南。

### 运行模式

部署 Keycloak 首先要考虑的是你想用什么操作模式。这主要取决于您的环境，大多数模式的配置是相同的，只是在不同的文件中。我对独立高可用性模式最有经验，所以这就是我们在本系列中将要使用的模式。

该模式的配置在位于`$keycloak_home/standalone/configuration/standalone-ha.xml`的 standalone-ha.xml 配置文件中完成。需要在独立高可用性集群设置中的所有服务器上编辑该文件。

### 关系数据库设置

我们要做的下一件事是设置 Keycloak 来使用数据库，因为我们将创建一个包含多个服务器的部署，我们将需要一个共享数据库。设置一个可集中访问的数据库超出了本文的范围。只需知道我们将使用托管在两个 Keycloak 服务器之外的 PostgreSQL 数据库。

#### 下载 JDBC 驱动程序

为 Keycloak 设置数据库的第一步是为数据库下载一个 JDBC 驱动程序。这允许 Java 与数据库交互。您通常可以在您选择的数据库的主站点上找到这些。比如 PostgreSQL 的 JDBC 驱动可以在这里找到:[https://jdbc.postgresql.org/download.html](https://jdbc.postgresql.org/download.html)

#### 打包驱动 JAR 并安装

官方文档是一个很好的资源，可以帮助你打包这个驱动程序来和 Keycloak 一起使用，没有必要重复劳动。可以在这里找到:[https://www . key cloak . org/docs/latest/server _ installation/index . html # package-the-JDBC-driver](https://www.keycloak.org/docs/latest/server_installation/index.html#package-the-jdbc-driver)

这归结为添加一个文件夹结构，复制。jar 文件，并添加一个。xml 文件如下:

```
<?xml version="1.0" ?>
<module xmlns="urn:jboss:module:1.3" name="org.postgresql">

    <resources>
        <resource-root path="postgresql-9.4.1212.jar"/> <!-- update the filename to match your PostgreSQL JDBC driver file name -->
    </resources>

    <dependencies>
        <module name="javax.api"/>
        <module name="javax.transaction.api"/>
    </dependencies>
</module> 
```

确保用正确的文件名更新路径。

#### 声明并加载驱动程序

这一部分以及修改数据源更高级一些，所以我将在这里更详细地介绍它们，但是文档仍然很有帮助。

我们将查看我们之前处理的 standalone-ha.xml 文件，特别是`drivers` XML 块。在这个模块中，我们将添加一个额外的驱动程序。我们主要可以复制 h2 驱动程序的现有格式，并为 PostgreSQL 更新信息。下面是`standalone-ha.xml`
中一个驱动程序的例子

```
<drivers>
    <driver name="h2" module="com.h2database.h2">
        <xa-datasource-class>org.h2.jdbcx.JdbcDataSource</xa-datasource-class>
    </driver>
    <driver name="postgresql" module="org.postgresql">
        <xa-datasource-class>org.postgresql.xa.PGXADataSource</xa-datasource-class>
    </driver>
</drivers> 
```

正如我们所看到的，驱动程序的声明与预先配置的 H2 数据库驱动程序几乎相同。

#### 修改 Keycloak 数据源

下面我们将看到一个 PostgreSQL 数据源配置的例子。

```
<datasource jndi-name="java:jboss/datasources/KeycloakDS" pool-name="KeycloakDS" enabled="true" use-java-context="true">
    <connection-url>jdbc:postgresql://$URL:$PORT/$DATABASE</connection-url>
    <driver>postgresql</driver>
    <pool>
        <max-pool-size>20</max-pool-size>
    </pool>
    <security>
        <user-name>$USERNAME</user-name>
        <password>$PASSWORD</password>
    </security>
</datasource> 
```

$ URL = PostgreSQL 服务器的 URL 或 IP 地址

$PORT =连接到 PostgreSQL 数据库的端口

$DATABASE =为 Keycloak

配置的数据库的名称$USERNAME =有权访问上面指定的数据库的用户名

$PASSWORD =上面定义的用户的密码

最后，您应该得到一个类似如下的`datasources`部分:

```
<subsystem xmlns="urn:jboss:domain:datasources:5.0">
    <datasources>
        <datasource jndi-name="java:jboss/datasources/KeycloakDS" pool-name="KeycloakDS" enabled="true" use-java-context="true">
            <connection-url>jdbc:postgresql://$URL:$PORT/$DATABASE</connection-url>
            <driver>postgresql</driver>
            <pool>
                <max-pool-size>20</max-pool-size>
            </pool>
            <security>
                <user-name>$USERNAME</user-name>
                <password>$PASSWORD</password>
            </security>
        </datasource>
        <drivers>
            <driver name="h2" module="com.h2database.h2">
                <xa-datasource-class>org.h2.jdbcx.JdbcDataSource</xa-datasource-class>
            </driver>
            <driver name="postgresql" module="org.postgresql">
                <xa-datasource-class>org.postgresql.xa.PGXADataSource</xa-datasource-class>
            </driver>
        </drivers>
    </datasources>
</subsystem> 
```

## 聚类

上面的步骤将得到一个共享数据库的基本设置，但是为了正确地集群 Keycloak，还需要完成一些步骤。

keycloak 文档中的相关章节如下:

1.  [选择一种运行模式](https://www.keycloak.org/docs/latest/server_installation/index.html#_operating-mode)

2.  [配置共享外部数据库](https://www.keycloak.org/docs/latest/server_installation/index.html#_database)

3.  [设置负载平衡器](https://www.keycloak.org/docs/latest/server_installation/index.html#_setting-up-a-load-balancer-or-proxy)

4.  [提供支持 IP 组播的专用网络](https://www.keycloak.org/docs/latest/server_installation/index.html#multicast-network-setup)

我们已经完成了设置集群中的步骤 1 和 2。接下来的两个操作需要一些额外的设置，其中一部分在这里是可维修的，但是在上面的链接中有更详细的介绍。

### 设置负载均衡器

#### 识别客户端 IP 地址

由于各种原因，Keycloak 能够识别客户端 IP 地址是非常重要的，这将在文档中进一步解释。我们将在这里讨论`standalone-ha.xml`中必须做出的更改。

您需要将`urn:jboss:domain:undertow:6.0`块配置成如下所示:

```
<subsystem xmlns="urn:jboss:domain:undertow:6.0">
   <buffer-cache name="default"/>
   <server name="default-server">
      <ajp-listener name="ajp" socket-binding="ajp"/>
      <http-listener name="default" socket-binding="http" redirect-socket="https" proxy-address-forwarding="true"/>
      ...
   </server>
   ...
</subsystem> 
```

#### 启用 HTTPS 反向代理

如果您在 Keycloak 前面有一个反向代理来处理您的 SSL 连接和终止，那么您需要进行以下更改:

在`urn:jboss:domain:undertow:6.0`块中(如上配置),将`redirect-socket`从 https 更改为我们将定义的套接字绑定。

```
<subsystem xmlns="urn:jboss:domain:undertow:6.0">
    ...
    <http-listener name="default" socket-binding="http"
        proxy-address-forwarding="true" redirect-socket="proxy-https"/>
    ...
</subsystem> 
```

我们现在需要添加一个新的套接字绑定到`socket-binding-group`元素，如下:

```
<socket-binding-group name="standard-sockets" default-interface="public"
    port-offset="${jboss.socket.binding.port-offset:0}">
    ...
    <socket-binding name="proxy-https" port="443"/>
    ...
</socket-binding-group> 
```

## 测试集群

一旦在您所有的 Keycloak 服务器上完成了更改，我们就可以以任何顺序手动启动 Keycloak 服务器。这样做的命令是

`bin/standalone.sh --server-config=standalone-ha.xml`

来自 Keycloak home 目录。如果 Keycloak 服务器连接到同一个外部数据库，它们将自动配置自己，您可以使用负载平衡器或反向代理连接到任一服务器来执行身份验证操作。

### 防火墙

确保您已经正确配置了防火墙，默认情况下，Keycloak 监听端口 8080 和 8443。根据您的配置，可能需要打开其他端口。

## 开机运行

假设您的测试已经通过，并且您可以直接和通过您的负载平衡器访问您的两个 Keycloak 服务器，那么您就可以设置一个 systemd 单元文件并让 Keycloak 在引导时启动了。

下面是我正在使用的 systemd 单元文件的副本，它放在`/etc/systemd/system/keycloak.service` :

```
[Unit]
Description=Keycloak Identity Provider
After=syslog.target network.target
Before=httpd.service

[Service]
Environment=LAUNCH_JBOSS_IN_BACKGROUND=1 JAVA_HOME=/usr/local/java
User=keycloak
Group=keycloak
LimitNOFILE=102642
PIDFile=/var/run/keycloak/keycloak.pid
ExecStart=/usr/local/keycloak/bin/standalone.sh --server-config=standalone-ha.xml
#StandardOutput=null

[Install]
WantedBy=multi-user.target 
```

完成此步骤后，您可以通过在所有 Keycloak 服务器上运行以下命令来启动和启用该服务:

`systemctl enable keycloak`

`systemctl start keycloak`