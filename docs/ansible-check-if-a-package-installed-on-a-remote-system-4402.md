# Ansible:检查软件包是否安装在远程系统上

> 原文：<https://dev.to/setevoy/ansible-check-if-a-package-installed-on-a-remote-system-4402>

[![](img/15d8789c54059c05cb2aa766622e43a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bAqYdRz_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2015/03/ansible_logo_black_square-e1425716693260.png) 有一个自写的`letsencrypt`角色(见 [Prometheus: RTFM 用 ansi ble-Grafana、Loki、promtail](https://dev.to/setevoy/prometheus-rtfm-blog-monitoring-set-up-with-ansible--grafana-loki-and-promtail-58i) 帖子设置的博客监控)。

在运行 Let's Encrypt client 以获取新证书之前，需要检查远程主机上是否安装了 NGINX。

让我们使用 [`package_facts`](https://docs.ansible.com/ansible/latest/modules/package_facts_module.html) 模块:

```
...
- name: "Check if NGINX is installed"
  package_facts:
    manager: "auto"
... 
```

并使用`ansible_facts.packages`数组:
添加一个带有`when`的条件检查

```
...
- name: "NGINX test result"
  debug: 
    msg: "NGINX found"
  when: "'nginx' in ansible_facts.packages"

- name: "NGINX test result"
  debug: 
    msg: "NGINX NOT found"
  when: "'nginx' not in ansible_facts.packages" 
```

检查:

```
...
TASK [test : Check if NGINX is installed] ****
ok: [ssh.dev.rtfm.co.ua]

TASK [test : NGINX test result] ****
ok: [ssh.dev.rtfm.co.ua] => {
  "msg": "NGINX found"
}

TASK [test : NGINX test result] ****
skipping: [ssh.dev.rtfm.co.ua]

PLAY RECAP ****
ssh.dev.rtfm.co.ua      : ok=3    changed=0    unreachable=0    failed=0
... 
```

移除 NGINX:

```
root@rtfm-do-dev:~# apt purge nginx 
```

再次运行:

```
...
TASK [test : Check if NGINX is installed] ****
ok: [ssh.dev.rtfm.co.ua]

TASK [test : NGINX test result] ****
skipping: [ssh.dev.rtfm.co.ua]

TASK [test : NGINX test result] ****
ok: [ssh.dev.rtfm.co.ua] => {
  "msg": "NGINX NOT found"
}

PLAY RECAP ****

ssh.dev.rtfm.co.ua      : ok=3    changed=0    unreachable=0    failed=0 
```

完成了。

### 类似的帖子

*   <small>03/10/2019</small> [普罗米修斯:用 ansi ble-Grafana，Loki，和 promtail](https://dev.to/setevoy/prometheus-rtfm-blog-monitoring-set-up-with-ansible--grafana-loki-and-promtail-58i) <small>(0)</small> 设置的 RTFM 博客监控
*   T005/22/2018 t1t2ansible:标签、include_vars 和变量优先级
*   t07/13/2018 t1t2ansible:在恢复 t3t 4 之前检查 NGINX 配置