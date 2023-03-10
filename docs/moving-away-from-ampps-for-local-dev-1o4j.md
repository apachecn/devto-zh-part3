# 从 AMPPS 转向本地开发

> 原文：<https://dev.to/davinaleong/moving-away-from-ampps-for-local-dev-1o4j>

自从我开始 web 开发以来，我一直使用 [WAMP](http://www.wampserver.com/en/) 和后来的 [AMPPS](https://www.ampps.com/) ，这只是 WAMP/ [MAMP](https://www.mamp.info/en/) 的 UI 更好。但是我觉得我一直认为环境设置是理所当然的，我想远离它。

自从我在工作中被介绍给 [Docker](https://www.docker.com/) 后，我一直想学习如何建立自己的本地开发环境。在工作时，技术总监会做设置过程，而我只是运行它。但是我很好奇，想看看不同的部分是如何组合在一起的，以便我更好地理解 web 开发的部署方面。这也有助于我在设置过程中更有效地解决问题。

我最初试着在我现有的 Windows 机器上做。所以我去买了一个 MBP，因为 Mac 已经预装了 PHP 和 MySQL。此外，基于 Unix 也使得运行`bash`命令和脚本变得更加简单！

我没有直接进入 Docker，而是决定从 simple 开始，看看是否可以运行一个本地开发服务器。经过 3 天的反复试验和大量的谷歌搜索，终于设法让一个现有的项目运行代客。

通过整个过程，我学会了如何使用[家酿](https://brew.sh/)和[代客](https://laravel.com/docs/5.7/valet)我面临的最大挑战是试图让 Sequel Pro 连接到本地主机 MySQL。

下一个设置是弄清楚如何为典型的 LEMP 设置编写 Docker 脚本。然后弄清楚如何专门为 [Laravel](https://laravel.com/) 和 [Craft](https://craftcms.com/) 设置本地开发环境。我知道有[拉腊多克](https://laradock.io/)给拉腊维尔...这也适用于 Craft(或 CraftCMS)吗？