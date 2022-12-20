# SemVer 版本控制实用程序

> 原文：<https://dev.to/pratham/semver-versioning-utility-1dgk>

### [t1 版](#versioner)

根据[sever](https://semver.org/)的流行包管理器的版本读取器/写入器

**安装**

```
 pip install git+https://github.com/VeritasOS/versioner.git 
```

Enter fullscreen mode Exit fullscreen mode

**当前支持**

*   npm
*   dep
*   json
*   汤姆
*   yaml(目前不支持单个文件中的多文档)

**用途**

*   读取版本(从项目根目录运行命令)

```
 > versioner read npm  # projects based on npm
  "0.0.1"

  > versioner read dep  # golang projects
  "0.1.1" 
```

Enter fullscreen mode Exit fullscreen mode

*   编写版本(从项目根目录运行命令)

```
 # package.json
  ....
  "version": "1.1.1",
  ....

  # Release patch version
  > versioner write npm --inc-patch

  # package.json
  ....
  "version": "1.1.2",
  ....

  # Release minor version
  > versioner write npm --inc-minor

  ....
  "version": "1.2.0",
  ....

  # Release major version
  > versioner write npm --inc-major

  ....
  "version": "2.0.0",
  .... 
```

Enter fullscreen mode Exit fullscreen mode

*   从自定义文件读取版本

```
 > versioner read yaml --file /root/app/version.yaml --key-depth metadata,version
  0.0.2

  version.yaml
  ---
  metadata:
    version: 0.0.2 
```

Enter fullscreen mode Exit fullscreen mode

*   将版本写入自定义文件

```
 > versioner write yaml --inc-major --file /root/app/version.yaml --key-depth metadata,version

  version.yaml
  ---
  metadata:
    version: 1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

#### 发展

```
 git clone https://github.com/VeritasOS/versioner.git
  cd versioner
  pip install -e . 
```

Enter fullscreen mode Exit fullscreen mode

**注**

*   该实用程序针对 npm 和 dep 进行了测试。
*   自定义文件的读/写可能会中断，请随时发送补丁。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)数据级别 / [版本](https://github.com/VeritasOS/versioner)

### 根据 SemVer，为流行的包管理器提供版本读取器/写入器

<article class="markdown-body entry-content container-lg" itemprop="text">

### 版本号

根据[sever](https://semver.org/)的流行包管理器的版本读取器/写入器

**安装**

```
pip install git+https://github.com/VeritasOS/versioner.git 
```

**当前支持**

*   npm
*   dep
*   json
*   汤姆
*   yaml(目前不支持单个文件中的多文档)

**用途**

*   读取版本(从项目根目录运行命令)

    ```
     > versioner read npm  # projects based on npm
      "0.0.1"
      > versioner read dep  # golang projects
      "0.1.1" 
    ```

*   编写版本(从项目根目录运行命令)

    ```
     # package.json
      ....
      "version": "1.1.1",
      ....

      # Release patch version
      > versioner write npm --inc-patch

      # package.json
      ....
      "version": "1.1.2",
      ....

      # Release minor version
      > versioner write npm --inc-minor

      ....
      "version": "1.2.0",
      ....

      # Release major version
      > versioner write npm --inc-major

      ....
      "version": "2.0.0",
      .... 
    ```

*   从自定义文件读取版本

    ```
     > versioner read yaml --file /root/app/version.yaml --key-depth metadata,version
      0.0.2

      version.yaml
      ---
      metadata:
        version: 0.0.2 
    ```

*   将版本写入自定义文件

    ```
     > versioner write yaml --inc-major --file /root/app/version.yaml --key-depth metadata,version

      version.yaml
      ---
      metadata:
        version: 1.0.0 
    ```

#### 发展

```
git clone
```

…</article>

[View on GitHub](https://github.com/VeritasOS/versioner)