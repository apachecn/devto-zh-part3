# 如何在任何服务器上显示当前的 HTTP 请求

> 原文：<https://dev.to/codeenigma/how-to-show-current-http-requests-on-any-server-mmp>

如果您需要查看传入的 HTTP 请求，可能需要检查传入的头，您想知道正在设置什么 cookies，或者想获得一些线索，了解为什么没有缓存内容，下面的命令可能会有所帮助:

tcpdump -A -s 10240 'tcp 端口 80 和((ip[2:2] - ((ip[0]&0xf) <<2)) - ((tcp[12]&0xf0)> >2))！= 0)' | egrep - line-buffered "^........(get | http \/| post | head )|^[a-za-z0-9-]+: | sed-r 's/^........(GET |HTTP\/|POST |HEAD )/\n\1/g '

这有点拗口，但是它显示了直接从网络接口传入的 HTTP 请求，并以人类可读的方式格式化它们。好的一面是，您可以在输出中使用 grep 来显示诸如传入的 cookies 或请求头之类的内容。对于监控请求来说，可能比查看日志文件和猜测更有用。

希望对人有帮助！