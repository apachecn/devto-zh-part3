# cloud Upload-从 cloudinary 平台上传和获取图像和视频

> 原文：<https://dev.to/nanavekta/cloudy-upload---upload-and-fetch-images-and-videos-from-the-cloudinary-platform-g8e>

使用 Cloudinary，您可以将图像和视频上传到云，然后通过内容交付网络(CDN)将其加载到您的应用程序中。本教程解释了如何构建一个简单的应用程序，将图像或视频上传到 Cloudinary 平台，然后将它们加载回应用程序。作为先决条件，您必须熟悉编程语言 PHP、HTML 和 CSS。我们还建议您在逐步完成本教程之前安装一个本地服务器，如 XAMPP 或 WampServer。

# 建筑界面

首先，创建应用程序的前端:

1.  如果您的本地服务器是 XAMPP，则在`htdocs`文件夹中创建一个文件夹；如果您的本地服务器是 WampServer，则在`www`文件夹中创建一个文件夹。将新文件夹命名为`cloudy-upload`。
2.  打开`cloudy-upload`然后右击从上下文菜单中选择新建>选择文本文档。
3.  将文本文档命名为`index.html`。
4.  打开 IDE，将以下代码添加到`index.html`文件中:

```
<!DOCTYPE html>
<html lang="en">
<head>
   <meta charset="UTF-8">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <meta http-equiv="X-UA-Compatible" content="ie=edge">
   Cloudy Upload
   <style>
      .container{
         display: flex;
         flex-direction: column;
         align-items: center;
       }
.form-group {
    margin-bottom: 15px;
}
.form-control {
 display: block;
 width: 100%;
 height: 34px;
 padding: 6px 12px;
 font-size: 14px;
 line-height: 1.42857143;
 color: #555;
 background-color: #fff;
 background-image: none;
 border: 1px solid #ccc;
 border-radius: 4px;
 -webkit-box-shadow: inset 0 1px 1px rgba(0, 0, 0, .075);
         box-shadow: inset 0 1px 1px rgba(0, 0, 0, .075);
 -webkit-transition: border-color ease-in-out .15s, -webkit-box-shadow ease-in-out .15s;
      -o-transition: border-color ease-in-out .15s, box-shadow ease-in-out .15s;
         transition: border-color ease-in-out .15s, box-shadow ease-in-out .15s;
}
      </style>
</head>
<body>
<div class="container">
   <img src="https://res.cloudinary.com/cloudinary/image/upload/c_scale,w_200/v1/logo/for_white_bg/cloudinary_vertical_logo_for_white_bg.png">
   <h1>CloudyUpload</h1>
   <p>Upload your image or video to Cloudinary</p>

<form enctype="multipart/form-data" action=”upload.php”>
<div class=”form-group”>
<input type="file" class="form-control" id="attachment" name="attachment" placeholder="Select Attachment">
</div>
<br>
<button type="submit" id="submitData" name=”submitData”> Submit </button>
</form>
       </div>
</body>
</html> 
```

然后，在浏览器中找到该文件。将显示以下页面:

[![cloudy upload form](img/3eadd864db47e7c6f82353e95806755e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vEhRE7Ms--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pesewawebsoft.cimg/cloudinary.JPG)

此时，单击**提交**会导致没有响应。

# 为公共 id 和文件类型创建数据库

接下来，创建一个数据库来存储您的视频和图像的公共 id 和文件类型:
在您的浏览器上转到`localhost/phpmyadmin`，单击顶部的 **SQL** 选项卡，并在顶部的文本字段中键入以下代码:

```
CREATE DATABASE *cloudy_upload*; 
```

[![database creation](img/434523ea37cf249dbb348fa16db7436a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xpNFPZa7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pesewawebsoft.cimg/databse.JPG)

点击左侧导航栏上的`cloudy_upload`，然后点击顶部的 **SQL** 选项卡。在顶部的文本字段中输入该代码:

```
CREATE TABLE resources(
    ID int NOT NULL PRIMARY KEY,
    FileType varchar(255),
    PublicID varchar(255),
); 
```

[![table creation](img/78fc03b9e0f9152744eb6b58d32dc2c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sOv82k_R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pesewawebsoft.cimg/database2.JPG)

# 构建后端并连接到 Cloudinary API

现在，在与`index.html`相同的文件夹中创建一个文件，将新文件命名为`upload.php`，并向其中添加以下代码:

```
<?php
$host = "localhost";
$user = "YOUR-DATABASE-USERNAME"; 
$password = "YOUR-DATABASE-PASSWORD";

   $con = mysqli_connect($host,$user,$password);
   mysqli_select_db($con,"cloudy_upload") OR DIE('could not connect to database');

\Cloudinary::config(array(
   "cloud_name" => "YOUR-CLOUD-NAME",
   "api_key" => "YOUR-CLOUDINARY-API-KEY",
   "api_secret" => "YOUR-CLOUDINARY-API-SECRET"
));

if(isset($_FILES['file'])){
   $fileName = $_FILES['file']['name'];
   $ext = pathinfo($fileName, PATHINFO_EXTENSION);
   $tmpName = $_FILES['file']['tmp_name'];
   $fileType = $_FILES['file']['type'];
   $fileName = addslashes($fileName);

   $public_id = "myT".time().".".$ext;

   if($_FILES['file']['error'] > 0){
       echo 'There is an error in your file';
   }
   else{
       $insert = mysqli_query($con, "INSERT INTO resources(FileType, PublicID) VALUES ('$fileType', '$public_id')");
       if($insert){
           \Cloudinary\Uploader::upload_large($tmpName,
               array("resource_type" => "auto",
                   "public_id" => $public_id));
           echo 'success! File uploaded';
       }
       else{
           echo 'error! File upload failed';
       }
   }
}
else{
   echo 'Kindly select a file';
} 
```

**注意:**确保将变量`YOUR-DATABASE-USERNAME`和`YOUR-DATABASE-PASSWORD`替换为它们的实际值，即数据库的用户名和密码。对`YOUR-CLOUD-NAME`、`YOUR-CLOUDINARY-API-KEY`、`YOUR-CLOUDINARY-API-SECRET`进行同样的操作。

接下来，通过调用 PHP `mysqli_connect()`函数连接到您的数据库，然后通过使用您的凭证登录到您的 Cloudinary 帐户来连接到 Cloudinary API。

**注意:**要获得您的帐户凭证，[注册](https://cloudinary.com/signup)一个免费的 Cloudinary 帐户:

[![cloudinary console](img/e65352e63af843d8a6b4906ee47c5bc3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7VL6fiDO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pesewawebsoft.cimg/console.png)

[![cloudinary console](img/a0cfec69e252519777630f47ddc77ca7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Et-qU2eE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pesewawebsoft.cimg/dashboard.png)

一旦建立了这两个连接，当您选择一个文件进行上传时，Cloudinary 就会获得它的名称、类型和大小，然后为该文件创建一个公共 ID。该 ID 是文件在 Cloudinary 平台上的唯一标识，与相关的文件类型一起保存在 Cloudinary 数据库中。

**注意:**如果文件有问题，Cloudinary 会显示错误信息。

要上传视频或图像到 Cloudinary，调用 Cloudinary 的`upload_large`方法，因为文件可能很大。此外，将`resource_type`设置为`auto`，为上传视频和图像铺平道路。

# 访问上传

要通过 Cloudinary CDN 访问上传的文件，
首先创建一个名为`show-file.php`的文件，并在其中添加以下代码:

```
<?php
$host = "localhost";
$user = "YOUR-DATABASE-USERNAME"; 
$password = "YOUR-DATABASE-PASSWORD";

   $con = mysqli_connect($host,$user,$password);
   mysqli_select_db($con,"cloudy_upload") OR DIE('could not connect to database');

$select = mysqli_query($con,"SELECT * FROM resources");
$res = mysqli_fetch_array($select);
$public_id = $res['PublicID']
$filetype = $res['FileType']
list($type,$ext)= split('[/]',$filetype);
if($type = 'video'){
?>
<div class="text-center">
               <video width="80%" class="center-block" controls>
                   <source src="https://res.cloudinary.com/YOUR-CLOUD-NAME/video/upload/FOLDER-NAME/<?php echo $public_id ?>" type="<?php $filetype ?>">
                   Your browser does not support the video tag.
               </video>
       </div>
<?php
}else if($type = 'image'){
?>
<div class="text-center">
<img src="https://res.cloudinary.com/YOUR-CLOUD-NAME/image/upload/FOLDER-NAME/<?php echo $public_id ?>"/>
</div>
<?php
} 
```

接下来，从数据库中选择视频的公共 ID 和文件类型，并在 Cloudinary 控制台上单击媒体库中的链接图标:

[![cloudinary video](img/909438fbe5c164e931ebf2b49712d891.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IWClop1U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pesewawebsoft.cimg/tempsnip.png)

然后，视频会加载到您的浏览器中，如下例所示:

[![uploaded video](img/bfab5cd005e6491a26e4aff6b69075eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ecAtwo0X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pesewawebsoft.cimg/video.JPG)

# 包装完毕

现在，您已经了解了如何将视频和图像文件上传到 Cloudinary 平台，以及如何为您的应用程序检索这些文件。现在是时候激发你的创造力，做一些令人惊奇的事情了。玩得开心！