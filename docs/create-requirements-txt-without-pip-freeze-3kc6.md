# 创建 requirements.txt 而不冻结 pip

> 原文：<https://dev.to/0xkoji/create-requirements-txt-without-pip-freeze-3kc6>

这对人们来说完全没有用，因为我们可以使用`pip freeze > requirements.txt`来创建 requirements.txt lol

但是下面的代码不需要 pip 冻结命令

```
import pkg_resources
OUTPUT_FILE = 'requirements.txt'
with open(OUTPUT_FILE, mode='w') as f:
    for dist in pkg_resources.working_set:
        requirement = dist.project_name + '==' + dist.version
        f.write(requirement + '\n') 
```

Enter fullscreen mode Exit fullscreen mode