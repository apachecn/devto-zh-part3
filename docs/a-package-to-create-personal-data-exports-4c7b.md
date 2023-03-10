# ★创建个人数据导出的包

> 原文：<https://dev.to/freekmurze/a-package-to-create-personal-data-exports-4c7b>

GDPR 带给我们的好东西之一是数据可移植性的权利。简而言之，这意味着应用程序应该能够为用户导出所有数据。

因为我们在 [Spatie](https://spatie.be) 有多个应用程序需要创建这样的导出，我们决定将我们的解决方案提取到[一个名为 laravel-personal-data-export](https://github.com/spatie/laravel-personal-data-export)的包中。在这篇博文中，我将向您介绍这个包。

## 创建导出

通过分派`CreatePersonalDataExportJob`任务，可以在应用程序的任何地方创建数据导出。

```
// somewhere in your app

use Spatie\PersonalDataExport\Jobs\CreatePersonalDataExportJob;

// ...

dispatch(new CreatePersonalDataExportJob(auth()->user()); 
```

该软件包将创建一个包含所有个人数据的 zip 文件。当 zip 被创建后，它的链接将被发送给用户。默认情况下，zip 保存在非公共位置，用户应该登录才能下载 zip。

您可以在用户模型的`selectPersonalData`方法中配置要导出的数据。

```
// in your User model

public function selectPersonalData(PersonalDataSelection $personalDataSelection) {
    $personalDataSelection
        ->add('user.json', ['name' => $this->name, 'email' => $this->email])
        ->addFile(storage_path("avatars/{$this->id}.jpg");
        ->addFile('other-user-data.xml', 's3');
} 
```

您可以向其中添加新文件或复制现有文件(甚至从远程文件系统)。所有这些都将通过流来完成，所以大文件根本不会造成问题。

这个包还提供了一个名为`personal-data-export:clean`的命令来清理旧的数据导出。

## 一些很酷的代码花絮

这个包的构建相对简单，但是它包含了一些很酷的代码。

在压缩之前，我们将所有选择的数据复制到一个临时目录中。如果我们从远程文件系统复制文件，我们将使用流。下面是代码[(摘自`PersonalDataSelection` )](https://github.com/spatie/laravel-personal-data-export/blob/c6dcab34245fc0c2644d0a3b03ed3c9bb6409a0a/src/PersonalDataSelection.php#L79-L90) :
中的样子

```
$stream = Storage::disk($diskName)->readStream($pathOnDisk);

$pathInTemporaryDirectory = $this->temporaryDirectory->path($pathOnDisk);

file_put_contents($pathInTemporaryDirectory, stream_get_contents($stream), FILE_APPEND); 
```

下面是我们如何使用 [Laravels storage fakes](https://laravel.com/docs/master/mocking#storage-fake) 来测试代码[(取自`PersonalDataSelectionTest.php` )](https://github.com/spatie/laravel-personal-data-export/blob/c6dcab34245fc0c2644d0a3b03ed3c9bb6409a0a/tests/Tests/PersonalDataSelectionTest.php#L55-L65)

```
/** @test */
public function it_can_copy_a_file_from_a_disk_to_the_personal_data_temporary_directory()
{
    $disk = Storage::fake('test-disk');
    $disk->put('my-file.txt', 'my content');

    $this->personalDataSelection->addFile('my-file.txt', 'test-disk');

    $this->assertFileContents($this->temporaryDirectory->path('my-file.txt'), 'my content');
} 
```

下面是我们如何将压缩文件传输到浏览器[(取自`ZipDownloadResponse` )](https://github.com/spatie/laravel-personal-data-export/blob/793622aad2474765b45b462329d3f93d84911387/src/Http/Responses/ZipDownloadResponse.php) 。这段代码适用于本地和远程文件系统。注意，我们可以使用`Content-Disposition`头来确定下载 zip 文件的用户将在浏览器中获得哪个文件名。

```
class ZipDownloadResponse extends StreamedResponse
{
    public function __construct(string $filename)
    {
        $disk = Storage::disk(config('personal-data-export.disk'));

        if (! $disk->exists($filename)) {
            abort(404);
        }

        $downloadFilename = auth()->user()
            ? auth()->user()->personalDataExportName()
            : $filename;

        $downloadHeaders = [
            'Cache-Control' => 'must-revalidate, post-check=0, pre-check=0',
            'Content-Type' => 'application/zip',
            'Content-Length' => $disk->size($filename),
            'Content-Disposition' => 'attachment; filename="'.$downloadFilename.'"',
            'Pragma' => 'public',
        ];

        parent::__construct(function () use ($filename, $disk) {
            $stream = $disk->readStream($filename);

            fpassthru($stream);

            if (is_resource($stream)) {
                fclose($stream);
            }
        }, Response::HTTP_OK, $downloadHeaders);
    }
} 
```

在这个包中，我还发现了一个很好的`void` typehint 用例。让我们来看看[的`ExportsPersonalData`界面](https://github.com/spatie/laravel-personal-data-export/blob/793622aad2474765b45b462329d3f93d84911387/src/ExportsPersonalData.php) :

```
namespace Spatie\PersonalDataExport;

interface ExportsPersonalData
{
    public function selectPersonalData(PersonalDataSelection $personalDataSelection): void;

    public function personalDataExportName(): string;

    public function getKey();
} 
```

那个`void` typehint 告诉用户不应该从`selectPersonalData`返回任何东西。这将更清楚地表明，给定的`$personalDataSelection`是可变的，我们不希望在该实例上调用选择方法。

## 在关闭

我们的软件包还有一些在这篇博文中没有提到的选项。要了解它们，请阅读 GitHub 上的软件包[的自述文件。](https://github.com/spatie/laravel-personal-data-export)

我希望在你的下一个项目中这个包会来自我们。一定要看一下我们团队之前创建的包列表。