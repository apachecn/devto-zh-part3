# Laravel 电子商务应用程序开发–使用 Laravel Mix 设置资产

> 原文：<https://dev.to/larashout/laravel-e-commerce-application-development-assets-setup-using-laravel-mix-1k43>

这是 Laravel 电子商务应用程序开发教程系列的第二部分，在这一部分中，我们将使用 Laravel Mix 设置静态资产。

您的机器上应该有您的电子商务应用程序项目，这是我们在本系列的最后一部分中设置的。

。首先，我们必须在我们的。gitignore 文件。这个文件用于列出我们不想添加到 Git 存储库中的所有文件夹和文件。提前考虑要从 Git 中排除的文件夹和文件总是很有帮助的。

让我们开始，我们知道我们将在这个系列中建立后端(管理部分)和前端(商店网站)。因此，我们必须管理我们的后端资产和前端资产。你想如何管理你的资产完全取决于你自己。我将展示我将如何管理这个项目的资产(通常我使用项目的模块化结构，但这个系列是为了帮助新手，所以我会保持简单)。

话虽如此，我们将不得不在我们的 public/ folder 中为静态资产管理两个名为 backend 和 frontend 的文件夹(您可以随意命名它们)。

此外，我们将有另一个文件夹名为上传，我们将从后端上传我们所有的产品和其他图像。

打开你。git 忽略应用程序根目录下的文件，并在其中添加几行代码。

请在[https://www . larashout . com/laravel-电子商务-应用-开发-资产-设置-使用-laravel-mix](https://www.larashout.com/laravel-e-commerce-application-development-assets-setup-using-laravel-mix) 阅读全文