# 使用 Portus 托管您自己的私人 docker 注册表

> 原文：<https://dev.to/mrpowerscripts/host-your-own-private-docker-registry-with-portus-5ha7>

如果你熟悉 docker，那么你可能用过 DockerHub。Dockerhub 很牛逼。我绝对喜欢(大部分)它和它所做的事情。DockerHub 只是一个 docker 容器注册表。这是一个你可以存储、拉取和共享 docker 容器的地方。

[葡萄牙语教程视频](https://www.youtube.com/watch?v=gvaRfAqCfGY)

这不是唯一的集装箱登记处。谷歌有自己的容器注册表，你可以上传你的图片。微软和亚马逊也有 docker 容器注册中心。所有这些都很好，但是如果你想在一个私有注册表中托管你的 won docker 图片呢？我提到的那些注册中心支持私有帐户，但是我将揭示如何托管你自己控制的私有容器注册中心。

想要自动化整个配置过程吗？我有一个脚本，将在 10 分钟内设置好这一切！这对于第一次安装非常有用。你可以[在此处](http://bit.ly/mrps-portus-registry)或页面底部找到该脚本的链接。尽情享受吧！

两个要求:

*   域名
*   一个服务器

服务器很简单。我在配置了 SSH 访问的数字海洋上使用了售价 10 美元的 Ubuntu 18.10 droplet。虽然它应该也适用于 18.04。当您可以 SSH 到您的服务器时，您就为下一步做好了准备

我确信您想在本地使用 docker 客户端登录到您的 docker 注册中心——就像您使用 DockerHub 一样。为了做到这一点，您和注册中心(您的服务器)之间必须有一个安全的连接。否则 docker 会大发雷霆，要求各种各样的黑客来让事情运转起来。

因此，您需要一个来自可信证书颁发机构的 SSL 证书。我们将使用 [LetsEncrypt](https://letsencrypt.org) ，因为它是免费的并且可以自动化。我用[这篇博文](https://www.humankode.com/ssl/how-to-set-up-free-ssl-certificates-from-lets-encrypt-using-docker-and-nginx)在服务器上获得了我的 SSL 证书。它解释了确保您的域正确配置安全连接所需的一切。

一旦正确配置了您的域并且 SSL 证书在服务器上，您就可以开始配置注册表了。docker 自己维护并发布[一个 Docker 映像](https://hub.docker.com/_/registry/)，它是一个 Docker 注册表。是的，他们在 docker 容器中放了一个 docker 容器注册表。但是有一个问题。没有图形用户界面。没有门禁。它本身并不令人兴奋。如果有某个基于这个注册表容器的开源项目，包含了所有很酷的功能就好了。哦，对了，有。[葡萄牙](https://github.com/SUSE/Portus)。

葡萄牙太棒了。我将在视频中介绍 Portus UI，但它基本上能做你想让它做的一切。您可以创建帐户、团队和名称空间。您可以添加访问控制，使图像成为私人图像、登录用户的私人图像或互联网上任何人都可以使用的公共图像。用户界面非常干净，组织良好。

这篇[博客文章](https://www.objectif-libre.com/en/blog/2018/06/11/self-hosting-a-secure-docker-registry-with-portus/)把我带到了配置 Portus 并让它运行的正确方向。幸运的是，他们有一个`docker-compose.yml`文件可以用来启动所有的服务。

在修改了博客文章中的`docker-compose.yml`以使用我自己的 SSL 证书，并在这里和那里做了一些额外的配置更改以改善体验之后，Portus 服务器就可以使用了！

现在，我能够`docker login`进入我自己的安全私有 Docker 注册表，在那里我可以管理和共享对图像的访问。

```
#!/bin/bash

### ##
### This script will turn an Ubuntu 18.04/10 server into a docker registry with a GUI ##
### ##

## MrPowerScripts ##
# Website: https://MrPowerScripts.com
# Discord: https://bit.ly/mrps-discord
# Patreon: https://bit.ly/mrps-patreon
# Sign up: https://bit.ly/mrps-mail-list
# Twitter: https://bit.ly/mrps-twitter

# This script was created thanks to a number of guides and other peoples efforts. Please check them out!
# https://www.objectif-libre.com/en/blog/2018/06/11/self-hosting-a-secure-docker-registry-with-portus/
# https://www.humankode.com/ssl/how-to-set-up-free-ssl-certificates-from-lets-encrypt-using-docker-and-nginx

# THis script is intended to be run on ubuntu 18.04 or 18.10\. You must run it from the home directory (~).
# I was able to run the box fine on a $10 droplet from digital ocean.
# It will provision the environment, install an ssl cert from letsencrypt, and start the Portus registry service.
# You can login to your new docker registry at https://${your_domain}:3000.
# You WILL NOT be able to connect through http. You must prefix your URL with HTTPS.
# You will be asked to create an admin account on first visit. If you have any issues join my Discord server.

# After you have the server set up you can create other user account, or use the admin account.
# You will be able to login to your private registry using the docker client by pointing it to your domain.
# ex. docker login forestfiles.com

# From here you can interact with your private registry to push and pull images as you would on DockerHub.

# =====!!!!!!! IMPORTANT !!!!!!!=====
# Make sure to set these values.
# If your server is on a subdomain (sub.forestfiles.com) that's fine
# The domain must be set up with a properly configured A Record that points to the server IP.
# You cannot use an IP address with this configuration. You must have a domain name.
DOMAIN="forestfiles.com"
EMAIL="email@gmail.com"

if ["$DOMAIN" == "forestfiles.com"]; then
  echo "you forgot to change the domain"
  exit
elif ["$EMAIL" == "email@gmail.com"]; then
  echo "you forgot to change the email"
  exit
fi

# We'll use this later
UBUNTU=$( lsb_release -r | awk '{ print $2 }' | sed 's/[.]//' )
sudo apt-get update && sudo apt-get install jq -y

if ["$UBUNTU" == 1804]; then
  # Install Docker Normally
  sudo apt-get install \
      apt-transport-https \
      ca-certificates \
      curl \
      software-properties-common -y
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
  sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable" -y
  sudo apt-get update -y
  sudo apt-get install docker-ce -y
elif ["$UBUNTU" == 1810]; then
  # Install docker 18.10 - this is before they had a release for this version. hack required
  sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
  sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic test" -y
  sudo apt install docker-ce -y
else
 echo "This script was meant to be run on at least Ubuntu 18.04"
 exit
fi

# Install docker compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.21.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Install Portus
rm -rf /tmp/portus
git clone https://github.com/SUSE/Portus.git /tmp/portus
mv /tmp/portus/examples/compose ./portus

# Edit the portus files for our domain and SSL
(
cd portus || exit
docker-compose down -v
sed -i "s/172.17.0.1/${DOMAIN}/g" .env
sed -i "s/172.17.0.1/${DOMAIN}/g" nginx/nginx.conf
sed -i "s/portus.crt/fullchain.pem/g" nginx/nginx.conf
sed -i "s/portus.key/privkey.pem/g" nginx/nginx.conf
sed -i "s/portus.crt/fullchain.pem/g" registry/init
rm docker-compose.*
)

# We're getting all the config files into the right place
sudo mkdir -p /docker/letsencrypt-docker-nginx/src/letsencrypt/letsencrypt-site
cat << 'EOF' > ./portus/docker-compose.yml
version: "2"

services:
  portus:
    image: opensuse/portus:head
    environment:
      - PORTUS_MACHINE_FQDN_VALUE=${MACHINE_FQDN}
      - PORTUS_SECURITY_CLAIR_SERVER=http://clair:6060

      # DB. The password for the database should definitely not be here. You are
      # probably better off with Docker Swarm secrets.
      - PORTUS_DB_HOST=db
      - PORTUS_DB_DATABASE=PORTUS_production
      - PORTUS_DB_PASSWORD=${DATABASE_PASSWORD}
      - PORTUS_DB_POOL=5

      # Secrets. It can possibly be handled better with Swarm's secrets.
      - PORTUS_SECRET_KEY_BASE=${SECRET_KEY_BASE}
      - PORTUS_KEY_PATH=/certificates/privkey.pem
      - PORTUS_PASSWORD=${PORTUS_PASSWORD}

      # SSL
      - PORTUS_PUMA_TLS_KEY=/certificates/privkey.pem
      - PORTUS_PUMA_TLS_CERT=/certificates/fullchain.pem

      # NGinx is serving the assets instead of Puma. If you want to change this,
      # uncomment this line.
      - RAILS_SERVE_STATIC_FILES=true

      # Other Config
      #- PORTUS_SIGNUP_ENABLED=false
      - PORTUS_ANONYMOUS_BROWSING_ENABLED=false
      - PORTUS_DELETE_ENABLED=true
    ports:
      - 3000:3000
    links:
      - db
    volumes:
      - ./secrets:/certificates:ro
      #- ./static:/srv/Portus/public

  background:
    image: opensuse/portus:head
    depends_on:
      - portus
      - db
    environment:
      # Theoretically not needed, but cconfig's been buggy on this...
      - CCONFIG_PREFIX=portus
      - PORTUS_MACHINE_FQDN_VALUE=${MACHINE_FQDN}
      - PORTUS_SECURITY_CLAIR_SERVER=http://clair:6060

      # DB. The password for the database should definitely not be here. You are
      # probably better off with Docker Swarm secrets.
      - PORTUS_DB_HOST=db
      - PORTUS_DB_DATABASE=PORTUS_production
      - PORTUS_DB_PASSWORD=${DATABASE_PASSWORD}
      - PORTUS_DB_POOL=5

      # Secrets. It can possibly be handled better with Swarm's secrets.
      - PORTUS_SECRET_KEY_BASE=${SECRET_KEY_BASE}
      - PORTUS_KEY_PATH=/certificates/privkey.pem
      - PORTUS_PASSWORD=${PORTUS_PASSWORD}

      - PORTUS_BACKGROUND=true
      - PORTUS_SYNC_ENABLED=true
      - PORTUS_SYNC_STRATEGY=update-delete
    links:
      - db
    volumes:
      #- ./secrets:/certificates:ro
      - /etc/letsencrypt/live/dr.mrpowerscripts.com:/certificates

  db:
    image: library/mariadb:10.0.23
    command: mysqld --character-set-server=utf8 --collation-server=utf8_unicode_ci --init-connect='SET NAMES UTF8;' --innodb-flush-log-at-trx-commit=0
    environment:
      - MYSQL_DATABASE=PORTUS_production

      # Again, the password shouldn't be handled like this.
      - MYSQL_ROOT_PASSWORD=${DATABASE_PASSWORD}
    volumes:
      - ./mariadb:/var/lib/mysql

  registry:
    image: library/registry:2.6
    command: ["/bin/sh", "/etc/docker/registry/init"]
    environment:
      # Authentication
      REGISTRY_AUTH_TOKEN_REALM: https://${MACHINE_FQDN}/v2/token
      REGISTRY_AUTH_TOKEN_SERVICE: ${MACHINE_FQDN}:5000
      REGISTRY_AUTH_TOKEN_ISSUER: ${MACHINE_FQDN}
      REGISTRY_AUTH_TOKEN_ROOTCERTBUNDLE: /secrets/fullchain.pem

      # SSL
      REGISTRY_HTTP_TLS_CERTIFICATE: /secrets/fullchain.pem
      REGISTRY_HTTP_TLS_KEY: /secrets/privkey.pem

      # portus endpoint
      REGISTRY_NOTIFICATIONS_ENDPOINTS: >
        - name: portus
          url: https://${MACHINE_FQDN}/v2/webhooks/events
          timeout: 2000ms
          threshold: 5
          backoff: 1s
    volumes:
      - ./secrets:/usr/local/share/ca-certificates:ro
      - ./registry/data:/var/lib/registry
      - ./secrets:/secrets:ro
      - ./registry/config.yml:/etc/docker/registry/config.yml:ro
      - ./registry/init:/etc/docker/registry/init:ro
    ports:
      - 5000:5000
      - 5001:5001 # required to access debug service
    links:
      - portus:portus

  postgres:
    image: library/postgres:10-alpine
    environment:
      POSTGRES_PASSWORD: portus

  clair:
    image: quay.io/coreos/clair
    restart: unless-stopped
    depends_on:
      - postgres
    links:
      - postgres
      - portus
    ports:
      - "6060-6061:6060-6061"
    volumes:
      - /tmp:/tmp
      - ./clair/clair.yml:/clair.yml
    command: [-config, /clair.yml]

  nginx:
    image: library/nginx:alpine
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./secrets:/secrets:ro
      - static:/srv/Portus/public:ro
    ports:
      - 80:80
      - 443:443
    links:
      - registry:registry
      - portus:portus

volumes:
  static:
EOF

cat << EOF > /docker/letsencrypt-docker-nginx/src/letsencrypt/docker-compose.yml
version: '3.1'

services:

  letsencrypt-nginx-container:
    container_name: 'letsencrypt-nginx-container'
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
      - ./letsencrypt-site:/usr/share/nginx/html
    networks:
      - docker-network

networks:
  docker-network:
    driver: bridge
EOF

cat << EOF > /docker/letsencrypt-docker-nginx/src/letsencrypt/nginx.conf
server {
    listen 80;
    listen [::]:80;
    server_name ${DOMAIN}.com www.${DOMAIN}.com;

    location ~ /.well-known/acme-challenge {
        allow all;
        root /usr/share/nginx/html;
    }

    root /usr/share/nginx/html;
    index index.html;
}
EOF

# Bring up the nginx webserver to get the letsencrypt certs
cd /docker/letsencrypt-docker-nginx/src/letsencrypt || exit
sudo docker-compose up -d || exit

sleep 5

# Now we're getting the real cert here
sudo docker run --rm \
  -v /docker-volumes/etc/letsencrypt:/etc/letsencrypt \
  -v /docker-volumes/var/lib/letsencrypt:/var/lib/letsencrypt \
  -v /docker/letsencrypt-docker-nginx/src/letsencrypt/letsencrypt-site:/data/letsencrypt \
  -v "/docker-volumes/var/log/letsencrypt:/var/log/letsencrypt" \
  certbot/certbot \
  certonly --webroot \
  --email "${EMAIL}" --agree-tos --no-eff-email \
  --webroot-path=/data/letsencrypt \
  -d "${DOMAIN}"

# Copy our new cert to the portus secrets folder
cp /docker-volumes/etc/letsencrypt/live/"${DOMAIN}"/* ~/portus/secrets

# Shutdown the letsencrypt webserver
cd /docker/letsencrypt-docker-nginx/src/letsencrypt || exit
sudo docker-compose down

cd ~/portus || exit

docker-compose up -d --force-recreate

# ----------- this is all stuff to do a self signed cert
# Yuu have to do all kinda of weird client side stuff to connect to self signed certs tho
# echo "subjectAltName = URI:${DOMAIN}" > extfile.cnf
# echo "basicConstraints=CA:FALSE" >> extfile.cnf || exit
# echo "subjectAltName=@my_subject_alt_names" >> extfile.cnf || exit
# echo "subjectKeyIdentifier = hash" >> extfile.cnf || exit
# echo "[my_subject_alt_names]" >> extfile.cnf || exit
# echo "DNS.1 = *.${DOMAIN}" >> extfile.cnf || exit

# openssl genrsa -out secrets/rootca.key 2048
# dd if=/dev/urandom of=~/.rnd bs=256 count=1
# openssl req -x509 -new -nodes -key secrets/rootca.key \
# -subj "/C=US/ST=CA/O=Acme, Inc." \
# -sha256 -days 1024 -out secrets/rootca.crt

# openssl genrsa -out secrets/portus.key 2048
# openssl req -new -key secrets/portus.key -out secrets/portus.csr \
# -subj "/C=US/ST=CA/O=Acme, Inc./CN=${DOMAIN}"

# openssl x509 -req -in secrets/portus.csr -CA secrets/rootca.crt -extfile \
# extfile.cnf -CAkey secrets/rootca.key -CAcreateserial \
# -out secrets/portus.crt -days 500 -sha256 
```