# 不要将“sudo”与 NPM“install”一起使用

> 原文：<https://dev.to/brylie/don-t-use-sudo-with-npm-install-56p9>

安装 NPM 软件包时使用“sudo”可能会给不受信任的代码提供管理权限，从而使您的计算机面临风险。请不要使用“sudo”安装 NPM 软件包。

相反，使用以下说明允许 NPM 在没有管理权限的情况下安装全局软件包:

[https://docs . npmjs . com/resolving-eacces-permissions-errors-when-installing-packages-globally](https://docs.npmjs.com/resolving-eacces-permissions-errors-when-installing-packages-globally)

* * *

[![Creative Commons License](img/e59cd93fa004ceb14ed402ec095ed201.png)](http://creativecommons.org/licenses/by/4.0/) 
本作品获得[知识共享署名 4.0 国际许可](http://creativecommons.org/licenses/by/4.0/)的许可。