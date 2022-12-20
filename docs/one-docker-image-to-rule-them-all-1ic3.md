# 一个码头工人的形象来统治他们

> 原文：<https://dev.to/novaloop/one-docker-image-to-rule-them-all-1ic3>

我们有一个有多个部门的网站。我们希望对所有部门重复使用相同的 docker 图像。

第一种方法是从服务器动态加载配置(取决于映像中的`ENV`变量)。这意味着每个客户端都必须发送请求，配置服务器的正常运行时间至关重要。

我们现在在`environment.ts`文件中使用变量，例如:

```
export const environment = {
  ...
  url: 'https://__DOMAIN__',
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们有一个 docker 的`entrypoint.sh`脚本，可以用参数调用它，并替换应用程序中的字符串。

```
#!/bin/bash

DOMAIN="$1"

for filename in dist/**/*.js; do
    if [[ $filename =~ "main" ]]; then
        sed -i -e 's/__DOMAIN__/'$DOMAIN'/g' $filename
    fi
done

...

pm2-runtime start pm2.json --web 
```

Enter fullscreen mode Exit fullscreen mode

该映像用于 Kubernetes 部署:

```
...
  spec:
    containers:
      - image: <image-repo>
        args: ["www.ecologic.ch"]  
... 
```

Enter fullscreen mode Exit fullscreen mode

也有可能。然后在`environment`文件中使用`ENV`变量，在`entrypoint`脚本中使用`envsubst`。在 kubernetes 部署中，您可以添加`env`变量，而不是`args`。

## 需要更多细节？

如果您对更多细节感兴趣，请告诉我们！