# 带有正文参数的 WebAPI Post

> 原文：<https://dev.to/chriscooper01/webapi-post-with-body-parameters-21be>

前几天，我需要为第三方创建一个 POST API (WebApi)来消费和返回数据，很简单！事情并不像我最初想的那么简单，因为他们无法访问我的 API，并且得到了*“没有找到与请求 URI 匹配的 HTTP 资源”*。这取决于我创建 API 的方式和他们发送 POST 数据的方式。

通常我用方法中的参数创建 PPOST WebAPI，你用 URL 发送参数。(*获取参数*)他们在帖子正文中发送参数，而不是在 URL 中，所以他们得到了错误。所以我需要移除参数，并从 **RequestContext** 中获取参数。

HTTP Post 方法

这是不带任何参数的 **POST** 方法，从 HTTP POST 请求中获取请求上下文。

`[HttpPost] public bool DeliveryReceipts() { var value = this.Request.GetRequestContext(); getPostContent(Request.Content); return true; }`
Reading the context

Now that the context has been collected from the request. This method gets a string key\value for the parameters, sent in the request.

`private async void getPostContent(HttpContent content) { DataClassesRequestParameterDataClass request = newDataClassesRequestParameterDataClass(); var contentString = await content.ReadAsStringAsync(); var contentElements = contentString.Split('&'); foreach(var element in contentElements) { var parameter = element.Split('='); if(parameter.Count()>0) { setDataClass(parameter[0], parameter[1], ref request); }//END if(parameter.Count()>0) }//END foreach(var element in contentElements) }`
Collecting the value

This is a simple method that sets a veritable from the key found within the key\value string.

`private void setDataClass(string parameter, string value, ref DataClassesRequestParameterDataClass request) { switch(parameter.ToLower().Trim()) { case "number": request.Number = value; break; case "userid": request.UserId = value; break; case "datetime": request.DateTime = value; break; case "status": request.Status= value; break; } }`

总结

我从来没有使用过这种创建 post 方法的技术，但是这似乎比在方法中设置参数更安全，因为没有人知道需要哪个参数来运行这个方法。

我敢肯定这不会是最后一次，我用逻辑换了一个贴法。