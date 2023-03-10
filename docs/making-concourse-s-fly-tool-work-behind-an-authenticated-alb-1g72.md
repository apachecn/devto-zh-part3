# 让 Concourse 的 fly 工具在经过认证的 ALB 后面工作

> 原文：<https://dev.to/sirech/making-concourse-s-fly-tool-work-behind-an-authenticated-alb-1g72>

我们最近在 *AWS* 中一个经过认证的 [ALB](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html) 后面放了一个 *Concourse CI* 实例，以确保有两个不同的登录发生，而不必求助于使用堡垒主机。

其工作原理是，每当你试图访问分配给*广场*的 URL 时，就会有一个重定向将你发送到身份提供商。一旦你登录到那里(希望使用 MFA ),一个名为`AWSELBAuthSessionCookie-0`的 cookie 就会被设置在你的浏览器中，然后你将再次访问这个工具。

实现这一点的代码相当简单。你在你的`IdP`里设置了一个新的应用，从中你得到一个`client_id`和一个`client_secret`。然后在 *ALB 中设置一个检查*使用`terraform`T6】

```
resource "aws_lb_listener" "web-alb" {
  load_balancer_arn = "${aws_lb.web-alb.arn}"
  port = "443"
  protocol = "HTTPS"
  ssl_policy = "ELBSecurityPolicy-TLS-1-2-2017-01"
  certificate_arn = "${aws_acm_certificate_validation.web.certificate_arn}"

  default_action {
    type = "authenticate-oidc"

    authenticate_oidc {
      authorization_endpoint = "${var.idp_url}/oidc/auth"
      client_id = "${var.client_id_oidc}"
      client_secret = "${var.client_secret_oidc}"
      issuer = "${var.idp_url}/oidc"
      token_endpoint = "${var.idp_url}/oidc/token"
      user_info_endpoint = "${var.idp_url}/oidc/me"
    }
  }

  default_action {
    type = "forward"
    target_group_arn = "${aws_lb_target_group.web-alb.arn}"
  }
} 
```

然而，这有一个意想不到的后果。`fly`也通过该 URL 访问*中央大厅 CI* ，它不能处理重定向。您不应该在本地触发很多事情，因为这应该由管道本身来覆盖。当你创建一个新的管道时，你仍然需要第一次上传一个管道，并且你也想使用像`intercept`这样的命令，所以在这个设置中打开这个选项是很好的。

本身并没有多大帮助，所以我们不得不寻找一个变通方法。我们所做的是构建一个本地代理，它将请求从`fly`转发到 *ALB* ，并在请求中注入 cookie 以使其工作。这个代理就是带有参数化配置的`nginx`，运行在一个`Docker`容器中。值得注意的一件有趣的事情是，我们必须为`intercept`设置一些头，因为它依赖于 *websockets* 。

```
events {
    worker_connections 16;
}

http {
    server {
        listen ${CONCOURSE_PROXY_PORT};
        server_name localhost;

        location / {
            proxy_pass ${CONCOURSE_URL};
            proxy_set_header Cookie AWSELBAuthSessionCookie-0=${COOKIE};

            # Fix `websocket: bad handshake` when using `fly intercept`
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }
    }
} 
```

这个配置在容器的`CMD`中被转换成一个有效的配置，比如:

```
#! /usr/bin/env bash

set -e

# shellcheck disable=SC2016
envsubst '$COOKIE $CONCOURSE_URL $CONCOURSE_PROXY_PORT' < /tmp/nginx.conf.template > /etc/nginx/nginx.conf
nginx -g "daemon off;" 
```

我们使用`docker-compose`运行容器，并在一个`.env`文件中提供`COOKIE`。

## 连接到中央大厅

一旦*代理*运行，我们只需要用`fly`登录。我们直接从我们的`go`脚本:

```
goal_login-to-concourse() {
  fly --target aftersales login --concourse-url "http://localhost:$CONCOURSE_PROXY_PORT"
} 
```

这就是我们看到的这个解决方案的一个难点。当您尝试登录时，您会看到类似这样的内容:

```
navigate to the following URL in your browser:

  http://localhost:3232/sky/login?redirect_uri=http://127.0.0.1:50449/auth/callback

or enter token manually: 
```

那个网址不行。您需要用 *Concourse* 服务器的实际 URL 替换`localhost`部分。我们真的找不出一种方法让它变得更加用户友好，所以我们只需要处理它。

获取 cookie 也有点麻烦，因为您需要从浏览器中手动获取它，并将其放在`.env`文件中。我尝试用编程的方式获取这个 cookie，但是我无法从 Chrome 上获取解密的 cookie，所以我暂时就这样了。

基本上就是这样。老实说，这有点像黑客，但是它完成了工作，而不需要对我们所追求的两次登录设置做出妥协。