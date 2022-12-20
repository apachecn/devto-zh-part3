# Nginx 上的 Joomla:制作 SEF URL

> 原文：<https://dev.to/sm0k3/joomla-on-nginx-making-sef-urls-1co6>

[![Outsourcing](img/57f7f127364c871c693ebdd740815117.png "Setting up SEF urls for Joomla on Nginx with php-fpm")](https://res.cloudinary.com/practicaldev/image/fetch/s--RwfRpSQz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qor2vtvu8v0ib5aaudbb.png)

作为自由职业者，有时我会做一些有趣的工作，比如行政和安全工程师。这篇文章将是关于为运行在 Nginx 上的 Joomla 设置 SEF URL。我已经在网上搜索了关于如何设置这样的网址的信息，但是没有在一个地方找到所有的东西。应用一些解决方案管理面板不能正常工作或显示为例如“index.php”文件，所以在这里我将添加简单的解决方案，如何使事情正常工作。

要求如下:使从 www 到非 www 域重定向，只使用 HTTPS，删除斜线和隐藏“index.php”文件从地址栏。我们的堆栈:ISP 管理器面板，Nginx + php-fpm 和 Joomla。还想提一下，客户要求用 php-fpm 将 web 服务器从 Apache(以前使用的)改为 nginx。

首先，让我们将用户从 http 发送到 https:

```
server {
     listen 80;
     server_name mysite.com www.mysite.com;
     return 301 https://mysite.com$request_uri; ##sending users to https with no www
 }

```

写着“返回 301…”表示此重定向是永久的，而不是临时的(代码 302 是临时重定向)。现在用户只会在 https 上访问我们。接下来，添加 https 配置并从路径中删除 www(即，借助“重写”重定向用户):

```
server {
     listen 192.168.1.12:443 ssl;
     server_name mysite.com www.mysite.com;
     if ($http_host = www.mysite.com) {
         rewrite  (.*)  https://mysite.com$request_uri; ##rewriting path to non www domain
     } ...other part of the config...}

```

这里我们设置了一个来自 www 的重定向，并在 https 下开始配置。下一步是删除“index.php”文件，这样它就不会显示:

```
rewrite ^/index.php/(.*) /$1  permanent; ##hiding our index.php file

```

接下来我们有了标准的 nginx 配置，考虑到 SSL 下的工作，这里我不公布它，但在文章的最后会提供完整的配置。我们的下一步是设置 SEF URL 的工作，并删除斜线(例如，如果用户在末尾输入 3 个斜线，它们将消失，页面将正确加载，不会出现类似 404 的错误)，但同时我们不应该忘记 404 的正确处理:

```
location / {
    try_files $uri $uri/ @joomlaurls; ## adding our processing rules
}
location @joomlaurls {
    rewrite ^/(.+)/$ /$1 permanent; ## removing slashes
    try_files $uri $uri/ /index.php?$args; ## setting up SEF urls
}
location ~ [^/]\.ph(p\d*|tml)$ {
    try_files /does_not_exists @php; ## processing 404
}

access_log off;
location @php {
    fastcgi_index index.php;
    fastcgi_pass unix:/var/www/php-fpm/www-root.sock;
    fastcgi_split_path_info ^((?U).+\.ph(?:p\d*|tml))(/?.+)$;
    try_files $uri =404;
    include fastcgi_params;
}

```

我们的网站将正确处理所有的 SEF 网址，给 404，如果没有页面，从地址栏中删除斜线，还隐藏了“index.php”文件的路径，默认情况下，我们将从文件夹中读取“index.php”文件，所以这里不需要直接指向它。

关于管理面板还有一点——如果配置不正确，那么 Joomla 管理面板将根本无法工作，或者它将沿着“/administrator/index.php”路径笨拙地打开，只有这样，使用我上面给出的配置——一切都将得到处理并正常工作。同样在 Joomla 本身，你也需要激活重定向功能(在管理区或 Joomla)。

下面你可以找到我在 nginx 上使用 php-fpm 的完整配置文件:

```
server {

    listen 80;

    server_name mysite.com www.mysite.com;

    return 301 https://mysite.com$request_uri;

}

server {

listen 443 ssl;

server_name www.mysite.com; 

return 301 $scheme://mysite.com$request_uri;

}

server {

    listen 192.168.1.12:443 ssl;

    server_name mysite.com www.mysite.com;

    if ($http_host = www.mysite.com) {

        rewrite  (.)  https://mysite.com$request_uri;
    }
    rewrite ^/index.php/(.) /$1  permanent;

    ssl_certificate "/var/www/httpd-cert/www-root/mysite.com.crtca";

    ssl_certificate_key "/var/www/httpd-cert/www-root/mysite.com.key";

    ssl_ciphers EECDH:+AES256:-3DES:RSA+AES:!NULL:!RC4;

    ssl_prefer_server_ciphers on;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

    add_header Strict-Transport-Security "max-age=31536000;";

    ssl_dhparam /etc/ssl/certs/dhparam4096.pem;

    charset off;

    index index.php;

    disable_symlinks if_not_owner from=$root_path;

    include /etc/nginx/vhosts-includes/.conf;
    include /etc/nginx/vhosts-resources/mysite.com/.conf;

    error_log /var/www/httpd-logs/mysite.com.error.log notice;

    ssi on;

    set $root_path /var/www/www-root/data/www/mysite.com;

    root $root_path;

    gzip on;

    gzip_comp_level 9;

    gzip_disable "msie6";

    gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript application/javascript;

    location / {

        try_files $uri $uri/ @joomlaurls;

    }

    location @joomlaurls {

        rewrite ^/(.+)/$ /$1 permanent;

        try_files $uri $uri/ /index.php?$args;

        #error_page 404 = /index.php;

    }

    location ~ [^/].ph(p\d|tml)$ {
        try_files /does_not_exists @php;
    }
    access_log off;
    location @php {
        fastcgi_index index.php;
        fastcgi_param PHP_ADMIN_VALUE "sendmail_path = /usr/sbin/sendmail -t -i -f admin@mysite.com";
        fastcgi_pass unix:/var/www/php-fpm/www-root.sock;
        fastcgi_split_path_info ^((?U).+.ph(?:p\d|tml))(/?.+)$;

        try_files $uri =404;

        include fastcgi_params;

    }

}

```

就是这样！看似简单？但是在研究和实施过程中仍然出现了一些问题，这些问题在上面的配置示例中已经解决。我以前没有和 Joomla 一起工作过，主要是和 WordPress 一起工作，所以这个案例对我来说很有趣，并且可能对其他人有所帮助。