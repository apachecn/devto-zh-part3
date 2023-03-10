# 限制用户访问运行在 nginx 上的网站

> 原文：<https://dev.to/sm0k3/restriction-of-user-access-to-the-website-running-on-nginx-31o4>

[![alt text](img/98ec3cc25adb60ba81b19b6798fba1c8.png "Limiting access to your website with Nginx")](https://res.cloudinary.com/practicaldev/image/fetch/s--C-6CfYek--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nenhxsrf57qvuj5w0w0p.jpeg)

我在自由职业者网站上遇到的一个关于 Nginx 的有趣问题是，你需要限制用户对网站的访问，这样他们只能从搜索引擎进入。从即时通讯，其他网站和直接访问该网站的过渡需要被禁止，反过来，搜索机器人被允许索引该网站没有任何障碍(直接访问，从其他网站转移，等等)。即不受限制地工作)。

服务器在 Ubuntu 上，Nginx 作为网络服务器，网站在 WordPress 上运行，总的来说，没什么新的。实现应该是在 web 服务器级别，使用 nginx 工具，有必要控制谁来自用户或搜索机器人，并为后续操作发出适当的指令——访问站点或发送到存根。

在站点配置文件级别对 nginx 进行了更改，比如针对单个目标主机的 mysite.conf。

为了更正确地确定谁是谁以及他来自哪里，使用了参数“$ http_referer”和“$ http_user_agent”、正则表达式、bot 代理的标准列表、自定义错误页面、rewrite 来为它们中的每一个添加几个条件，输出的是一个长度为 12 行代码的条件(还可以删减一点)。

nginx 规则的逻辑如下:

*   如果用户直接或不通过搜索引擎访问网站，给他分配一个标记“a”
*   如果这不是一个搜索机器人，分配标记“b”
*   我们检查标记的存在，如果两者都存在，那么它是用户移动到一个直接的或从另一个站点，例如，分别地，我们发送它到存根。现在我们来详细看看规则。

**第一个条件:**

```
if ($ http_referer ~ * "^ $ | ^ ((?! (google | yandex | bing | yahoo | mail | duckduckgo) \. [a-z] +).) * $") {
  set $ marker a;
} 
```

Enter fullscreen mode Exit fullscreen mode

什么是什么？一切都很简单，我们要求 nginx 检查每个用户来自哪里的推荐人。通过条件:“^ $”执行一个空的参考是否-这是如何直接访问该网站的决定。接下来，我们尝试从该行的开头确定用户不是来自 google、yandex 和其他搜索引擎(在正则表达式中，构造？！意思是没有)嗯，和他们相应的清单。

我决定只列出域名，而不列出它们的结尾，对于互联网的不同部分，结尾可能会有所不同，因此使用了[[az] +"类型的更通用的解决方案，这意味着将会有小写字母，也可能有少或多(从 1 到 1...很多)。结尾的点表示任何事情都可以更进一步。前面还有一个点——它被“\”符号转义，以便被正确定义，就像一个实点，而不是任何东西。

如果这个条件满足，那么我们设置变量$ marker 值“a”并继续。

**第二个条件:**

这里的一切都简单多了，我们需要**通过代理**定义一个机器人或用户，它们是机器人中的标准，它们不会改变，因此它们被选为检查的例子(此外，它们的数量较少)。该条件规定，如果“用户代理”参数与 Google、Yandex 等的掩码不匹配。-这意味着它不是机器人。

```
if ($ http_user_agent! ~ * "googlebot | yandex | yahoo | bing") {
set $ marker "$ {marker} b";
} 
```

Enter fullscreen mode Exit fullscreen mode

在条件的最开始使用感叹号指示差异被检查，星号不是严格的条件，然后相应地列出来自不同搜索引擎的搜索引擎的可用代理。

如果这个条件满足，那么我们将第二个值赋给$ marker 变量。使用$ {marker} b 结构是为了使第一次检查的数据不会丢失，也就是说，我们只是用第二个条件的数据来补充它。

当然，在所有这些验证的最后，我们只能通过一个简单的比较来验证我们条件的有效性:

```
if ($ marker = ab) {
return 403;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果变量的值在所有执行的操作之后等于值“ab ”,那么一切工作正常，我们分别确定用户，以及他直接或不从搜索引擎访问站点的事实，并且您需要对它应用特殊的措施，将它发送到存根站点。

另请参见自由职业者任务:ISP 管理器中自动更新 SSL 证书的问题
管理员已经有了自己的判断，您可以设置任何错误的代码，例如 403 或注册一个重定向到自定义页面。

在我的例子中，完整的解决方案如下:

```
if ($ http_referer ~ * "^ $ | ^ ((?! (google | yandex | bing | yahoo | mail | duckduckgo | website) \. [a-z] +).) * $") {
 set $ bot a;
}

if ($ http_user_agent! ~ * "googlebot | yandex | yahoo | bing") {
 set $ bot "$ {bot} b";
}
if ($ bot = ab) {
 rewrite ^ / (. *) $ https://website.com/index.html permanent
} 
```

Enter fullscreen mode Exit fullscreen mode

有一个“网站”，是客户的网站，让用户在网站内自由活动。