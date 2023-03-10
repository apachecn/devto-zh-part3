# 今天的 rails 安全更新用简单的英语介绍

> 原文：<https://dev.to/ajsharp/todays-rails-security-update-in-plain-english-5h88>

*交叉发布自[分享秘密博客](https://blog.sharesecret.co/todays-rails-security-update-plain-english/)。*

* * *

今天早些时候，Rails 团队[推出了新版本](https://weblog.rubyonrails.org/2019/3/13/Rails-4-2-5-1-5-1-6-2-have-been-released/)来修补三个安全漏洞:

1.  [CVE-2019-5418](https://groups.google.com/forum/#!topic/rubyonrails-security/pFRKI96Sm8Q) :动作查看文件内容公开
2.  [CVE-2019-5419](https://groups.google.com/forum/#!topic/rubyonrails-security/GN7w9fFAQela) :行动查看拒绝服务(DOS)
3.  [CVE-2019-5420](https://groups.google.com/forum/#!topic/rubyonrails-security/IsQKvDqZdKw) : Rails 开发模式远程代码执行(RCE)

我已经按照严重性的顺序解决了下面的漏洞。

**注意: [rails 升级指南](https://guides.rubyonrails.org/upgrading_ruby_on_rails.html)是升级你的 rails 应用的一个很好的资源。**

## 🚨 🚨 🚨 🚨 🚨 🔥操作查看拒绝服务(DOS)

**报警等级:**五级火警。立即修补。

如果您正在渲染模板，您几乎肯定会受到 DOS 攻击。这个真的很糟糕。立即打补丁/升级。

使用特制的标题，您可以通过利用模板位置代码来最大限度地利用 CPU。包装在`respond_to`块中的渲染模板是安全的。否则，你很脆弱。

易受攻击:

```
class UserController < ApplicationController 
  def index 
    render "index" 
  end 
end 
```

不脆弱:

```
class UserController < ApplicationController 
  def index 
    respond_to |format| 
      format.html { render "index" } 
    end 
  end 
end 
```

[阅读更多](https://groups.google.com/forum/#!topic/rubyonrails-security/GN7w9fFAQeI)

## 🚨 🚨 🚨 🚨 🚨 🔥操作查看文件内容公开

**报警等级:**五级火警。立即修补。

通过使用特制的头，如果使用`render file: 'filename'`，您可以查看任意文件的内容。不太好。

好消息是:如果你只是渲染普通模板，你不会受到这个漏洞的影响*，尽管你可能会受到 CVE 2019-5419 的影响。*

[阅读更多](https://groups.google.com/forum/#!topic/rubyonrails-security/pFRKI96Sm8Q)

## 🚨RCE 铁路发展模式

**报警等级:**不好，但是回去睡觉。早上修好它。

由于 rails 如何在开发模式中生成`secret_key_base`**——应用程序模块名称的 MD5 如果您知道应用程序的名称，您就可以计算出密钥。只要你没有开发模式的应用程序暴露在公众面前，这不是一个大问题，但仍然是值得修复的。**

[阅读更多](https://groups.google.com/forum/#!topic/rubyonrails-security/IsQKvDqZdKw)

* * *

☝️ ***一定要看看 [Sharesecret](https://www.sharesecret.co) ，它让安全共享敏感数据变得很容易。***