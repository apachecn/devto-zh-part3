# 从命令行运行 AzureDevops 版本

> 原文：<https://dev.to/melezhik/run-azuredevops-builds-from-command-line-58np>

如果你更喜欢命令行，AzureDevops 附带了 [vsts cli](https://docs.microsoft.com/en-us/cli/vsts/overview?view=vsts-cli-latest) 允许你通过命令行执行许多与 vsts 相关的操作。然而，您仍然需要额外的 bash 编码来使它平稳运行。何必呢？改为选择 [vsts-build](https://sparrowhub.org/info/vsts-build) ！

```
$ cat .sparrowdo/run-build.pl6
task-run "run build", "vsts-build", %(
  definition => 'Backend01'
);

$ sparrowdo --no_sudo --local_mode --sparrowfile=.sparrowdo/run-build.pl6
user: root
found ini file: /root/sparrowdo.ini
index update: False
running sparrow tasks on 127.0.0.1 ...
target OS is - centos7
push [task] run build [plg] vsts-build OK
SPL file /opt/sparrow/sparrow.list is empty
set up task box file - /home/root/.sparrowdo//opt/sparrow/task-box.json - OK
sparrow root: [/opt/sparrow]
plugin /opt/sparrow/plugins/public/vsts-build/ installed locally, nothing to do here ...
running task box from /opt/sparrow/sparrow-cache/task-box.json ...
2018-12-12 13:02:44 : [task] run build [plg] vsts-build [hook]
2018-12-12 13:02:44 : [task] run build [plg] vsts-build [path] /modules/run/
vsts build queue --definition-name Backend01 --branch refs/heads/poc/storage-account
    ID       Number  Status      Result      Definition ID  Definition Name    Source Branch        Queued Time                 Reason
------  -----------  ----------  --------  ---------------  -----------------  -------------------  --------------------------  --------
219604  2.01812e+07  notStarted                       1240  Backend01           feature/storage-account  2018-12-12 13:02:45.551673  manual
ok      scenario succeeded
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode