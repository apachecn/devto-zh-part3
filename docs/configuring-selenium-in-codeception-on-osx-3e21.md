# 在 OSX 上配置硒元素

> 原文：<https://dev.to/ljonesfl/configuring-selenium-in-codeception-on-osx-3e21>

## 安装硒

使用 brew，这是一个非常简单的任务。

```
 brew install selenium-server-standalone 
```

## 安装 Chromedriver

```
 brew tap homebrew/cask
    brew cask install chromedriver 
```

## 配置代码异常

Tho old acceptance.suite.yml:

```
 actor: AcceptanceTester
    modules:
        enabled:
            - PhpBrowser:
            url: http://devsite.local
            - \Helper\Acceptance 
```

更新以使用 selenium:

```
 actor: AcceptanceTester
    modules:
        enabled:
            - WebDriver:
                url: http://devsite.local
                browser: chrome
            - \Helper\Acceptance 
```

## 运行测试

首先，点燃硒:

```
 selenium-server -port 4444 
```

然后运行您的测试(假设安装了 composer):

```
 vendor/bin/codecept run 
```

此时，您应该会看到浏览器弹出，并开始响应看不见的手指。