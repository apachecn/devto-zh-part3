# -=伊甸园美剧 http://sfile . ydy . com =-荣誉出品本字幕仅供学习交流，严禁用于商业途径

> 原文:# t0]https://dev . to/setevy/redis-replication-part-3-redis-py-and-work-with-redis-sentinel from-python-MB 9

[![](img/f6d45197b0b18a1a39702233dbf5afe7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rqSEDlVy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2018/05/redislogo.png) 仍然是关于 Redis 复制和 Redis Sentinel 的主题——使用 Python 的 [`redis-py`](https://github.com/andymccurdy/redis-py) 库的几个例子。

以前的系列文章:

1.  Redis:复制，第 1 部分-概述。复制与分片。哨兵 vs 集群。Redis 拓扑
2.  [Redis:复制，第 2 部分-主从复制，以及 Redis Sentinel](https://rtfm.co.ua/en/redis-replication-part-2-master-slave-replication-and-redis-sentinel/)

Python 的所有 Redis 客户端都可以在这里找到——[redis.io/clients](https://redis.io/clients#python)。

起初，会使用 [`redis-py`](https://github.com/andymccurdy/redis-py) 而不用哨兵，只是为了检查它的工作情况。

然后-将旋转哨兵，并检查主人和奴隶的发现。

前一篇文章的[中已经配置了一个工作环境，即 Redis 复制和 Redis Sentinel 这里只是 Python 的例子。](https://dev.to/setevoy/redis-replication-part-2-master-slave-replication-and-redis-sentinel-1hoe)

### `redis-py`和惯用语

在 Debian 上安装`redis-py`:

```
root@redis-0:/home/admin# apt install python-redis 
```

另外，`pip`也可以使用，但我更喜欢使用普通的存储库。

检查一下:

```
root@redis-0:/home/admin# python
Python 2.7.13 (default, Sep 26 2018, 18:42:22)
[GCC 6.3.0 20170516] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import redis
>>> r = redis.Redis(host='localhost', port=6379, db=0)
>>> r.get('test')
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
File "/usr/lib/python2.7/dist-packages/redis/client.py", line 880, in get
return self.execute_command('GET', name)
File "/usr/lib/python2.7/dist-packages/redis/client.py", line 573, in execute_command
return self.parse_response(connection, command_name, **options)
File "/usr/lib/python2.7/dist-packages/redis/client.py", line 585, in parse_response
response = connection.read_response()
File "/usr/lib/python2.7/dist-packages/redis/connection.py", line 582, in read_response
raise response
redis.exceptions.ResponseError: NOAUTH Authentication required. 
```

好吧，管用。

在文档中查找授权:

```
>>> help(redis.Redis) 
```

添加`password` :

```
>>> r = redis.Redis(host='localhost', password='foobared', port=6379, db=0)
>>> r.get('test')
'test' 
```

好吧。

### `redis-py`和【雷迪斯哨兵】

如果 Sentinel 实例尚未启动，现在运行它们，让我们通过 Redis Sentinel 集群客户端尝试 Redis。

Sentinel 在主机上的配置`/etc/redis/sentinel.conf`现在是:

```
sentinel monitor redis-test 52.58.69.184 6379 2
sentinel down-after-milliseconds redis-test 6001
sentinel failover-timeout redis-test 60000
bind 0.0.0.0
sentinel auth-pass redis-test foobared 
```

在 Sentinel 实例启动之后，它将更新配置，所以现在在 *redis-1* (这里是第一个从站)上的配置看起来像:

```
admin@redis-1:~$ cat /etc/redis/sentinel.conf
sentinel myid e3e62e6577aa975f93346dad3d4f8e25833fd8f1
sentinel monitor redis-test 52.29.101.118 6379 2
sentinel down-after-milliseconds redis-test 6001
bind 0.0.0.0
sentinel failover-timeout redis-test 60000
# Generated by CONFIG REWRITE
port 26379
dir "/home/admin"
sentinel auth-pass redis-test foobared
sentinel config-epoch redis-test 1
sentinel leader-epoch redis-test 1
sentinel known-slave redis-test 52.58.69.184 6379
sentinel known-slave redis-test 35.159.18.26 6379
sentinel known-sentinel redis-test 172.31.46.202 26379 e0fe655c59aa3cc32eab1c0858c52418700abe79
sentinel known-sentinel redis-test 172.31.41.39 26379 07a450af0d2f178410b78ee0f5ae99ce1cd0ac62
sentinel current-epoch 1 
```

检查哨点群集状态:

```
root@redis-0:/home/admin# redis-cli -p 26379 info sentinel
Sentinel
sentinel_masters:1
sentinel_tilt:0
sentinel_running_scripts:0
sentinel_scripts_queue_length:0
sentinel_simulate_failure_flags:0
master0:name=redis-test,status=ok,address=52.58.69.184:6379,slaves=2,sentinels=3 
```

并使用`redis-py` :

```
>>> from redis.sentinel import Sentinel
>>> sentinel = Sentinel([('localhost', 26379)], socket_timeout=0.1) 
```

使用`discover_master()`方法:
可以获得硕士 IP

```
>>> sentinel.discover_master('redis-test')
('52.58.69.184', 6379) 
```

还有奴隶:

```
>>> sentinel.discover_slaves('redis-test')
[('52.29.101.118', 6379), ('35.159.18.26', 6379)] 
```

与高手合作使用 [`master_for()`](https://www.pydoc.io/pypi/redis-2.10.5/autoapi/sentinel/index.html#sentinel.Sentinel.master_for) 的方法:

> | master_for(self，service_name，redis_class=，connection_pool_class=，**kwargs)
> 
> |返回“service_name”主服务器的 redis 客户端实例。

```
>>> master = sentinel.master_for('redis-test', socket_timeout=0.1) 
```

但是如果现在给 master 打电话，它会告诉我们没有授权:

```
>>> master.set('test-key', 'test-value')
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
File "/usr/lib/python2.7/dist-packages/redis/client.py", line 1072, in set
return self.execute_command('SET', *pieces)
File "/usr/lib/python2.7/dist-packages/redis/client.py", line 573, in execute_command
return self.parse_response(connection, command_name, **options)
File "/usr/lib/python2.7/dist-packages/redis/client.py", line 585, in parse_response
response = connection.read_response()
File "/usr/lib/python2.7/dist-packages/redis/sentinel.py", line 55, in read_response
return super(SentinelManagedConnection, self).read_response()
File "/usr/lib/python2.7/dist-packages/redis/connection.py", line 582, in read_response
raise response
redis.exceptions.ResponseError: NOAUTH Authentication required. 
```

所以在创建`master`对象的过程中——添加`password` :

```
>>> master = sentinel.master_for('redis-test', socket_timeout=0.1, password='foobared') 
```

现在检查:

```
>>> master.set('test-key', 'test-value')
True 
```

还有，你可以得到一个完整的大师配置:

```
>>> master.config_get()
{'appendonly': 'no', ... 'slave-announce-port': '0'} 
```

或者用更易读的观点:

```
>>> for i in master.config_get():
...   print(i)
...
appendonly
requirepass
daemonize
protected-mode
zset-max-ziplist-entries
zset-max-ziplist-value
dir
slave-serve-stale-data
cluster-require-full-coverage
slowlog-log-slower-than
masterauth
rdbchecksum
... 
```

在托盘上–检查我们之前添加的密钥:

```
admin@redis-1:~$ redis-cli -a foobared get test-key
"test-value" 
```

和奴隶一起工作和主人差不多:

```
>>> slave = sentinel.slave_for('redis-test', socket_timeout=0.1, password='foobared')
>>> slave.get('test-key')
'test-value' 
```

### 主变和哨兵故障转移

这时我们来看看高手:

```
>>> sentinel.discover_master('redis-test')
('52.58.69.184', 6379) 
```

最终让我们尝试在 *redis-0* 主机:
上停止 Redis master

```
root@redis-0:/home/admin# systemctl stop redis-server 
```

或者由:

```
root@redis-0:/home/admin# redis-cli -a foobared DEBUG sleep 600 
```

(如果您只是用`kill`终止`redis-server`进程，Sentinel 将再次重启它)。

查看哨兵日志:

```
10633:X 01 Apr 13:46:10.430 # +sdown master redis-test 52.58.69.184 6379
10633:X 01 Apr 13:46:10.486 # +odown master redis-test 52.58.69.184 6379 #quorum 2/2
10633:X 01 Apr 13:46:10.486 # +new-epoch 1
10633:X 01 Apr 13:46:10.486 # +try-failover master redis-test 52.58.69.184 6379
10633:X 01 Apr 13:46:10.488 # +vote-for-leader e3e62e6577aa975f93346dad3d4f8e25833fd8f1 1
10633:X 01 Apr 13:46:10.492 # 07a450af0d2f178410b78ee0f5ae99ce1cd0ac62 voted for e3e62e6577aa975f93346dad3d4f8e25833fd8f1 1
10633:X 01 Apr 13:46:10.492 # e0fe655c59aa3cc32eab1c0858c52418700abe79 voted for e3e62e6577aa975f93346dad3d4f8e25833fd8f1 1
10633:X 01 Apr 13:46:10.559 # +elected-leader master redis-test 52.58.69.184 6379
10633:X 01 Apr 13:46:10.559 # +failover-state-select-slave master redis-test 52.58.69.184 6379
10633:X 01 Apr 13:46:10.611 # +selected-slave slave 52.29.101.118:6379 52.29.101.118 6379 @ redis-test 52.58.69.184 6379
10633:X 01 Apr 13:46:10.612 * +failover-state-send-slaveof-noone slave 52.29.101.118:6379 52.29.101.118 6379 @ redis-test 52.58.69.184 6379
10633:X 01 Apr 13:46:10.664 * +failover-state-wait-promotion slave 52.29.101.118:6379 52.29.101.118 6379 @ redis-test 52.58.69.184 6379
10633:X 01 Apr 13:46:11.498 # +promoted-slave slave 52.29.101.118:6379 52.29.101.118 6379 @ redis-test 52.58.69.184 6379
10633:X 01 Apr 13:46:11.498 # +failover-state-reconf-slaves master redis-test 52.58.69.184 6379
10633:X 01 Apr 13:46:11.557 * +slave-reconf-sent slave 35.159.18.26:6379 35.159.18.26 6379 @ redis-test 52.58.69.184 6379
10633:X 01 Apr 13:46:12.550 * +slave-reconf-inprog slave 35.159.18.26:6379 35.159.18.26 6379 @ redis-test 52.58.69.184 6379
10633:X 01 Apr 13:46:12.550 * +slave-reconf-done slave 35.159.18.26:6379 35.159.18.26 6379 @ redis-test 52.58.69.184 6379
10633:X 01 Apr 13:46:12.632 # -odown master redis-test 52.58.69.184 6379
10633:X 01 Apr 13:46:12.632 # +failover-end master redis-test 52.58.69.184 6379
10633:X 01 Apr 13:46:12.632 # +switch-master redis-test 52.58.69.184 6379 52.29.101.118 6379
10633:X 01 Apr 13:46:12.632 * +slave slave 35.159.18.26:6379 35.159.18.26 6379 @ redis-test 52.29.101.118 6379
10633:X 01 Apr 13:46:12.632 * +slave slave 52.58.69.184:6379 52.58.69.184 6379 @ redis-test 52.29.101.118 6379
10633:X 01 Apr 13:46:18.647 # +sdown slave 52.58.69.184:6379 52.58.69.184 6379 @ redis-test 52.29.101.118 6379 
```

来自 Python 的检查:

```
>>> sentinel.discover_master('redis-test')
('52.29.101.118', 6379) 
```

主人变了——*52.29.101.118*——是 *redis-1* 主机。

从 *redis-1* :
检查状态

```
admin@redis-1:~$ redis-cli -a foobared info replication
Replication
role:master
connected_slaves:1
slave0:ip=35.159.18.26,port=6379,state=online,offset=76847,lag=0
... 
```

检查 *redis-0* 主机上的 Redis 节点状态——它现在必须成为从节点:

```
root@redis-0:/home/admin# redis-cli -a foobared info replication
Replication
role:slave
... 
```

并尝试在 *redis-0* 主机上使用我们之前创建的同一个`master`对象添加一个来自 Python 的新密钥:

```
>>> master.set('test-key2', 'test-value2')
True 
```

检查:

```
admin@redis-2:~$ redis-cli -a foobared get test-key2
"test-value2" 
```

完成了。

### 类似的帖子

*   <small>03/29/2019</small> [Redis:复制，第 2 部分-主从复制，Redis Sentinel](https://dev.to/setevoy/redis-replication-part-2-master-slave-replication-and-redis-sentinel-1hoe) <small>(0)</small>
*   <small>03/29/2019</small> [Redis:复制，第 1 部分–概述。复制与分片。哨兵 vs 集群。Redis 拓扑。](https://dev.to/setevoy/redis-replication-part-1-an-overview-replication-vs-sharding-sentinel-vs-cluster-redis-topology-3ao9) <small>(0)</small>