# 整理你的烧瓶申请

> 原文：<https://dev.to/mandrewcito/dockerizing-your-flask-application-kdo>

首先，我们在 apache 服务器上运行 flask 应用程序，因此我们需要配置 apache 服务器。完整的教程可以查看[这里](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)

# wsgi 文件

```
#!/usr/bin/python import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/")

from src.run import app as application 
```

Enter fullscreen mode Exit fullscreen mode

# App python

```
 import sys
sys.path.insert(0,"/var/www/")

from app.application import ExampleApi

from config import Configuration

cfg = Configuration(debug=False)
app = ExampleApi(cfg)
if __name__ == "__main__":
    app.run(host=cfg.host,port=cfg.port,debug=cfg.debug) 
```

Enter fullscreen mode Exit fullscreen mode

# Dockerfile

```
FROM ubuntu:12.04

MAINTAINER Andrés Baamonde Lozano

RUN apt-get update && apt-get install -y apache2 libapache2-mod-wsgi python-dev python-pip && apt-get clean && rm -rf /var/lib/apt/lists/*
RUN pip install Flask
#enable mod_wsgi
RUN a2dissite default
RUN a2dissite default-ssl
RUN a2enmod wsgi

ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2

COPY src /var/www/src

#Creamos el virtual host
COPY resources/docker/app /etc/apache2/sites-available/appflask
RUN chown -R www-data:www-data /var/www/src
RUN chown www-data:www-data /etc/apache2/sites-available/appflask
RUN a2ensite appflask

EXPOSE 80
CMD ["/usr/sbin/apache2", "-D", "FOREGROUND"] 
```

Enter fullscreen mode Exit fullscreen mode

你可以在我的 [github](https://github.com/mandrewcito/flask-example-app) 查看完整的应用