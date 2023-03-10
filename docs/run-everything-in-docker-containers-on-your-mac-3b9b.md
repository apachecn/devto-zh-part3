# 运行 Docker 容器中的所有内容(在 Mac 上)

> 原文：<https://dev.to/greenido/run-everything-in-docker-containers-on-your-mac-3b9b>

如果你希望在你的 Mac 上看到 docker 的强大功能，作为运行所有(或许多)日常应用程序的一种方式，请查看 Jess 的这篇[好文章。](https://blog.jessfraz.com/post/docker-containers-on-the-desktop/)

然而，当你打算用所有 docker 文件来[repo 时，很明显它是要在 Linux 上使用的。然而，这里有一个在 Mac 上运行所有这些强大应用程序的捷径。](https://github.com/jessfraz/dockerfiles) 

```
 # Install socat 
brew install socat 

# Install xQuartz as our X11 server 
brew cask install xquartz 

# Run it 
socat TCP-LISTEN:6000,reuseaddr,fork UNIX-CLIENT:\"$DISPLAY\" # 

# In a new terminal 
# open -a XQuartz 

# Let's test it by running Firefox in Docker 

docker run -it -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=docker.for.mac.host.internal:0 jess/firefox 
```

Enter fullscreen mode Exit fullscreen mode

简单，不是吗？如果你好奇我从哪里救了你？

查看本[话题](https://github.com/moby/moby/issues/8710)上的线程。

享受快乐和富有成效的时光。

[![Packages are great not only for software](img/7a546ed6ddebf7342636adf9c3bfcf2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N-I6Iq8y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://greenido.files.wordpress.com/2019/03/screen-shot-2019-03-26-at-1.51.33-pm.png%3Fw%3D696)