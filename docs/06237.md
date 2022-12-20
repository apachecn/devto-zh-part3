# 移除不必要的 Docker 资源。

> 原文：<https://dev.to/sot528/remove-unnecessary-docker-resources-24bh>

# 问题

长时间使用 Docker for Mac 会占用大量存储空间。

```
sot-mbp:clean_docker_resources sot528$ docker system df TYPE                TOTAL               ACTIVE              SIZE                RECLAIMABLE
Images              152                 2                   37.57GB             35.79GB (95%)
Containers          4                   0                   35.8MB              35.8MB (100%)
Local Volumes       4                   0                   7.118MB             7.118MB (100%)
Build Cache         0                   0                   0B                  0B 
```

# 解

```
docker system prune 
```

然后，大小大约是 30GB。
并删除没有最近 10 张图像的旧图像。

```
docker rmi -f $(docker images | sed -n '10,$p') 
```

# 结果

很好！

```
sot-mbp:clean_docker_resources sot528$ docker system df TYPE                TOTAL               ACTIVE              SIZE                RECLAIMABLE
Images              7                   0                   5.337GB             5.337GB (100%)
Containers          0                   0                   0B                  0B
Local Volumes       4                   0                   7.118MB             7.118MB (100%)
Build Cache         0                   0                   0B                  0B 
```

# 环境

*   马科斯莫哈韦 10.14.3(18D109)
*   Docker 版本 18.09.2，内部版本号 6247962