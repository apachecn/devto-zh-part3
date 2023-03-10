# Prometheus:black box-exporter probe _ http _ status _ code = = 0 及其调试

> 原文：<https://dev.to/setevoy/prometheus-blackbox-exporter-probehttpstatuscode--0-and-its-debug-41mc>

[![](img/8eb2782278fc08cb85541163de751f0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5EqCkUqa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2017/09/prometheus-logo.png) 今天我决定将 Grafana 升级到已经发布的 6.0 版本以及所有其他 Docker 图片。

升级成功了——Loki 最终开始显示以前遗漏的日志文件名和其他标记，就在——我立即从用于检查每个 API 端点的 blackbox-exporter 的 Slack 中收到了一堆关键警报。

实际上，在这篇文章中——一个如何调试黑盒导出器的例子。

精确监控的堆栈由以下容器组成:

```
admin@monitoring-production:~$ cat /opt/prometheus/prometheus-compose.yml | grep -B1 image | grep -v image

prometheus-server:
--
grafana-ui:
--
cloudwatch-exporter-us-east-1:
--
cloudwatch-exporter-us-east-2:
--
cloudwatch-exporter-eu-west-1:
--
blackbox-exporter:
--
alertmanager:
--
loki:
--
grafana-loki: 
```

在普罗米修斯图上看起来是这样的:

[![](img/ea85cf48414ad76074fb04a153ce28e1.png "Prometheus: blackbox-exporter probe_http_status_code == 0 и его debug")](https://rtfm.co.ua/wp-content/uploads/2019/03/Screenshot_20190306_210033.png)

在第一瞬间，我有点害怕，因为这是真正的生产 API 端点和警报…它们全都突然“关闭”。

但是很明显——这是 Docker images 升级后 blackbox-exporter 本身的问题(在这之后，我最终添加了带有 Docker images 标签/版本的 Ansible 变量，而不是只有“ *latest* ”)。

好的，让我们试着调试一下，看看发生了什么。

首先，让我们看看谷歌，而不是我们的网址:

```
root@monitoring-production:/home/admin# curl -s "localhost:9115/probe?target=https://google.com&module=http_200_module" | grep -v \#
probe_dns_lookup_time_seconds 0.001286702
probe_duration_seconds 0.001563372
probe_failed_due_to_regex 0
probe_http_content_length 0
probe_http_duration_seconds{phase="connect"} 0
probe_http_duration_seconds{phase="processing"} 0
probe_http_duration_seconds{phase="resolve"} 0.001286702
probe_http_duration_seconds{phase="tls"} 0
probe_http_duration_seconds{phase="transfer"} 0
probe_http_redirects 0
probe_http_ssl 0
probe_http_status_code 0
probe_http_version 0
probe_ip_protocol 6
probe_success 0 
```

还有`probe_http_status_code 0`，还有`probe_success 0` …

*“出事了”* (с)

将`debug=true`参数添加到探针的请求中:

```
root@monitoring-production:/home/admin# curl -s "localhost:9115/probe?debug=true&target=https://google.com&module=http_200_module" | grep -v \#
Logs for the probe:
ts=2019-03-06T14:52:43.226669976Z caller=main.go:118 module=http_200_module target=https://google.com level=info msg="Beginning probe" probe=http timeout_seconds=4.5
ts=2019-03-06T14:52:43.226800447Z caller=utils.go:42 module=http_200_module target=https://google.com level=info msg="Resolving target address" ip_protocol=ip6
ts=2019-03-06T14:52:43.227902933Z caller=utils.go:70 module=http_200_module target=https://google.com level=info msg="Resolved target address" ip=2607:f8b0:4009:804::200e
ts=2019-03-06T14:52:43.227948957Z caller=http.go:349 module=http_200_module target=https://google.com level=info msg="Making HTTP request" url=https://[2607:f8b0:4009:804::200e] host=google.com
ts=2019-03-06T14:52:43.228076802Z caller=http.go:364 module=http_200_module target=https://google.com level=error msg="Error for HTTP request" err="Get https://[2607:f8b0:4009:804::200e]: dial tcp [2607:f8b0:4009:804::200e]:443: connect: cannot assign requested address"
ts=2019-03-06T14:52:43.228098528Z caller=http.go:450 module=http_200_module target=https://google.com level=info msg="Response timings for roundtrip" roundtrip=0 start=2019-03-06T14:52:43.227999657Z dnsDone=2019-03-06T14:52:43.227999657Z connectDone=2019-03-06T14:52:43.228064489Z gotConn=0001-01-01T00:00:00Z responseStart=0001-01-01T00:00:00Z end=0001-01-01T00:00:00Z
ts=2019-03-06T14:52:43.228120026Z caller=main.go:131 module=http_200_module target=https://google.com level=error msg="Probe failed" duration_seconds=0.001397763

Metrics that would have been returned:
...
probe_http_status_code 0
probe_http_version 0
probe_ip_protocol 6
probe_success 0
Module configuration:
prober: http
timeout: 5s 
```

诶诶…

> ts = 2019-03-06t 14:52:43.227948957 z caller = HTTP . go:349 module = HTTP _ 200 _ module target =[https://google.com](https://google.com)level = info msg = " Making HTTP request " URL = https://[2607:f8b 0:4009:804::200 e]host = Google . com
> T3】ts = 2019-03-06t 14:

*URL = https://[2607:f8b 0:4009:804::200 e]host = Google . com*–WTF？IP v6？但是总是使用 IP v4

检查`blackbox_exporter`的[文档](https://github.com/prometheus/blackbox_exporter/blob/master/CONFIGURATION.md):

```
... 
# The IP protocol of the HTTP probe (ip4, ip6). 
[preferred_ip_protocol: <string> | default = "ip6"]
... 
```

啊，好吧，好吧…

默认版本已更改。

突然间，IP v6 在世界上的应用比我想象的要多得多。

你可以在[ipv6-test.com](http://ipv6-test.com/stats/)上查看统计数据。

OK–更新配置并显式设置 ip4:

```
modules:
  icmp:
    prober: icmp
    timeout: 5s
  http_200_module:
    prober: http
    timeout: 5s
    http:
      preferred_ip_protocol: ip4
      valid_status_codes: [200]
      method: GET
      no_follow_redirects: true
      fail_if_not_ssl: true 
```

再次检查:

```
root@monitoring-production:/home/admin# curl -s "localhost:9115/probe?debug=true&target=https://google.com&module=http_200_module" | grep -v \#

Logs for the probe:
ts=2019-03-06T14:57:41.827341666Z caller=main.go:118 module=http_200_module target=https://google.com level=info msg="Beginning probe" probe=http timeout_seconds=4.5
ts=2019-03-06T14:57:41.82743191Z caller=utils.go:42 module=http_200_module target=https://google.com level=info msg="Resolving target address" ip_protocol=ip4
ts=2019-03-06T14:57:41.847978323Z caller=utils.go:70 module=http_200_module target=https://google.com level=info msg="Resolved target address" ip=172.217.8.174
ts=2019-03-06T14:57:41.848021827Z caller=http.go:349 module=http_200_module target=https://google.com level=info msg="Making HTTP request" url=https://[172.217.8.174] host=google.com
ts=2019-03-06T14:57:41.949592972Z caller=http.go:305 module=http_200_module target=https://google.com level=info msg="Received redirect" url=https://www.google.com/
ts=2019-03-06T14:57:42.075895742Z caller=http.go:368 module=http_200_module target=https://google.com level=info msg="Received HTTP response" status_code=200
ts=2019-03-06T14:57:42.078492632Z caller=http.go:450 module=http_200_module target=https://google.com level=info msg="Response timings for roundtrip" roundtrip=0 start=2019-03-06T14:57:41.848092002Z dnsDone=2019-03-06T14:57:41.848092002Z connectDone=2019-03-06T14:57:41.867216977Z gotConn=2019-03-06T14:57:41.917500103Z responseStart=2019-03-06T14:57:41.949545477Z end=0001-01-01T00:00:00Z
ts=2019-03-06T14:57:42.078528466Z caller=http.go:450 module=http_200_module target=https://google.com level=info msg="Response timings for roundtrip" roundtrip=1 start=2019-03-06T14:57:41.949692456Z dnsDone=2019-03-06T14:57:41.950657776Z connectDone=2019-03-06T14:57:41.969811463Z gotConn=2019-03-06T14:57:42.020416388Z responseStart=2019-03-06T14:57:42.075831768Z end=2019-03-06T14:57:42.078481538Z
ts=2019-03-06T14:57:42.078563182Z caller=main.go:129 module=http_200_module target=https://google.com level=info msg="Probe succeeded" duration_seconds=0.251186806

Metrics that would have been returned:
...
probe_http_status_code 200
probe_http_version 1.1
probe_ip_protocol 4
probe_ssl_earliest_cert_expiry 1.5586899e+09
probe_success 1
Module configuration:
prober: http
timeout: 5s
http:
preferred_ip_protocol: ip4 
```

[![](img/9da5cd9c8437b773376f17f1a8f4fbf0.png "Prometheus: blackbox-exporter probe_http_status_code == 0 и его debug")](https://rtfm.co.ua/wp-content/uploads/2019/03/Screenshot_20190306_211754.png)

*(由于`no_follow_redirects: true`添加到配置中，出现了 3xx)*

一切都回到了正常状态。

[![](img/8bcc65f08eee115193d0ba2e0af039bf.png "Prometheus: blackbox-exporter probe_http_status_code == 0 и его debug")](https://rtfm.co.ua/wp-content/uploads/2019/03/Screenshot_20190306_211845.png)

完成了。

### 类似的帖子

*   <small>07/27/2018</small>T2】普罗米修斯:alert managerиblack box-exporter–проверкасрокадействияSSLинотификациявslack<small>(0)</small>
*   <small>02/13/2019</small>[Grafana Labs:Loki——使用 AWS S3 作为数据存储，使用 AWS DynamoDB 作为索引](https://rtfm.co.ua/en/grafana-labs-loki-using-aws-s3-as-a-data-storage-and-aws-dynamodb-for-indexes/) <small>(0)</small>
*   t010/30/2018 年 t1t2prometheus:在 alert manager T3t 3 上漫游亚历山德罗斯
*   T008/15/2018 t1t2prometheus:检查无度量–AVG _ over _ tie