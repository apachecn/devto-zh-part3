# 使用 Snippy 上传图片到 WordPress 媒体库

> 原文：<https://dev.to/pqina/using-snippy-to-upload-images-to-the-wordpress-media-library-57le>

这个快速指南向你展示了如何使用免费的 WordPress 插件 Snippy 建立一个上传图片到你的媒体库的表单。

## 创建文件上传处理程序

第一步，让我们打开你的主题文件`functions.php`，并添加下面的代码片段。这将为我们创建一个发送文件的位置。

```
function handle_my_file_upload() {

    // will return the attachment id of the image in the media library
    $attachment_id = media_handle_upload('my_file_field', 0);

    // test if upload succeeded
    if (is_wp_error($attachment_id)) {
        http_response_code(400);
        echo 'Failed to upload file.';
    }
    else {
        http_response_code(200);
        echo $attachment_id;
    }

    // done!
    die();
}

// allow uploads from users that are logged in
add_action('wp_ajax_my_file_upload', 'handle_my_file_upload');

// allow uploads from guests
add_action('wp_ajax_nopriv_my_file_upload', 'handle_my_file_upload'); 
```

Enter fullscreen mode Exit fullscreen mode

有几件事需要注意:

*   `handle_my_file_upload`是处理上传的函数名，两个`add_action`调用都引用它。
*   `wp_ajax_`和`wp_ajax_noprive_`是默认的钩子，我们给它们添加了`my_file_upload`,以允许与我们接下来要设置的表单进行通信。
*   注释掉`nopriv`调用以防止文件从来宾上传。

## 创建表单

好了，现在我们需要添加一个`<form>`来给用户一个位置来选择他们想要上传的图片。

我们将使用免费的 WordPress 插件 Snippy 来创建一个短代码，我们可以在 WordPress 网站的任何地方使用它。

安装 Snippy 后，我们将开始创建一个 HTML 位。点击“Snippy/Add Bit”菜单项。将你的 HTML 比特命名为“文件格式”。将类型设置为“HTML”。并将下面的代码粘贴到文本字段中。

```
<form action="{{admin_url}}" method="POST" enctype="multipart/form-data">
    <input type="hidden" name="action" value="my_file_upload"/>
    <label for="image">Select file:</label>
    <input type="file" id="image" name="my_file_field" accept="image/*"/>
    <button type="submit">Upload</button>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

*   `my_file_upload`对应于`add_action`调用中使用的值。
*   `my_file_field`对应于`media_handle_upload`调用中使用的值。

现在我们将创建我们的自定义短代码。

点击“Snippy/Add Shortcode”并使用“file-upload”作为名称。在“可用位”列表中，我们选择“文件格式”。单击保存。

就是这样！我们现在有了一个表格简称。

```
[file-upload/] 
```

Enter fullscreen mode Exit fullscreen mode

将它添加到一个页面(使用页面编辑器)，刷新页面，您的文件上传表单现在应该会出现。

## 奖励:编辑图像

让我们升级表单，允许用户使用[多卡](https://pqina.nl/doka/)编辑拖放的图像

我们可以使用 Snippy 将多卡文件添加到页面中。

1.  创建一个新的本地资源位，并选择多卡 CSS 文件(doka.min.css)。
2.  创建另一个本地资源位，并选择多卡 JavaScript 文件(doka.min.js)。
3.  创建一个 JavaScript 位并添加以下代码。

```
document.addEventListener('change', function(e) {

    // Store reference to the field to make later code easier to read
    var field = e.target;

    // Only intercept fields of type file that accept images
    if ((field.type !== 'file') || (!/image\/*/.test(field.accept))) return;

    // Get reference to parent form
    var form = field.form;

    // Edit dropped image with Doka
    Doka.create({

        // The file the user selected
        src: field.files[0],

        // We allow the user to crop and filter the image
        utils: ['crop', 'filter'],

        // Called when the user is done editing
        onconfirm: function(output) {

            // Create a new FormData object based on the information in the form
            var formData = new FormData(form);

            // Overwrite the file data with the modified file
            formData.set(field.name, output.file, output.file.name);

            // Post async to the server
            fetch(form.getAttribute('action'), {
                    method: form.getAttribute('method'),
                    body: formData
                })
                .then(function(id) {
                    alert('done!') // Or redirect to another page
                });
        }
    });

}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以通过点击“Snippy/Shortcodes”菜单项并编辑我们的“文件上传”短代码，将这些新创建的位链接到我们的“文件上传”短代码。选择我们刚刚创建的位，然后单击“保存”。

刷新页面并选择图像，多卡应该打开图像，当点击完成时，编辑过的图像应该出现在 WordPress 媒体库中。

[![Animation of Doka and WordPress](img/ef2292a95c8d4cfcb3767222852df9d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uW1A6rV9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sp2rle56hmkgh2rbq98e.gif)

## 安全

为了让这个解决方案更安全一点，我们可以添加一个 [WordPress nonce 字段](https://codex.wordpress.org/Function_Reference/wp_nonce_field)。

像这样编辑 HTML 表单:

```
<form action="{{admin_url}}" method="POST" enctype="multipart/form-data">

    {{nonce_field:my_file_upload, my_nonce_field}}

    <!-- ...This part of the form remains untouched -->

</form> 
```

Enter fullscreen mode Exit fullscreen mode

并将下面的 if 语句添加到`functions.php`文件中，以便它检查表单 post 是否有效，是否来自您自己的站点。

```
<?php
function handle_my_file_upload() {

    if (empty($_POST)) {
        http_response_code(400);
        echo 'Nothing data received.';
        die();
    }

    if (!wp_verify_nonce($_POST['my_nonce_field'], 'my_file_upload')) {
        http_response_code(400);
        echo 'Unknown error.';
        die();
    }

    /* ...The rest of the function stays the same */

} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

就是这样！我们完了。这些代码片段应该能让你很好地理解用 WordPress 上传文件需要什么，以及如何在其上构建。如果你不熟悉 Snippy，这应该是一个很好的介绍，关于它如何帮助你管理你的 WordPress 站点的小部分。

也就是说。这仍然是一个基本的解决方案，可以通过重定向、上传进度指示器、发送额外的表单数据等等进一步构建。我将把它留给以后的文章。