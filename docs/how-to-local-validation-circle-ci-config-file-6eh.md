# 如何本地验证 Circle 配置文件

> 原文：<https://dev.to/fumiya_kume/how-to-local-validation-circle-ci-config-file-6eh>

# 步骤 1 安装 Circle CI CLI 工具。

Circle CI 有 CLI 工具，可在 HomeBrew 安装。执行该命令并安装 Circle CI CLI 工具。

```
brew install circleci 
```

# Step2 执行验证命令。

在 Circle CI CLI 工具中验证本地配置文件。

```
circleci config validate 
```

## 成功输出

```
Config file at .circleci/config.yml is valid. 
```