# 尝试 Ionic 预览应用程序的 Docker 命令

> 原文：<https://dev.to/vochicong/docker-command-to-try-the-ionic-preview-app-227>

```
docker run --rm -p 8100:8100 --namionic node:8 bash -c \
  "git clone https://github.com/ionic-team/ionic-preview-app.git; cd ionic-preview-app &&\
  npm install && npm install -g ionic && ionic serve"

open http://localhost:8100 
```