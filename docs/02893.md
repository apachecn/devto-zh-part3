# docker swarm mysql 服务，扩展时无法工作

> 原文：<https://dev.to/taragrg6/docker-swarm-mysql-service-when-scaling-not-working-2l8p>

我只是创建了一个群节点(主节点)。写了一个 docker-compose 与多个服务其中之一是 mysql。现在，当我扩展 mysql 容器时，我遇到了连接问题。mysql 有一个卷，定义为

```
volumes:
  - ./mysql:/var/lib/mysql 
```