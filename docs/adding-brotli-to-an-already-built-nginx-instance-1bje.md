# 将 Brotli 添加到已经构建的 NGINX 实例中

> 原文：<https://dev.to/majal/adding-brotli-to-an-already-built-nginx-instance-1bje>

你有一个正在运行的 NGINX 实例，并且想要添加 Brotli 压缩吗？如果您不想从源代码重新编译一个定制的 web 服务器或者替换您的自动更新 repo-installed 版本，该怎么办？你可以创建动态的 Brotli 模块，只要 NGINX 升级，它就会升级。

下面的 bash 脚本解释了安装步骤。请随意一步一步地学习和执行这些代码。这可能有助于您排除系统中的任何异常。否则，只需运行脚本。

```
#!/bin/bash

# https://www.majlovesreg.one/tag/code/
# https://www.majlovesreg.one/adding-brotli-to-a-built-nginx-instance
# https://github.com/majal/maj-server/tree/master/nginx-modules-brotli

# Install needed development packages if not yet installed in the system
# sudo apt -y install git libpcre3 libpcre3-dev zlib1g zlib1g-dev openssl libssl-dev

# For predefined NGINX version, use:
# ngver=1.17.1

# For passing the version via the command line (i.e.: user@server:~$ ./mkbrotli 1.17.1), use:
ngver=$1

# For automated detection of currently installed NGINX version (not to be used for auto-updating, see hooks in post), use:
# ngver=$(nginx -v 2>&1 | grep -o '[0-9\.]*')

# Get configure parameters of installed NGINX. Not needed if NGINX was configured '--with-compat'.
# Uncomment one of the lines below if the script sucessfully builds modules but NGINX throws a "not binary compatible" error.
# confparams=$(nginx -V 2>&1 | grep -o -- '--prefix='.*)
# confparams=$(nginx -V 2>&1 | grep -o -- '--[^with]'.*)
# confparams=$(nginx -V 2>&1 | grep -- '--' | sed "s/.*' //")

# To manually set NGINX modules directory:
# moddir=/path/to/modules/directory

# To automatically select NGINX modules directory:
[ -d /usr/share/nginx/modules ] && moddir=/usr/share/nginx/modules
[ -d $(nginx -V 2>&1 | grep -o 'prefix=[^ ]*' | sed 's/prefix=//')/modules ] && moddir="$(nginx -V 2>&1 | grep -o 'prefix=[^ ]*' | sed 's/prefix=//')/modules"
[ -d $(nginx -V 2>&1 | grep -o 'modules-path=[^ ]*' | sed 's/modules-path=//') ] && moddir="$(nginx -V 2>&1 | grep -o 'modules-path=[^ ]*' | sed 's/modules-path=//')"
[ "${moddir}" ] || { echo '!! missing modules directory, exiting...'; exit 1; }

# Set temporary directory and build on it
builddir="$(mktemp -d)"
cd "${builddir}"

echo
echo '################################################################################'
echo
echo "Building Brotli for NGINX ${ngver}"
echo "Temporary build directory: ${builddir}"
echo "Modules directory: ${moddir}"
echo

# Download and unpack NGINX
wget https://nginx.org/download/nginx-${ngver}.tar.gz && { tar zxf nginx-${ngver}.tar.gz && rm nginx-${ngver}.tar.gz; } || { echo '!! download failed, exiting...'; exit 2; }

# Download, initialize, and make Brotli dynamic modules
git clone https://github.com/google/ngx_brotli.git
cd ngx_brotli && git submodule update --init && cd ../nginx-${ngver}
[ "${confparams}" ] && nice -n 19 ionice -c 3 ./configure --add-dynamic-module=../ngx_brotli "${confparams}" || nice -n 19 ionice -c 3 ./configure --with-compat --add-dynamic-module=../ngx_brotli
nice -n 19 ionice -c 3 make modules || { echo '!! configure or make failed, exiting...'; exit 4; }

# Replace Brotli in modules directory
[ -f "${moddir}/ngx_http_brotli_filter_module.so" ] && sudo mv "${moddir}/ngx_http_brotli_filter_module.so" "${moddir}/ngx_http_brotli_filter_module.so.old"
[ -f "${moddir}/ngx_http_brotli_static_module.so" ] && sudo mv "${moddir}/ngx_http_brotli_static_module.so" "${moddir}/ngx_http_brotli_static_module.so.old"
sudo cp objs/*.so "${moddir}/"
sudo chmod 644 "${moddir}/ngx_http_brotli_filter_module.so" || { echo '!! module permissions failed, exiting...'; exit 5; }
sudo chmod 644 "${moddir}/ngx_http_brotli_static_module.so" || { echo '!! module permissions failed, exiting...'; exit 6; }

# Clean up build files
cd "${builddir}/.."
sudo rm -r "${builddir}/ngx_brotli"
rm -r "${builddir}"

echo
echo "Sucessfully built and installed latest Brotli for NGINX ${ngver}"
echo "Modules can be found in ${moddir}"
echo "Next step: Configure dynamic modules and reload/restart NGINX."
echo
echo '################################################################################'
echo

# Start/restart NGINX.
# Not recommended if script is hooked since NGINX is automatically restarted by the package manager (e.g. apt) after an upgrade.
# Restarting NGINX before the upgrade could cause a module version mismatch.
# sudo nginx -t && { systemctl is-active nginx && sudo systemctl restart nginx || sudo systemctl start nginx; } || true
# echo
# systemctl --no-pager status nginx
# echo 
```

您可以选择通过运行上面的脚本来自动安装。在其默认设置中，它将处理您传递给它的版本号。要获得你的 **NGINX 的版本号**，运行这个命令:

```
$ nginx -v
  nginx version: nginx/1.17.1 
```

由于本例中的**版本**是 1.17.1，在您下载了上面的 bash 脚本之后，转到脚本所在的目录并运行这些命令:

```
$ chmod +x mkbrotli
$ ./mkbrotli 1.17.1 
```

现在，当脚本自动为您下载、构建和安装动态模块时，您可以放松了。如果脚本出现错误，请在评论中让我知道，这样我就可以对它进行调整。

### 

<center>激活模块</center>

现在我们有了模块，激活 Brotli 压缩需要两个配置步骤。首先，**加载 NGINX 主配置文件中的模块**(我的在`/etc/nginx/nginx.conf`)。您可以通过运行命令
获得您的**配置文件路径**

```
$ nginx -V 2>&1 | grep -o 'conf-path=[^ ]*' | sed 's/conf-path=//' 
```

现在编辑 NGINX 的配置文件。在任何 *`block { ... }`* 之外，也许在文件的最上面一节，加上这几行:

```
# nginx/nginx.conf

load_module modules/ngx_http_brotli_filter_module.so;
load_module modules/ngx_http_brotli_static_module.so;

# http {
#   ...
# } 
```

第二，**激活 Brotli** (并设置其[配置指令，又名设置](https://github.com/google/ngx_brotli))或者在 NGINX 的 conf 文件的`http`块中，或者在你的域配置的`server`块中(例如`nginx/sites-enabled/`中的文件)。至于我，我把这些指令放在`nginx/snippets/`文件夹中，并在我的域配置中通过`include`调用它们。

```
# nginx/nginx.conf

# http {
#   ...

  brotli on;
  brotli_static on;
  brotli_types
    text/plain
    text/css
    text/xml
    text/javascript
    text/x-component
    application/xml
    application/xml+rss
    application/javascript
    application/json
    application/atom+xml
    application/vnd.ms-fontobject
    application/x-font-ttf
    application/x-font-opentype
    application/x-font-truetype
    application/x-web-app-manifest+json
    application/xhtml+xml
    application/octet-stream
    font/opentype
    font/truetype
    font/eot
    font/otf
    image/svg+xml
    image/x-icon
    image/vnd.microsoft.icon
    image/bmp;

#   ...
# } 
```

### 

<center>用 NGINX 自动更新</center>

动态模块的一个潜在问题是当 NGINX 通过一个包管理器(例如`apt`、`yum`或`pacman`)自动升级时。如果你和我一样，我的服务器上的所有升级都是由脚本自动完成的。你看，动态模块是特定于版本的。NGINX 被设置为**而不是**来加载为不同版本构建的模块。那么 Brotli 模块**如何和 NGINX 一起自动升级**？

答案是在升级过程中挂接软件包管理器。在`apt`中，这可能是一个两步或三步的过程:

1.  **将**与`apt`挂钩，调用一个工人脚本。(或者，钩子可以直接调用`mkbrotli`,这样就不需要工人脚本了。)

2.  **worker** 脚本调用一个或多个构建脚本来更新动态模块。

3.  **构建脚本**将在 NGINX 升级之前构建并安装新模块。

这里是我的**钩子文件**在`/etc/apt/apt.conf.d/` :

```
# /etc/apt/apt.conf.d/05nginxmodules

// Hook to build and install dynamic modules before NGINX upgrades
// Script calls individual build scripts and passes back error codes
// Place this file in /etc/apt/apt.conf.d/

DPkg::Pre-Install-Pkgs {"/usr/local/sbin/nginx-mod-preinstall";}; 
```

我使用的工人脚本:

```
# /usr/local/sbin/nginx-mod-preinstall

#!/bin/bash
# Call NGINX module build scripts and pass error codes to apt hook

# Get NGINX version to upgrade to
read ngfile < <(grep '/nginx_') || exit 0
ngver=$(echo $ngfile | sed 's/-.*//' | sed 's/.*_//')

# List of build scripts to run:
/usr/local/sbin/mkbrotli $ngver || exit $?
# /usr/local/sbin/mkmodsec $ngver || exit $?
# /usr/local/sbin/mkpagespeed $ngver || exit $? 
```

最后，这部电影的**构建脚本**是……(请击鼓)……当然是`mkbrotli`。😉我希望 Brotli 在未来能够得到 NGINX 的原生支持。太牛逼了。目前，我们有这个解决方法。享受压缩！

### 

<center>谷歌页面速度</center>

另一个旨在加快网络速度的项目是谷歌的最佳实践优化，名为 [PageSpeed](https://developers.google.com/speed/pagespeed/module/) 。我还制作了一个 Bash 脚本，用于在正在运行的 NGINX 服务器上自动安装 PageSpeed。如果你感兴趣，看看这篇文章:[为已经运行的 NGINX 实例](https://www.majlovesreg.one/adding-pagespeed-to-a-running-nginx-instance)添加 PageSpeed。

将 Brotli 添加到已经构建好的 NGINX 实例中首先出现在 [Maj Loves Reg](https://www.majlovesreg.one) 上，由 [Majal Mirasol](https://www.majlovesreg.one/author/majal) 发布。