# Ssh-bulk-check -通过 Ssh 对节点组进行超级灵活的检查。

> 原文：<https://dev.to/melezhik/ssh-bulk-check-super-flexible-check-of-group-of-nodes-by-ssh-42ff>

有时，我们需要监控一组主机是否处于正确的状态。写作测试需要时间。像 goss 或 inspec 这样的现有工具很好，但是它们缺乏灵活性。总有一些东西没有被提供的 API 覆盖。

新的 [Sparrow6](https://github.com/melezhik/sparrow-plugins/tree/master/Sparrow6) 插件 [ssh-bulk-check](https://github.com/melezhik/sparrow-plugins/tree/master/ssh-bulk-check) 允许编写检查脚本，以不受限制的方式验证一组 ssh 主机的状态。

它非常灵活和全面，因为依赖于能够覆盖几乎所有用例的 [Sparrow6 任务检查 DSL](https://github.com/melezhik/Sparrow6/blob/master/documentation/taskchecks.md) 和普通 Bash 脚本。

让我展示一下它是如何工作的。

# 安装 Sparrow6

`zef install https://github.com/melezhik/Sparrow6.git`

# 创建 Sparrow6 存储库

`export SP6_REPO=file:///tmp/repo`
`mkdir -p /tmp/repo`
`s6 --repo_init /tmp/repo`
`git clone https://github.com/melezhik/sparrow-plugins.git`
T4】

# Ssh 主机状态

比方说，我们有一组节点，其中每个节点都必须具有相同的状态:

*   目录`/var/data`存在

*   `/tmp`的大小不超过 1GB

*   nginx 的运行不超过 2 个工人。

这是一个很有想象力的例子，但你可能已经明白了。

让我们首先创建运行*测试*命令 shell 脚本:

# Cmd.sh

`mkdir files`

`nano files/cmd.sh`

```
echo '==='

echo "check /var/data dir"
  ls -d /var/data && echo "/var/data is a directory"
echo "end check"

echo '==='

echo "check /tmp/ dir size"
  sudo du -sh /tmp/
echo "end check"

echo '==='

echo "check if nginx is alive"
  ps uax| grep nginx| grep -v grep
echo "end check"

echo '===' 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们定义检查规则，即*分析`cmd.sh`脚本的*输出。我在范围表达式中使用 Sparrow6 TaskCheck DSL，因此为了区分不同的检查:

# 状态检查

```
between: { 'check /var/data dir' } { end \s+ check }

  /var/data is a directory

end:

note: ===

between: { '/tmp/ dir size' } { end \s+ check }

  regexp: ^^ \d+(\w+) \s+ '/tmp/'

  generator: <<HERE
  !perl

    if (@{matched()}){
      my $order = capture()->[0];
      print "assert: ", ( $order eq 'G' ? 0 : 1 ), " the size of /tmp dir is less
      then 1 GB\n";
    }

  HERE

end:

note: ===

between: { 'check if nginx is alive' } { end \s+ check }

  /usr/sbin/nginx -g daemon on; master_process on;

  regexp: ^^ 'www-data' \s+ .* \s+ worker \s+ process $$

  generator: <<HERE
  !perl

    if (my $cnt = @{matched()}){
      print "assert: ", ( $cnt <= 2 ? 1 : 0  ), " no more 2 nginx worker
      launched\n";
    }

  HERE

end: 
```

Enter fullscreen mode Exit fullscreen mode

我们几乎设置好了，现在让我们对 ssh 主机运行所有检查，我们将在`--localhost`模式下使用 Sparrowdo 任务运行器，因为我们使用`ssh/sshpass`实用程序从本地主机运行测试:

# Sparrowdo 场景

sparrowfile

```
#!perl6

task-run "check my hosts", "ssh-bulk-check", %(
  cmd => "files/cmd.sh",
  state => "files/state.check",
  hosts => [ "192.168.0.1" ],
); 
```

Enter fullscreen mode Exit fullscreen mode

要运行测试，只需说:

`sparrowdo --localhost`

下面是结果:

```
20:01:46 04/29/2019 [check my hosts] check host [192.168.0.1]
20:01:46 04/29/2019 [check my hosts] ===
20:01:46 04/29/2019 [check my hosts] check /var/data dir
20:01:46 04/29/2019 [check my hosts] /var/data
20:01:46 04/29/2019 [check my hosts] /var/data is a directory
20:01:46 04/29/2019 [check my hosts] end check
20:01:46 04/29/2019 [check my hosts] ===
20:01:46 04/29/2019 [check my hosts] check /tmp/ dir size
20:01:46 04/29/2019 [check my hosts] 40K        /tmp/
20:01:46 04/29/2019 [check my hosts] end check
20:01:46 04/29/2019 [check my hosts] ===
20:01:46 04/29/2019 [check my hosts] check if nginx is alive
20:01:46 04/29/2019 [check my hosts] root      1243  0.0  0.0 140628  1500 ?        Ss   18:32   0:00 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
20:01:46 04/29/2019 [check my hosts] www-data  1244  0.0  0.0 143300  6264 ?        S    18:32   0:00 nginx: worker process
20:01:46 04/29/2019 [check my hosts] www-data  1245  0.0  0.0 143300  6264 ?        S    18:32   0:00 nginx: worker process
20:01:46 04/29/2019 [check my hosts] end check
20:01:46 04/29/2019 [check my hosts] ===
20:01:46 04/29/2019 [check my hosts] end check host [192.168.0.1]
[task check] ====================================================
[task check] check results
[task check] ====================================================
[task check] stdout match (r) </var/data is a directory> True
[task check] ===
[task check] stdout match (r) <^^ \d+(\w+) \s+ '/tmp/'> True
[task check] <the size of /tmp dir is less then 1 GB> True
[task check] ===
[task check] stdout match (r) </usr/sbin/nginx -g daemon on; master_process on;> True
[task check] stdout match (r) <^^ 'www-data' \s+ .* \s+ worker \s+ process $$> True
[task check] <no more 2 nginx worker launched> True 
```

Enter fullscreen mode Exit fullscreen mode

对于这个例子，我只使用了一个主机- `192.168.0.1`，但是我们可以根据需要添加任意多的主机，插件会在一次事务中检查所有的主机。

用这个插件编写几乎任何检查都非常容易。请分享你的用例，我很乐意帮助你如何使用`ssh-bulk-check`

* * *