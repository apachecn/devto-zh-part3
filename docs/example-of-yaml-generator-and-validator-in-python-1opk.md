# Python 中 Yaml 生成器和验证器的示例

> 原文：<https://dev.to/jmarhee/example-of-yaml-generator-and-validator-in-python-1opk>

不管你是否经常使用 Yaml，大多数人都知道，它绝对*关心空白，即使是细心的从业者有时也会自动化一个糟糕的过程，对于 Yaml 来说，这是一个糟糕的时期，所以验证(特别是在生成 Yaml 时，更不用说手工编写了)是必须的。
让我们看一个常见的 Yaml 用例:Kubernetes 清单。在我的例子中，我希望创建不同的配置，动态地填充信息(例如，像已知长度的令牌之类的东西)，然后转储到使用 elsehwere 的 Yaml 文件中。我用 Python 用`pyyaml`做到了这一点。
要在您的集群中对资源(如机密)使用静态加密，Kubernetes 需要一个 EncryptionConfig 文件，这是一个很短的 Yaml 生成文件，它只需要提供者、密钥以及在 Etcd 中对哪个资源进行静态加密，哪个资源生成为 Yaml，我将把它表示为 JSON:*

```
configIn = {
        "kind": "EncryptionConfig",
        "apiVersion": "v1",
        "resources": [
            {
            "resources": [
                "secrets"
            ],
            "providers": [
                {
                "aescbc": {
                    "keys": [
                    {
                        "name": "key1",
                        "secret": "%s" % (generateSecret(32))
                    }
                    ]
                }
                }
            ]
            }
        ]
        } 
```

然后我们将使用那个`generateSecret`(一个[λ](http://www.diveintopython.net/power_of_introspection/lambda_functions.html)，它接受一个字符串长度并返回该长度的随机字符串的 base64 编码版本)结果来填充那个 JSON 对象的值:

```
import base64
import random
import string
import os
import sys
import yaml
generateSecret = lambda length: base64.b64encode(''.join(random.sample(string.lowercase+string.digits,length))) #32 length def populateConfig():
    configIn = {
        "kind": "EncryptionConfig",
        "apiVersion": "v1",
        "resources": [
            {
            "resources": [
                "secrets"
            ],
            "providers": [
                {
                "aescbc": {
                    "keys": [
                    {
                        "name": "key1",
                        "secret": "%s" % (generateSecret(32))
                    }
                    ]
                }
                }
            ]
            }
        ]
        }

    configOut = yaml.dump(configIn)
    return configOut 
```

然后让`yaml.dump`将该对象作为 Yaml 返回给我们:

```
apiVersion: v1
kind: EncryptionConfig
resources:
- providers:
  - aescbc:
      keys:
      - {name: key1, secret: BASE64_STRING }
  resources: [secrets] 
```

这是有效的 Yaml，但为了使它符合 Kubernetes 风格(因为从 1.11 开始，它所支持的实验特性不接受这一点)，我们将把`configOut`行的 dump 选项改为如下所示:

```
 configOut = yaml.dump(configIn,default_flow_style=False) 
```

要返回:

```
apiVersion: v1
kind: EncryptionConfig
resources:
- providers:
  - aescbc:
      keys:
      - name: key1
        secret: BASE64_STRING
  resources:
  - secrets 
```

好的，很好，我们已经得到了我们的配置，并且它*看起来*相当正确，但是因为它是自动创建的，我们可能想要仔细检查。
有几种方法可以做到这一点，但是因为我的输入相对简单，模式没有以任何有意义的方式修改，只是填充数据，而且因为我更喜欢用已经导入的库来做这件事，我们可以使用`yaml`包的内置`safe_load`方法来查看传入的配置(就像上面函数返回的那个)是否验证了:

```
def validateYaml(config):
    try:
        yaml.safe_load(config)
        return config
    except:
        sys.exit('Failed to validate config.') 
```

如果配置无法验证(这一点很重要),这个函数将会退出，但如果验证成功，它将返回有效的配置，因此有了这些信息，我们可以前进到程序的入口点将所有这些连接在一起，如果有效，我们将把配置写入一个文件:

```
if __name__ == '__main__':  
    config = validateYaml(populateConfig())
    EncryptionConfig = open("secrets.conf","w")
    EncryptionConfig.write(config)
    EncryptionConfig.close()
    print "OK" 
```

如果`validateYaml`失败，它将阻止我们编写一个错误的配置(或者至少是一个肯定**不会**工作的配置，其他验证问题可能会出现，safe_load 在更复杂的 Yaml 输入中可能默认检测不到)。*