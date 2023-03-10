# 将 Nginx 入口控制器配置为使用 JSON 日志格式

> 原文：<https://dev.to/bzon/send-gke-nginx-ingress-controller-logs-to-stackdriver-2ih4>

## 问题

我正在运行部署在 GKE 的 Nginx 入口控制器，我想构建我的 web 服务器日志，这样我就可以在 Google Cloud Stackdriver 中创建指标和警报。

### 参考文献

*   [https://cloud . Google . com/logging/docs/reference/v2/rest/v2/log entry](https://cloud.google.com/logging/docs/reference/v2/rest/v2/LogEntry)
*   [https://kubrintes . github . io/entry-engine/user-guide/engine-configuration/log-format/](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/log-format/)

### 解

如果使用 GKE，已经有一个 Fluentd DaemonSet 将所有容器日志转发给 Stackdriver。

你需要做的就是确保 Nginx 入口控制器的配置图有以下**日志-格式-上游**设置:

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-ingress-controller
  labels:
    app: nginx-ingress
data:
  log-format-escape-json: "true"
  log-format-upstream: '{"timestamp":  "$time_iso8601",  "requestID":  "$req_id",  "proxyUpstreamName":
    "$proxy_upstream_name",  "proxyAlternativeUpstreamName":  "$proxy_alternative_upstream_name","upstreamStatus":
    "$upstream_status",  "upstreamAddr":  "$upstream_addr","httpRequest":{"requestMethod":
    "$request_method",  "requestUrl":  "$host$request_uri",  "status":  $status,"requestSize":
    "$request_length",  "responseSize":  "$upstream_response_length",  "userAgent":  "$http_user_agent",
    "remoteIp":  "$remote_addr",  "referer":  "$http_referer",  "latency":  "$upstream_response_time  s",
    "protocol":"$server_protocol"}}' 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用 [**舵图**](https://github.com/helm/charts/tree/master/stable/nginx-ingress) 部署 Nginx，值文件看起来会像这样:

```
controller:
  config:
    log-format-escape-json: "true"
    log-format-upstream: '{"timestamp":  "$time_iso8601",  "requestID":  "$req_id",  "proxyUpstreamName":
    "$proxy_upstream_name",  "proxyAlternativeUpstreamName":  "$proxy_alternative_upstream_name","upstreamStatus":
    "$upstream_status",  "upstreamAddr":  "$upstream_addr","httpRequest":{"requestMethod":
    "$request_method",  "requestUrl":  "$host$request_uri",  "status":  $status,"requestSize":
    "$request_length",  "responseSize":  "$upstream_response_length",  "userAgent":  "$http_user_agent",
    "remoteIp":  "$remote_addr",  "referer":  "$http_referer",  "latency":  "$upstream_response_time  s",
    "protocol":"$server_protocol"}}' 
```

Enter fullscreen mode Exit fullscreen mode