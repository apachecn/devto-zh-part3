# 通过命令处理文件上传

> 原文：<https://dev.to/goceb/handing-file-uploads-via-commands-5g7p>

为了给手头的问题一点背景，我们将想象我们正在构建一个简单的应用程序，用户可以上传他们的个人资料的头像。显示的代码不会被绑定到任何框架。

为了避免任何错误方向的思考，我们将立即从讨论中放弃“上传”部分。应用程序真的应该关心文件来自哪里吗？在这种特殊情况下，我不这么认为。

## 命令

我们将这个命令命名为`SetAvatar`。只是一个简单的不可变类，有一些 getters，没有行为/没有业务逻辑，一个 DTO。它需要用户 Id 和上传的文件来设置用户的头像。

说到文件部分，我们有几个选项。我们可以将文件的内容作为字符串传递给命令，这完全没问题。字符串是一种基本类型，命令是不可变的和可序列化的，但是因为它可能会被持久化，所以我不喜欢将整个文件转储到数据库中的想法。

我们可以获取资源，但是资源不能序列化。对于`UploadedFileInterface`实例也是如此(它包含一个资源+可以通过一个流读取文件内容),但是我们将搞乱不变性，并且将总是被约束到这个接口的对象。一点也不明智。

因此，我们只剩下文件路径作为最佳选择。每个上传的文件都有一个 tmp 名称，这实际上是上传图像临时存储的文件路径，应用程序可以毫无问题地读取它。PSR-7 `UploadedFileInterface`公开了获取流和元数据的方法，包括文件的路径。因为这个命令接受一个文件路径，我们可以使用它，不仅仅是上传的图像，还可以使用系统上的任何图像来设置头像。

```
final class SetAvatar {
    private $userId;
    private $filePath;

    public function __construct(UserId $userId, string $filePath){
        $this->userId = $userId;
        $this->filePath = $filePath;
    }

    public function getUserId(): UserId{
        return $this->userId;
    }

    public function getFilePath(): string{
        return $this->filePath;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

不会显示命令处理程序的实现，但是您有文件路径，因此可以从那里开始。

```
final class SetAvatarHandler {
    public function handle(SetAvatar $command): void {
        /**
         * TODO Process the file here, make thumbnails, save to storage, etc...
         */
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

处理程序验证文件很重要，我们不能依赖于在表示层执行的验证，并且有可能通过指定任意文件从应用程序的其他地方触发命令。

像 [mime_content_type](http://php.net/manual/en/function.mime-content-type.php) 和 [finfo_open](http://php.net/manual/en/ref.fileinfo.php) 这样的函数可以用来获取 mime 类型并确定文件的扩展名。

## 表示层

控制器的实现非常简单，但是它完成了工作。表单工厂根据请求创建表单，验证表单，如果成功，则通过命令总线处理命令。

```
final class AvatarController {
    public function upload(ServerRequestInterface $request): ResponseInterface {
        $response = new RedirectResponse('/');

        $form = $this->avatarFormFactory->createFromRequest($request);
        if(!$form->isValid()){
            $this->session->getFlashBag()->add('errors', implode(' ', $form->getValidationErrors()));
            return $response;
        }

        $this->commandBus->handle($form->toCommand());
        $this->session->getFlashBag()->add('success', 'Avatar uploaded');

        return $response;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`UploadAvatarFormFactory`用上传的文件(`UploadedFileInterface`)和登录用户的 Id 创建表单。

```
final class UploadAvatarFormFactory {
        private $session;

    public function __construct(Session $session){
        $this->session = $session;
    }

    public function createFromRequest(ServerRequestInterface $request){
        /**
         * @var UploadedFileInterface[] $uploadedFiles
         */
        $uploadedFiles = $request->getUploadedFiles();
        $uploadedFile = null;

        if(array_key_exists('avatar', $uploadedFiles) && $uploadedFiles['avatar']->getError() != UPLOAD_ERR_NO_FILE){
            $uploadedFile = $uploadedFiles['avatar'];
        }

        return new UploadAvatarForm(
            $this->session->get('userId', null),
            $uploadedFile
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`UploadAvatarForm`是最适合验证输入和可用参数的地方，如文件大小、媒体类型、扩展名等。并向用户返回消息。在这里进行验证，可以确保我们发送一个最有可能有效的命令，但是我们不应该仅仅依赖于这些(因为显而易见的原因),而应该在命令处理程序中实现真正的深度验证。

在这个例子中，客户机文件名是不相关的，但是如果您需要，它可以作为可选参数传递给命令构造函数。

```
final class UploadAvatarForm {
    use UploadedFileValidatorTrait;
    private $userId;
    private $avatar;

    public function __construct(?int $userId, ?UploadedFileInterface $avatar){
        $this->userId = $userId;
        $this->avatar = $avatar;
    }

    public function toCommand(){
        return new SetAvatar(
            new UserId($this->userId),
            $this->avatar->getStream()->getMetadata('uri')
        );
    }

    public function isValid(): bool {
        return empty($this->getValidationErrors());
    }

    public function getValidationErrors(): array{
        $errors = [];
        if($this->userId == null){
            $errors[] = 'Invalid user id!';
        }
        if(!$this->isFileUploaded($this->avatar)){
            $errors[] = $this->getFileUploadError();
        }
        return $errors;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 重放命令

上面的代码假设一旦命令被处理，无论成功与否，它都不能被重放，因为在请求被处理后 tmp 文件不可用。

如果您想要回复命令，您可以采取的一种方法是，首先将文件保存到某个中间存储，并将该路径传递给命令。这样，只要您需要，该文件就可以使用。