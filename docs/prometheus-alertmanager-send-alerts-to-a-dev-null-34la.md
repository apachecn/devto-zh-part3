# Prometheus:alert manager–向“/dev/null”发送警报

> 原文：<https://dev.to/setevoy/prometheus-alertmanager-send-alerts-to-a-dev-null-34la>

[![](img/8eb2782278fc08cb85541163de751f0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5EqCkUqa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2017/09/prometheus-logo.png) 除了 [Prometheus: Alertmanager 的警报接收者和路由基于严重级别和标签的帖子](https://rtfm.co.ua/en/prometheus-alertmanager-send-alerts-to-a-dev-null/)帖子。

具有带路由的 Alertmanager 配置。

任务是–将开发环境中的所有警报发送到“/dev/null”中。

为此–创建一个空接收器:

```
...
receivers:
  - name: 'blackhole'
  - name: 'default'
    slack_configs:
      - send_resolved: true
        title_link: 'http://dev.monitor.example.world/prometheus/alerts'
        title: '{{ if eq .Status "firing" }}:confused:{{ else }}:dancing_panda:{{ end }} [{{ .Status | toUpper }}] {{ .CommonAnnotations.summary }}'
        text: "{{ range .Alerts }}*Priority*: `{{ .Labels.severity | toUpper }}`\nMonitoring host: {{ .Labels.monitor }}\n{{ .Annotations.description }}\n{{ end }}"
... 
```

在 routes 中添加新的规则——用`match_re`检查我们监控中使用的 env 标签，并将所有匹配发送到`receiver: blackhole` :

```
...
  receiver: 'default'
  routes:
    # capture All INFO
    - match:
        severity: info
      receiver: default
      routes:
      # forward Dev INFO to the 'blackhole'
      - match_re:
          env: .*(-dev).*
        receiver: blackhole
    # capture All WARN to the 'warning' with P3
    - match:
        severity: warning
      receiver: warning
      routes:
      # forward Dev WARNING to the 'blackhole'
      - match_re:
          env: .*(-dev).*
        receiver: blackhole
      # forward Stage WARN to the 'default'
      - match_re:
          env: .*(-stage).*
        receiver: default
    # capture All CRIT to the 'critical' with P1
    - match:
        severity: critical
      receiver: critical
      routes:
      # forward Dev CRIT to the 'blackhole'
      - match_re:
          env: .*(-dev).*
        receiver: blackhole
      # forward Stage CRIT to the 'default'
      - match_re:
          env: .*(-stage).*
        receiver: default
... 
```

完成了。

### 类似的帖子

*   <small>03/06/2019</small> [普罗米修斯:black box-exporter probe _ http _ status _ code = = 0 及其调试](https://dev.to/setevoy/prometheus-blackbox-exporter-probehttpstatuscode--0-and-its-debug-41mc) <small>(0)</small>
*   <small>03/10/2019</small> [普罗米修斯:用 ansi ble-Grafana，Loki，和 promtail](https://dev.to/setevoy/prometheus-rtfm-blog-monitoring-set-up-with-ansible--grafana-loki-and-promtail-58i) <small>(0)</small> 设置的 RTFM 博客监控
*   T008/15/2018 t1t2prometheus:检查无度量–AVG _ over _ tie
*   <small>2018 年 10 月 30 日</small> [普罗米修斯:警报管理器基于严重级别和标签的警报接收者和路由](https://rtfm.co.ua/en/prometheus-alertmanagers-alerts-receivers-and-routing-based-on-severity-level-and-tags/) <small>(0)</small>