# NGINX:访问日志——记录除 200 个响应之外的所有响应

> 原文：<https://dev.to/setevoy/nginx-access-logs--log-all-except-200-responses-426o>

[![](img/78085e4140d88b37a268ebde3e60aac9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r6fzzO2X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2012/09/nginx_logo2.jpg) 任务是将所有请求保存到一个日志文件中，不包括来自 NGINX 的响应为 200 的请求。

使用下面描述的方法和`map`你可以做各种事情，例如——根据一个头值选择一个要使用的位置。后面会补充这样一个例子。

现在，我们对 NGINX 的两个功能感兴趣:在需要将事件保存到日志文件时选择的和根据其他变量设置变量值的 [`map`](http://nginx.org/en/docs/http/ngx_http_map_module.html) 。

### `map`

在`http {}`块中添加`$abnormal`变量，并将其值设置为 0——如果响应值为 200 或默认值为 1——否则:

```
http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;
    map $status $abnormal {
        ~^200  0;
        default 1;
    }
    ... 
```

### 有条件记录

接下来，在使用`if`条件的`server {}`中设置用于`access.log`的条件:如果`$abnormal`变量的值为零，则不记录任何内容，如果为 1，则将该响应保存到日志:

```
server {
    charset utf-8;
    listen 80 default_server;
    server_name stage.example.com;
    if ($http_x_forwarded_proto = 'http') {
        return 301 https://stage.example.com$request_uri;
    }
    set $root_path /data/projects/frontend/web;
    root $root_path;
    index index.php;
    error_log /var/log/nginx/stage.example.com-error.log;
    access_log /var/log/nginx/stage.example.com-access.log combined if=$abnormal;
... 
```

检查一下:

127 . 0 . 0 . 1---[15/Mar/2019:14:51:37+0200]" GET/nginx _ status HTTP/1.1 " 404 153 "-" Go-HTTP-client/1.1 "

127 . 0 . 0 . 1---[15/Mar/2019:14:51:52+0200]" GET/nginx _ status HTTP/1.1 " 404 153 "-" Go-HTTP-client/1.1 "

完成了。

### 类似的帖子

*   <small>04/12/2017</small>[nginx:динамический上游](https://rtfm.co.ua/nginx-dinamicheskij-upstream/) <small>(0)</small>
*   <small>03/23/2017</small>[engine:用户代理](https://rtfm.co.ua/draft-nginx-ban-user-agent/)<small>【0】</small>
*   <small>03/17/2019</small> [NextCloud:用 PHP-FPM 在 NGINX 后面的 Debian 上安装服务器，在 Arch Linux 上安装客户端](https://dev.to/setevoy/nextcloud-installing-server-on-debian-behind-nginx-with-php-fpm-and-client-on-arch-linux-51bj) <small>(0)</small>
*   <small>2018 年 9 月 20 日</small>[【engine:gzip 和 etg 弱验证】](https://rtfm.co.ua/nginx-gzip-i-etag-weak-validation/)<small></small>
<small></small>

<small></small>