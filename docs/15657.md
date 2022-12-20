# Bcrypt 如何比较密码

> 原文：<https://dev.to/amree/how-bcrypt-compares-password-pb9>

在数据库中创建密码:

```
> password = 'secret'
> encrypted_password_in_database = BCrypt::Password.create(password) 
```

Enter fullscreen mode Exit fullscreen mode

比较密码:

```
> BCrypt::Password.new(encrypted_password_in_database) == 'secret'
=> true 
```

Enter fullscreen mode Exit fullscreen mode

`==`实际上是在 [bcrypt-ruby](https://www.rubydoc.info/github/codahale/bcrypt-ruby/BCrypt%2FPassword:==) 中定义的方法

Devise 正在使用类似于常数时间安全比较的方法进行比较，但是 bcrypt-ruby 项目决定不采用这种方法。点击此处了解更多信息:

*   [https://github.com/codahale/bcrypt-ruby/issues/42](https://github.com/codahale/bcrypt-ruby/issues/42)
*   [https://github.com/codahale/bcrypt-ruby/pull/43](https://github.com/codahale/bcrypt-ruby/pull/43)
*   [https://github.com/codahale/bcrypt-ruby/pull/119](https://github.com/codahale/bcrypt-ruby/pull/119)