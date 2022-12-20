# 先失败/快速失败

> 原文：<https://dev.to/mjb2kmn/failing-first--failing-fast-4gik>

# 先衰而快衰

当编写新代码时，我通常首先关注成功的路径，这似乎总是导致糟糕的代码。第一遍糟糕的代码并不是真正的问题，大脑转储代码通常会产生糟糕的代码，至少对我来说是这样。重要的是大脑转储代码被跟踪一次或多次以简化逻辑。这些经过往往是在几个月甚至几年之后。

示例案例是使用 [Lua 和 Nginx](https://github.com/openresty/lua-nginx-module) 验证 cookie 中的单点登录(SSO)令牌。

我的第一张通行证包括了这样的东西:

*为便于说明，简化了代码*

```
function check_sso()
  local cookies = ngx.req.get_headers()['Cookie']
  if cookies then
    local token = cookies:match('sso_token=([^; ]+)')
    if token then
      local username = get_username_from_token(token)
      if username then
        if is_member_of(username, GROUP_NAME) then
          return true
        else
          ngx.log(ngx.WARN, username .. ' is not in group ' .. GROUP_NAME)
          return false
        end
      end
    end
  end
end 
```

当你通读它的时候，它遵循一个简单的逻辑，但是它看起来很可怕，并且很容易失去你在嵌套中的位置。由于嵌套块的长度，具有调试日志记录和令牌缓存的实际代码更差。

考虑一下当我们首先处理支票失败的一方时会是什么样子:

```
function check_sso()
  local cookies = ngx.req.get_headers()['Cookie']
  if not cookies then
    return false
  end
  local token = cookies:match('sso_token=([^; ]+)')
  if not token then
    return false
  end
  local username = get_username_from_token(token)
  if not username then
    return false
  end
  if is_member_of(username, GROUP_NAME) then
    return true
  else
    ngx.log(ngx.WARN, username .. ' is not in group ' .. GROUP_NAME)
    return false
  end
end 
```

尽管有更多的行，我发现这个版本更简单，视觉上更容易阅读。当我回到像这样编写的代码时，我发现修改和排除故障要容易得多。然而，我仍然在努力修改我的思维，以便首先使用这种类型的逻辑。

有没有人在不同于最佳代码逻辑的思维过程中遇到过类似的问题？

有没有人不同意，认为第一个例子比第二个好或者至少不比第二个差？