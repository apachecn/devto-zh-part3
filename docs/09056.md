# Prometheus: Alertmanager 的警报接收器和基于严重性级别和标签的路由

> 原文：<https://dev.to/setevoy/prometheus-alertmanagers-alerts-receivers-and-routing-based-on-severity-level-and-tags-3f9c>

[![](img/8eb2782278fc08cb85541163de751f0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5EqCkUqa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2017/09/prometheus-logo.png) 我们有三个工作环境——*Dev*、 *Stage* 、 *Production* 。

此外，还有一堆严重性不同的警报——*信息*、*警告*、и *严重*。

例如:

```
...
- name: SSLexpiry.rules
  rules:
  - alert: SSLCertExpiring30days
    expr: probe_ssl_earliest_cert_expiry{job="blackbox"} - time() < 86400 * 30
    for: 10m
    labels:
      severity: info
    annotations:
      summary: "SSL certificate warning"
      description: "SSL certificate for the {{ $labels.instance }} will expire within 30 days!"
... 
```

警报被发送给 Slack 和 [OpsGenie](https://rtfm.co.ua/prometheus-opsgenie-i-alertmanager-uvedomleniya-v-pochtu-sms-telefon/) 。

该任务取决于环境和严重程度——仅发送给时差，或时差+ OpsGenie。

OpsGenie 将根据严重程度依次执行以下操作:

*   对于*警告*——将向其移动应用程序发送电子邮件和通知
*   对于*关键的*——电子邮件加通知到它的移动应用程序，再加上机器人打电话到手机

因此，整个逻辑如下所示:

*   *开发*
    *   所有消息独立于严重性–仅发送给空闲时间
*   *分期*:
    *   *信息*–仅时差
    *   *警告* и *关键*–松弛+ OpsGenie 并为 OpsGenie 设置*警告*优先级 *(P3)*
*   *生产*
    *   *信息*–仅时差
    *   *警告* и *关键*–松弛和优先，并为优先 *(P1)* 设置*关键*

为了分解 Slack 和 OpsGenie 之间的消息，我们为 OpsGenie 配置了三个[接收器](https://prometheus.io/docs/alerting/configuration/#%3Creceiver%3E)和*警告*和*关键*接收器——优先级 *P3* 或 *P1* 将被设置为

```
...
receivers:
  - name: 'default'
    slack_configs:
      - send_resolved: true
        title_link: 'https://monitor.example.com/prometheus/alerts'
        title: '{{ if eq .Status "firing" }}:confused:{{ else }}:dancing_panda:{{ end }} [{{ .Status | toUpper }}] {{ .CommonAnnotations.summary }}'
        text: "{{ range .Alerts }}*Priority*: `{{ .Labels.severity | toUpper }}`\nMonitoring host: {{ .Labels.monitor }}\n{{ .Annotations.description }}\n{{ end }}"
  - name: 'warning'
    slack_configs:
      - send_resolved: true
        title_link: 'https://monitor.example.com/prometheus/alerts'
        title: '{{ if eq .Status "firing" }}:disappointed_relieved:{{ else }}:dancing_panda:{{ end }} [{{ .Status | toUpper }}] {{ .CommonAnnotations.summary }}'
        text: "{{ range .Alerts }}*Priority*: `{{ .Labels.severity | toUpper }}`\nMonitoring host: {{ .Labels.monitor }}\n{{ .Annotations.description }}\n{{ end }}"
    opsgenie_configs:
      - priority: P3
  - name: 'critical'
    slack_configs:
      - send_resolved: true
        title_link: 'https://monitor.example.com/prometheus/alerts'
        title: '{{ if eq .Status "firing" }}:scream:{{ else }}:dancing_panda:{{ end }} [{{ .Status | toUpper }}] {{ .CommonAnnotations.summary }}'
        text: "{{ range .Alerts }}*Priority*: `{{ .Labels.severity | toUpper }}`\nMonitoring host: {{ .Labels.monitor }}\n{{ .Annotations.description }}\n{{ end }}"
    opsgenie_configs:
      - priority: P1 
```

而路由本身是在 [`route`](https://prometheus.io/docs/alerting/configuration/#%3Croute%3E) 块:
中完成的

```
...
route:
  group_by: ['alertname', 'cluster', 'job', 'env']
  repeat_interval: 24h
  group_interval: 5m
  # capture All Dev + All INFO
  receiver: 'default'
  routes:
    # capture All WARN to the 'warning' with P3
    - match:
        severity: warning
      receiver: warning
      routes:
      # forward Dev WARN to the 'default'
      - match_re:
          env: .*(-dev).*
        receiver: default
    # capture All CRIT to the 'critical' with P1
    - match:
        severity: critical
      receiver: critical
      routes:
      # forward Stage CRIT to the 'warning'
      - match_re:
          env: .*(-stage).*
        receiver: warning
      # forward Dev CRIT to the 'default'
      - match_re:
          env: .*(-dev).*
        receiver: default
... 
```

在这里，我们设置了'`default`'路由–所有与下面的其他规则不匹配的警报都将通过此路由发送，它将只发送松弛通知。

其他路线描述如下:

1.  在`match`中，使用`severity: warning`标签捕捉警报
2.  在使用`match_re`的嵌套路由中，将检查`env`——如果它是“ *-dev* ”值，那么它将发送回`default`接收器
3.  所有其他具有*警告*级别的警报将被发回，并通过`receiver: warning`接收器

类似地，将应用下一级别的规则——用`severity: critical`捕捉警报并检查它们:

1.  如果`env: .*(-stage).*`–则转到`warning`接收器
2.  如果`env: .*(-dev).*`–则转到`default`接收器
3.  其他一切(只剩下*环境==生产*和*严重性==关键*)将通过`critical`接收器

使用这种方法，您可以使用任何标记编写规则，并使用任何嵌套级别来检查条件和选择下一个警报路由。

### 类似的帖子

*   <small>03/26/2019</small> [普罗米修斯:alert manager–向 a "/dev/null"](https://dev.to/setevoy/prometheus-alertmanager--send-alerts-to-a-devnull-he-temp-slug-751082) <small>(0)</small> 发送警报
*   <small>03/06/2019</small> [普罗米修斯:black box-exporter probe _ http _ status _ code = = 0 及其调试](https://dev.to/setevoy/prometheus-blackbox-exporter-probehttpstatuscode--0-and-its-debug-41mc) <small>(0)</small>
*   T008/15/2018 t1t2prometheus:检查无度量–AVG _ over _ tie
*   <small>03/10/2019</small> [普罗米修斯:用 ansi ble-Grafana，Loki，和 promtail](https://dev.to/setevoy/prometheus-rtfm-blog-monitoring-set-up-with-ansible--grafana-loki-and-promtail-58i) <small>(0)</small> 设置的 RTFM 博客监控