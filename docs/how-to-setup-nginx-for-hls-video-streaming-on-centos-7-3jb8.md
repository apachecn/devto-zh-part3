# 如何在 Centos 7 上为 HLS 视频流设置 Nginx

> 原文：<https://dev.to/samuyi/how-to-setup-nginx-for-hls-video-streaming-on-centos-7-3jb8>

## 简介

HLS 代表 HTTP 直播流。这是由苹果公司开发的基于 HTTP 的流媒体协议。与 RTP 等基于 UDP 的协议不同，它不能被只允许 HTTP 流量的防火墙阻止。它可以通过 HTTP 服务器(如 Nginx)交付，也可以通过 cdn 分发。

Nginx 的默认安装不符合 HLS 模块；但是有一个开源的支持 HLS 的 Nginx 模块。我们需要从源代码编译 Nginx，并在编译过程中添加模块。

本教程向您展示了如何安装 Nginx 并将其用作视频直播服务器。

## 先决条件

要遵循本教程，请确保目标计算机上存在以下内容:

*   git、wget、gcc、gcc-c++、perl、gd、gd-devel、perl-textutils-Embed、geoip、geoip-devel 和 tar
*   具有 sudo 功能的非 root 用户

如果您没有构建实用程序，您需要安装它们。运行以下命令:

```
$ sudo yum update
$ sudo yum install epel-release
$ sudo yum install git wget gcc gcc-c++ tar gd gd-devel perl-ExtUtils-Embed geoip geoip-devel 
```

## 步骤 1 -下载并编译 Nginx 及其依赖项

我们需要下载 Nginx 的依赖库；包括用于为 nginx 提供 HLS 功能的开放源码 [nginx-rtmp](https://github.com/arut/nginx-rtmp-module) 模块。首先我们下载 Nginx [内核](https://nginx.org/en/docs/ngx_core_module.html)和[重写](https://nginx.org/en/docs/http/ngx_http_rewrite_module.html)模块所需的 [PCRE](http://pcre.org/) 模块。运行以下命令:

```
 $    wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.42.tar.gz
 $   tar -zxf pcre-8.42.tar.gz
 $   rm -rf pcre-8.42.tar.gz
 $   cd pcre-8.42
 $   ./configure
 $   make
 $   sudo make install
 $   cd 
```

接下来我们下载 Nginx 的 [Nginx Gzip 模块](https://nginx.org/en/docs/http/ngx_http_gzip_module.html)需要的 [zlib](//www.zlib.net) 模块并安装。运行这个来做:

```
$ wget http://zlib.net/zlib-1.2.11.tar.gz
$ tar -zxf zlib-1.2.11.tar.gz
$ rm -rf zlib-1.2.11.tar.gz
$ cd zlib-1.2.11
$ ./configure
$ make
$ sudo make install
$ cd 
```

接下来，我们下载 [Nginx SSL 模块](https://nginx.org/en/docs/http/ngx_http_ssl_module.html)所需的 [openssl 模块](http://www.openssl.org)。运行这个来做:

```
$ wget http://www.openssl.org/source/openssl-1.0.2q.tar.gz
$ tar -zxf openssl-1.0.2q.tar.gz
$ rm -rf openssl-1.0.2.tar.gz
$ cd openssl-1.0.2q
$ ./config
$ make
$ sudo make install
$ cd 
```

然后，我们从 github 资源库下载开源的 nginx-rtmp 模块。要做到这一点，运行:

```
$ git clone git://github.com/arut/nginx-rtmp-module.git 
```

最后，我们下载 Nginx 源代码。我们将从[nginx.org](http://www.nginx.org/en/download.html)下载最新的稳定版本，在撰写本文时是 1.14.2。运行这个来做:

```
$ wget https://nginx.org/download/nginx-1.14.2.tar.gz
$ tar zxf nginx-1.14.2.tar.gz
$ rm -rf nginx-1.14.2.tar.gz
$ cd nginx-1.14.2 
```

现在我们有了必要的依赖项，可以编译 Nginx 了。现在我们需要配置构建选项。这是通过运行。/configure "脚本，其中有许多可供 Nginx 编译的选项。这些选项包括以前下载和安装的开源模块、zlib 模块、pcre 模块和 openssl 模块的路径。我们还需要指定要编译哪些内置 Nginx 模块。我们运行这个来获得想要的构建选项:

```
$ ./configure  --add-module=../nginx-rtmp-module \
--sbin-path=/usr/sbin/nginx \ 
--lock-path=/var/run/nginx.lock \
--conf-path=/etc/nginx/nginx.conf \
--pid-path=/run/nginx.pid \   
--with-pcre=../pcre-8.42 \    
--with-zlib=../zlib-1.2.11 \  
--with-openssl=../openssl-1.0.2q \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--user=nginx \                 
--group=nginx \                
--with-http_auth_request_module \
--with-http_degradation_module \
--with-http_geoip_module \     
--with-http_gunzip_module \    
--with-http_gzip_static_module \
--with-http_image_filter_module \
--with-http_mp4_module \
--with-http_perl_module \
--with-http_realip_module \
--with-http_secure_link_module \
--with-http_slice_module \
--with-http_ssl_module  \
--with-http_stub_status_module \
--with-http_v2_module \
--with-stream_ssl_module \
--with-stream \
--with-threads \
--prefix=/etc/nginx 
```

最后编译构建 Nginx。

```
$ make 
$ sudo make install 
```

检查安装是否正确运行:

```
$ nginx -V #The output should be the Nginx version, compiler version, and configure script parameters. 
```

我们需要安装 Nginx 手册页，为此运行以下代码:

```
$ sudo cp ~/nginx-1.14.2/man/nginx.8 /usr/share/man/man8
$ sudo gzip /usr/share/man/man8/nginx.8 
```

现在我们清理以前下载的库。

```
$ rm -rf nginx-1.14.2 nginx-rtmp-module openssl-1.0.2q pcre-8.42 zlib-1.2.11 
```

## 步骤 2–设置和配置 Nginx

既然 nginx 二进制文件已经安装在我们的搜索路径中，我们需要设置一个 Nginx 用户。要设置 nginx 用户，运行:

```
$ sudo useradd --system --home /var/lib/nginx --shell /sbin/nologin --comment "nginx system user" nginx 
```

我们还需要创建存储 nginx 日志的目录，并让用户 Nginx 成为所有者。为此我们跑:

```
$ sudo mkdir /var/log/nginx &&  sudo chown nginx:nginx /var/log/nginx 
```

完成这些后，就该创建 nginx systemd 服务单元文件了。它的内容应该是这样的:

```
 [Unit]
  Description=nginx - high performance web server
  Documentation=https://nginx.org/en/docs/
  After=network.target remote-fs.target nss-lookup.target
  Wants=network-online.target
        [Service]
  Type=forking
  PIDFile=/run/nginx.pid
  ExecStartPre=/usr/bin/rm -f /run/nginx.pid 
  ExecStartPre=/usr/sbin/nginx -t -c /etc/nginx/nginx.conf
  ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf
  ExecReload=/bin/kill -s HUP $MAINPID
  KillSignal=SIGQUIT
  TimeoutStopSec=5
  KillMode=process
  PrivateTmp=true
     [Install]
  WantedBy=multi-user.target 
```

我们现在将上述内容粘贴到 nginx 服务文件中:

```
$ sudo vim  /lib/systemd/system/nginx.service   # You can replace vim with whichever editor you prefer 
```

现在我们重新加载 systemctl 守护进程并启动 Nginx。

```
$ sudo systemctl daemon-reload
$ sudo systemctl start nginx
$ sudo systemctl enable nginx 
```

现在我们需要配置 Nginx 来流视频。我们的 nginx.conf 文件；位于/etc/nginx/目录中，应该是这样的:

```
user  nginx;
worker_processes  auto;
 server_tokens off;
events {
    worker_connections  1024;
}
# We need to setup an rmtp server to stream video from client devices
rtmp {
    server {
      listen 1935;
      chunk_size 4096;
      ping 30s;
      notify_method get;
      allow play all;
       # rmtp handler our clients connect to for live streaming, it runs on port 1935\. It converts the stream to HLS and stores it on our server
   application app {
          live on;
          hls on;   
          hls_path /var/www/hls/live;
          hls_nested on;  # create a new folder for each stream
          record_notify on;
          record_path /var/www/videos;
          record all;
          record_unique on;
     }

    application vod {
       play /var/www/videos;
    }
 }
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    sendfile        on;
    tcp_nopush     on;
    keepalive_timeout  65;
    gzip  on;
    server {
        listen       80;
        server_name  _;
        location / {
            root   html;
            index  index.html index.htm;
        }
          # the http end point our web based users connect to see the live stream
          location /live {
            types {
                application/vnd.apple.mpegurl m3u8; 
             }
                 alias /var/www/hls/live;
                add_header Cache-Control no-cache;
       }
   } 
```

我们还需要创建存储视频流的目录。

```
 $ sudo mkdir -p /var/www/hls/live 
```

现在我们可以重启 nginx 来重新加载新的配置文件。

```
$ sudo systemctl restart nginx 
```

## 步骤 3-流式传输和发布视频

现在，为了从客户端机器上实时播放视频，假设客户端在本地存储了视频并且安装了 [ffmpeg](https://www.ffmpeg.org/) ，我们运行这个来发布到我们的服务器:

```
$ ffmpeg -i /path/to/video  -c:v h264 -c:a aac  -strict -2 -f flv rtmp://server_ip:1935/app/unique_stream_name     #the name of the stream has to be unique 
```

我们的观众可以通过流式传输 url: `http://server_ip/live/unique_stream_key/index.m3u8`在 [vlc 媒体播放器](https://www.videolan.org/vlc/index.html)上观看视频。还可以通过运行:
从 Linux 客户机上发布来自网络摄像头的视频

```
$ ffmpeg -f video4linux2 -i /dev/video0 -c:v libx264 -an -f flv rtmp://server_ip:1935/app/unique_stream_name 
```

从一本苹果笔记本上看，是这样的

```
$ ffmpeg -f avfoundation -framerate 30 -i "0" -c:v libx264 -an -f flv rtmp://server_ip:1935/app/unique_stream_name 
```

## 结论

这就是如何用 Nginx 设置 HLS。为了更进一步，我们可以用我们最喜欢的编程语言编写一个应用程序来处理用户认证，并可能将视频存储在媒体服务器上。关键是我们的用户可以用正规的 HTTP 直播流媒体视频。