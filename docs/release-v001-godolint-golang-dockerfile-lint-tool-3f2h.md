# 使用 moby buildkit 解析器的 Dockerfile lint 工具

> 原文：<https://dev.to/zabio3/release-v001-godolint-golang-dockerfile-lint-tool-3f2h>

# [godolint](https://github.com/zabio3/godolint)

帮助你建立[最佳实践](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) Docker 形象的 Dockerfile linter(灵感来自 [Haskell Dockerfile Linter](https://github.com/hadolint/hadolint) )。
对于 AST 的静态分析，使用 [moby/buildkit 解析器](https://github.com/moby/buildkit/tree/master/frontend/dockerfile/parser)，并进行 lint 检查。
该工具基于解析的 AST 执行 docker 规则检查。

## 用法

您可以在本地运行 godolint 来 lint 您的 docker 文件。

```
$ godolint <Dockerfile> 
```

##### 举例

检查 Dockerfile

```
$ godolint testdata/DL3000_Dockerfile
#3 DL3000 Use absolute WORKDIR. 

$ godolint testdata/DL3001_Dockerfile
#6 DL3001 For some bash commands it makes no sense running them in a Docker container like `ssh`, `vim`, `shutdown`, `service`, `ps`, `free`, `top`, `kill`, `mount`, `ifconfig`. 
```

#### 选项

您可以设置一些选项:

```
Available options:
  --ignore RULECODE     A rule to ignore. If present, the ignore list in the
                        config file is ignored

Other Commands:
  --help        -h      Help about any command
  --version     -v      Print the version information 
```

##### 举例

检查 Dockerfile(排除特定规则)。

```
$ godolint --ignore DL3000 testdata/DL3000_Dockerfile 
```

## 安装

您可以从发布页面下载二进制文件，并将其放在 PATH 目录中。

或者你可以使用 go get

```
$ go get github.com/zabio3/godolint 
```

## 规则

已实施的规则。

| 规则 | 描述 |
| --- | --- |
| [DL3000](https://github.com/hadolint/hadolint/wiki/DL3000) | 使用绝对工作目录。 |
| [DL3001](https://github.com/hadolint/hadolint/wiki/DL3001) | 对于一些 bash 命令来说，在 Docker 容器中运行它们是没有意义的，比如 ssh、vim、shutdown、service、ps、free、top、kill、mount、ifconfig。 |
| [DL3002](https://github.com/hadolint/hadolint/wiki/DL3002) | 最后一个用户不应是根用户。 |
| [DL3003](https://github.com/hadolint/hadolint/wiki/DL3003) | 使用 WORKDIR 切换到一个目录。 |
| [DL3004](https://github.com/hadolint/hadolint/wiki/DL3004) | 不要使用 sudo，因为它会导致不可预知的行为。使用 gosu 之类的工具强制 root。 |
| [DL3005](https://github.com/hadolint/hadolint/wiki/DL3005) | 不要使用 apt-get 升级或 dist-upgrade。 |
| [DL3007](https://github.com/hadolint/hadolint/wiki/DL3007) | 如果图像会更新，使用 latest 容易出错。将版本明确固定到发布标签。 |
| [DL3006](https://github.com/hadolint/hadolint/wiki/DL3006) | 总是明确地标记图像的版本。 |
| [DL3008](https://github.com/hadolint/hadolint/wiki/DL3008) | apt-get 安装中的 Pin 版本。 |
| [DL3009](https://github.com/hadolint/hadolint/wiki/DL3009) | 安装一些东西后删除 apt-get 列表。 |
| [DL3010](https://github.com/hadolint/hadolint/wiki/DL3010) | 使用 ADD 将归档文件提取到映像中。 |
| [DL3011](https://github.com/hadolint/hadolint/wiki/DL3011) | 有效的 UNIX 端口范围从 0 到 65535。 |
| [DL3012](https://github.com/hadolint/hadolint/wiki/DL3012) | 提供一个电子邮件地址或网址作为维护者。(此规则已被否决，不再有效) |
| [DL3013](https://github.com/hadolint/hadolint/wiki/DL3013) | pip 中的 Pin 版本。 |
| [DL3014](https://github.com/hadolint/hadolint/wiki/DL3014) | 使用`-y`开关。 |
| [DL3015](https://github.com/hadolint/hadolint/wiki/DL3015) | 通过指定-no-install-recommended 来避免额外的软件包。 |
| [DL3016](https://github.com/hadolint/hadolint/wiki/DL3016) | `npm`中的引脚版本。 |
| [DL3017](https://github.com/hadolint/hadolint/wiki/DL3017) | 不要使用`apk upgrade`。 |
| [DL3018](https://github.com/hadolint/hadolint/wiki/DL3018) | apk 添加中的引脚版本。用`apk add <package>=<version>`代替`apk add <package>`。 |
| [DL3019](https://github.com/hadolint/hadolint/wiki/DL3019) | 使用`--no-cache`开关以避免使用`--update`的需要，并在完成安装包时移除`/var/cache/apk/*`。 |
| [DL3020](https://github.com/hadolint/hadolint/wiki/DL3020) | 对于文件和文件夹，使用`COPY`而不是`ADD`。 |
| [DL3021](https://github.com/hadolint/hadolint/wiki/DL3021) | 超过 2 个参数的`COPY`要求最后一个参数以`/`结束。 |
| [DL3022](https://github.com/hadolint/hadolint/wiki/DL3022) | `COPY --from`应该引用先前定义的`FROM`别名。 |
| [DL3023](https://github.com/hadolint/hadolint/wiki/DL3023) | `COPY --from`不能引用自己的`FROM`别名。 |
| [DL3024](https://github.com/hadolint/hadolint/wiki/DL3024) | 别名(艺名)必须是唯一的。 |
| [DL3025](https://github.com/hadolint/hadolint/wiki/DL3025) | 对 CMD 和 ENTRYPOINT 参数使用 arguments JSON 表示法。 |
| [DL4000](https://github.com/hadolint/hadolint/wiki/DL4000) | MAINTAINER 已被弃用。 |
| [DL4001](https://github.com/hadolint/hadolint/wiki/DL4001) | 使用 Wget 或 Curl，但不要同时使用。 |
| [DL4003](https://github.com/hadolint/hadolint/wiki/DL4003) | 发现多个`CMD`指令。 |
| [DL4004](https://github.com/hadolint/hadolint/wiki/DL4004) | 发现多个`ENTRYPOINT`指令。 |
| [DL4005](https://github.com/hadolint/hadolint/wiki/DL4005) | 使用`SHELL`改变默认外壳。 |
| [DL4006](https://github.com/hadolint/hadolint/wiki/DL4006) | 在有管道的`RUN`之前设置`SHELL`选项-o pipefail。 |

### AST

Dockerfile 语法在 [Dockerfile 参考](https://docs.docker.com/engine/reference/builder/)中有完整描述。
看看 language-docker 项目中的 [moby/buildkit](https://github.com/moby/buildkit/tree/master/frontend/dockerfile/parser) 就知道 AST 的定义了。

## 贡献

贡献当然总是受欢迎的！

1.  叉子扎比奥 3/戈多林特([https://github.com/zabio3/godolint/fork](https://github.com/zabio3/godolint/fork))
2.  运行`go get`来安装依赖项
3.  创建特征分支
4.  提交您的更改
5.  使用`go test ./...`运行测试
6.  创建拉式请求

详见 [`CONTRIBUTING.md`](https://github.com/zabio3/godolint/blob/master/CONTRIBUTING.md) 。