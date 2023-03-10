# 创建一个 Web 服务器，并使用 PHP 将表单数据保存到 MySQL 数据库中(初学者指南)

> 原文：<https://dev.to/satellitebots/create-a-web-server-and-save-form-data-into-mysql-database-using-php-beginners-guide-fah>

静态页面非常适合展示你的项目。然而，如果你想从客户端获取数据并保存到服务器上，或者允许客户端从你的服务器上获取数据，那么你就需要一个动态网页。在本教程中，我将向你展示如何建立一个网络服务器，收集数据并保存到服务器数据库中。

### 关键概念

在我们开始之前，让我们回顾一些 HTTP 方法。从服务器获取请求数据(文本/文件)。POST 请求服务器接受数据(文本/文件)进入服务器。将修改指定资源的请求放到服务器中。DELETE 请求服务器删除指定的资源。我们将只使用邮政，并把在本教程。

### 环境设置

我们将不得不设置服务器之前，我们可以开始为网站编码。有许多其他语言和数据库可以用于此目的，但是本教程将只涵盖 PHP、MySQL 数据库和 Apache web 服务器。我们将讨论两种方法， [Linux](#linux-ubuntu) 和 [Windows](#windows) 。我推荐使用 Linux，因为它更容易上手。如果你已经有了环境设置，那么进入[编码](#code)模块。如果你没有 Linux 环境设置，那么我推荐你使用 virtual-box 来设置一个基本的 Linux VM 并在上面运行 Ubuntu。

### 窗口

首先在 Windows 中，继续从这里安装 WAMP 服务器[。这将需要一段时间，因为你将不得不安装许多软件包。请确保 readme.txt 文件中的所有 Visual C++包都已安装，然后再继续，因为这可能会在以后造成很多麻烦。安装完成后，请打开浏览器，然后转到不带引号的“localhost”。如果你看到一个网页，那么你就可以走了。我们将在 wamp 目录(安装路径)内的 www/html 文件夹中工作。在右上角，您会看到一个绿色的 W 图标，表示服务器处于在线状态并且正在工作。
](http://www.wampserver.com/en/)[![](img/162e50b4a96766ab65bc34dbdc306a63.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cDKV8EsS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vdmvihss0m0zcz3e2bka.JPG) 
如果你还不确定如何设置 wamp 服务器，我推荐你看看这个视频。

### Linux (Ubuntu)

如果您有不同的发行版，那么请使用适当的软件包管理器来安装必要的工具。首先安装 Apache2。
`sudo apt-get install apache2`
然后保证服务运行。
T1】

然后安装 mysql 并完成设置。请记住您的 root 密码。
T0
T1】

然后安装 php，我们应该准备好了。【T3`sudo apt-get install php`
`sudo apt-get install php-mysql`
`sudo /etc/init.d/apache2 restart`

请确保你安装了 php-mysql 软件包，因为没有它你很可能会得到驱动程序错误。

我们的工作目录是/var/www/html。

### Apache2

为了创建新项目，您将创建新的。将存储虚拟主机配置的每个项目的 conf 文件。请查看现有的配置文件以更好地理解这一点。对于 Linux，这个文件可以在/etc/apache2/sites-available/目录中找到。请检查名为 000-default.conf 的文件(如果有),否则只读取任何文件。conf 文件，以便理解这种格式。

### 代码

一旦一切都设置好了，请在 html 文件夹中创建一个新文件(如果您更改了 Apache 配置，请在您的工作目录中创建)。
我们将从在 html 目录下创建一个新文件开始。
`sudo touch /var/www/html/form.html`
然后继续编辑文件，用一些基本的 html 代码填充它。
`sudo nano /var/www/html/form.html`

```
<head>

Test Page

</head>
<body>
<form action="form_submit.php" class="alt" method="POST">
<div class="row uniform">
<div class="name">
<input name="name" id="" placeholder="Name" type="text">
</div>
<div class="email">
<input name="email" placeholder="Email" type="email">
</div>
<div class="message">
<textarea name="message" placeholder="Message" rows="4"></textarea>
</div>
</div>
<br/>
<input class="alt" value="Submit" name="submit" type="submit">
</form>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

### 数据库

现在让我们将数据库设置为，以便我们可以存储信息。我将只介绍在 Linux 中创建数据库和表。首次登录
`mysql -u root -p`

```
create database dev_to;
use dev_to;
create table test(
    id int NOT NULL AUTO_INCREMENT,
    name varchar(255),
    email varchar(255),
    message text,
    PRIMARY KEY (id)
); 
```

Enter fullscreen mode Exit fullscreen mode

让我们确保这行得通。
`describe test;`
输出应该是什么样子:
[![](img/b318c8becbfad5577a39d2d14230ae8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5O-FGe6n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qbbr2tvv6zgn9upirlav.JPG)

我们出去吧。
`exit`
现在我们终于准备好 PHP 代码来处理数据并保存到数据库中。

### 表单处理

首先创建一个名为 form_submit.php.
`sudo touch /var/www/html/form_submit.php`
的新文件，现在让我们用一些代码填充它。
`sudo nano /var/www/html/form_submit.php`
现在尝试下面的代码，它应该将数据持久存储到数据库中指定的表中。

```
<?php
$host = "localhost";
$db_name = "dev_to";
$username = "root";
$password = "password";
$connection = null;
try{
$connection = new PDO("mysql:host=" . $host . ";dbname=" . $db_name, $username, $password);
$connection->exec("set names utf8");
}catch(PDOException $exception){
echo "Connection error: " . $exception->getMessage();
}

function saveData($name, $email, $message){
global $connection;
$query = "INSERT INTO test(name, email, message) VALUES( :name, :email, :message)";

$callToDb = $connection->prepare( $query );
$name=htmlspecialchars(strip_tags($name));
$email=htmlspecialchars(strip_tags($email));
$message=htmlspecialchars(strip_tags($message));
$callToDb->bindParam(":name",$name);
$callToDb->bindParam(":email",$email);
$callToDb->bindParam(":message",$message);

if($callToDb->execute()){
return '<h3 style="text-align:center;">We will get back to you very shortly!</h3>';
}
}

if( isset($_POST['submit'])){
$name = htmlentities($_POST['name']);
$email = htmlentities($_POST['email']);
$message = htmlentities($_POST['message']);

//then you can use them in a PHP function. 
$result = saveData($name, $email, $message);
echo $result;
}
else{
echo '<h3 style="text-align:center;">A very detailed error message ( ͡° ͜ʖ ͡°)</h3>';
}
?> 
```

Enter fullscreen mode Exit fullscreen mode

现在，您的网站应该能够处理表单并将其保存到本地数据库中。

下面是我的测试运行的一个示例输出:
[![](img/00856a49992f6473e05f05bb0fbfff42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i3B9lzWN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wcx80hu1apd8f642wtkg.JPG)
[![](img/d73906a88480f65065b2b925a1f1e486.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fAyBTWGw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q6xdix7juj2eanzea6hf.JPG)
[![](img/3c46b94a6364f226575eaa34330d30eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q89AuY-P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1tei1bl9abdgfahmloqe.JPG)

注:请让我知道你有任何问题。本教程旨在帮助人们开始他们的动态 web 开发之旅。如果有任何不清楚的地方，或者任何信息不准确，请让我知道，这样我可以尽快纠正。