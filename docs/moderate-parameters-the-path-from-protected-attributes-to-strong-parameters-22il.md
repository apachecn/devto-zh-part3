# 适度参数:从受保护属性到强参数的路径

> 原文：<https://dev.to/hint/moderate-parameters-the-path-from-protected-attributes-to-strong-parameters-22il>

*最初发表于 [Hint 的博客](https://hint.io/blog/moderate-parameters)。*

我们都知道强参数在 2019 年并不是尖端技术。它最初是在 Rails 3.2 中引入的(2012 年 3 月 20 日)，在 4.0 中成为默认/标准(2013 年 6 月 25 日)。

[![image](img/0552aec7b3aae4dc7a2d9035473ddbd9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--itirl5W6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9qyd7fvs06wbjrabzowu.png)

即使后视镜中有 7 年的强大参数，仍有以`protected_attributes` gem 形式存在的传统安全支持。然而，随着 Rails 5 的发布，这颗宝石正式无人维护。

根据我们帮助客户迁移到最新最好版本的 Rails 的经验，这仍然是许多团队面临的一个很大的技术障碍。

考虑到这一点，我们开发了一个工具，可以安全可靠地迁移到强参数。

我们称之为适度参数。

## 它是这样工作的

适度参数是一个通过扩展`ActionController::Parameters`功能在控制器中提供安全网和数据源记录的工具。

将 gem([repo](https://github.com/hintmedia/moderate_parameters)/[ruby gems](https://rubygems.org/gems/moderate_parameters))添加到您的应用程序的 gem 文件:

```
gem 'moderate_parameters' 
```

Enter fullscreen mode Exit fullscreen mode

然后，在推荐的强参数私有方法中，用`moderate`替换`permit`，并添加一些上下文:

*(注:* `controller_path` *和* `action_name` *需要包含如图。它们是为记录器提供命名上下文的方法。)*

```
class UsersController < ActionController::Base

. . .

private

# def user_params
#   params.require(:user).permit(:email, :first_name, . . .)
# end

  def user_params
    params.require(:user).moderate(controller_path, action_name, :email, :first_name, . . .)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

和...就是这样。

中等参数将开始记录尚未列入白名单的控制器上下文和参数密钥。这意味着，在上面的例子中，如果您在创建用户时忘记将`:last_name`包含在允许的参数列表中，您的日志将如下所示:

```
Users#create is missing :last_name from the permitted parameters 
```

Enter fullscreen mode Exit fullscreen mode

## 考试喜欢你飞

我们已经构建了可部署到生产中的适度参数。这样，我们可以确保整个 Rails 应用程序中的每个控制器都考虑到了所有参数。数据仍然会像预期的那样流经应用程序，额外的好处是可以清晰地描绘出向强参数移动的情况。

一旦完整的参数列表被清晰地列出(意味着您的中等参数日志是空的)，切换到强参数就像用`permit` :
替换`moderate`一样简单

```
class UsersController < ActionController::Base

. . .

private

  def user_params
    params.require(:user).permit(:email, :first_name, :last_name)
  end

# def user_params
#   params.require(:user).moderate(controller_path, action_name, :email, :first_name, :last_name)
# end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 我们可以帮忙！

我们已经构建了一组服务，可以在生产中与中等参数一起运行，以程序化地解析和生成强参数应该是什么样子。

如果强大的参数或类似的东西阻碍了您或您的团队享受最新的 Rails 安全性和特性，让我们帮助您确保成功。我们的团队有几十年的经验，帮助团队利用 Rails 提供的所有优势，将难以管理的升级转变为尖端产品。