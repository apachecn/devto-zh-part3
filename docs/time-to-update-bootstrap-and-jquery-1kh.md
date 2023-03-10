# 是时候更新引导程序和 JQuery 了！

> 原文：<https://dev.to/bbossola/time-to-update-bootstrap-and-jquery-1kh>

**Bootstrap**
Bootstrap v 4 . 3 . 1 和 v3.4.1 [已发布，可用于](https://blog.getbootstrap.com/2019/02/13/bootstrap-4-3-1-and-3-4-1/)修补 XSS 漏洞， [CVE-2019-8331](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-8331) 。对于任何传统 3.3.7 的用户，这也将修复其他三个 XSS 问题，即 [CVE-2018-14040](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-14040) 、 [CVE-2018-14041](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-14041) 和 [CVE-2018-14042](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-14042) 。Bootstrap 现在包含一个 JavaScript 杀毒程序，它只允许元素的数据属性中包含白名单中的 HTML 元素。

它可以通过所有渠道获得:作为 [NPM 包](https://www.npmjs.com/package/bootstrap)，[通过 CDNs](https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/3.4.1/js/bootstrap.min.js) ，对于老式的家伙也作为[直接从 Github](https://github.com/twbs/bootstrap/releases/tag/v3.4.1) 下载。

**JQuery**
还有，请不要忘记 JQuery！3.4.0 之前的版本容易受到原型污染攻击(参见 [CVE-2019-11358](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-11358) ):即使攻击相当复杂，也建议升级任何使用 jQuery 代码作为其前端的 web app。

你可以通过 CDNs 找到 [NPM 套餐](https://www.npmjs.com/package/jquery)或者

**结论？**
你现在没有任何借口:立即升级！