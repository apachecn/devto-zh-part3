# 文件和装饰者:学习 Laravel 中的存储

> 原文：<https://dev.to/mariowhowrites/files-and-decorators-studying-storage-in-laravel-2j6h>

我一直在 Laravel 做一个项目，这个项目需要大量的文件工作，特别是图像。上传、下载、复制、移动和删除图像是这个应用程序运行的关键部分。

在某一点上，我意识到我对我的应用程序如何处理图像的了解并没有我想要的那么多。我当然知道文档中概述的内容，但除此之外就没什么了。我在与 Laravel 和 [Statamic](https://statamic.com/) 的合作中听说过 PHP 的 Flysystem 包，但我不知道为什么这个包如此受欢迎，也不知道它在核心层是如何工作的。当我需要编写的业务逻辑超出了 Laravel 的存储 API 所涵盖的功能时，这种知识的缺乏使我陷入了困境。

为此，我决定做一些调查。我还没有完成关于身份验证和顺风 CSS 的系列文章，所以我将把关于文件系统的扩展系列留到下次。这篇文章的重点主要是分享我对 Laravel 的存储 API 如何工作的笔记。在此过程中，我希望将有时抽象的数据结构世界与实际实现联系起来，以展示如何在自己的应用程序中使用这些模式。

## 自上而下储存

一般来说，我研究存储 API 的目标是将我编写的框架级业务逻辑和处理图像的实际业务联系起来。更具体地说，我想知道 Laravel 是如何将目录、权限、URL 等抽象概念包装到一个跨具体实现工作的 API 中的。我怎么能写同样的代码来处理存储在我电脑上的图像和存储在地球另一端的亚马逊数据中心的图像呢？

为了解决这个问题，我有两个选择——自下而上或者自上而下。虽然这两种方法都很适合我的目的，但我最终决定自上而下地工作。这使我能够跟踪我在自己的项目中使用的方法和类，并沿着它们向下探索我感觉舒适的最低级别:原生 PHP 函数。(总有一天我会鼓起勇气去研究 PHP 解释器背后的 C 代码，但今天不是那一天。)

[https://www.youtube.com/embed/8MdaeCpIqmA](https://www.youtube.com/embed/8MdaeCpIqmA)

所以，我们来看一些代码！您可能熟悉的第一种方法是`Storage::disk`方法。存储 facade 遵从 FilesystemManager 类，我们可以在 facade 的 docblock 中看到:

```
/**
    * @method static \Illuminate\Contracts\Filesystem\Filesystem disk(string $name = null)
    *
    * @see \Illuminate\Filesystem\FilesystemManager
    */
class Storage extends Facade
{
    // facade here 
```

下面是 FilesystemManager 类的`disk`方法:

```
/**
    * Get a filesystem instance.
    *
    * @param  string  $name
    * @return \Illuminate\Contracts\Filesystem\Filesystem
    */
public function disk($name = null)
{
    $name = $name ?: $this->getDefaultDriver();

    return $this->disks[$name] = $this->get($name);
}

/**
    * Attempt to get the disk from the local cache.
    *
    * @param  string  $name
    * @return \Illuminate\Contracts\Filesystem\Filesystem
    */
protected function get($name)
{
    return $this->disks[$name] ?? $this->resolve($name);
} 
```

如果之前已经加载了一个磁盘，那么可以在`disks`属性中找到它，这样就不需要加载一个磁盘两次。如果没有使用`disks`属性将磁盘存储在内存中，那么每次调用`Storage` facade 时，都会再次加载同一个磁盘，从性能的角度来看，这并不好。

我们来看一下`resolve`方法:

```
/**
    * Resolve the given disk.
    *
    * @param  string  $name
    * @return \Illuminate\Contracts\Filesystem\Filesystem
    *
    * @throws \InvalidArgumentException
    */
protected function resolve($name)
{
    $config = $this->getConfig($name);

    if (isset($this->customCreators[$config['driver']])) {
        return $this->callCustomCreator($config);
    }

    $driverMethod = 'create'.ucfirst($config['driver']).'Driver';

    if (method_exists($this, $driverMethod)) {
        return $this->{$driverMethod}($config);
    } else {
        throw new InvalidArgumentException("Driver [{$config['driver']}] is not supported.");
    }
}

/**
    * Get the filesystem connection configuration.
    *
    * @param  string  $name
    * @return array
    */
protected function getConfig($name)
{
    return $this->app['config']["filesystems.disks.{$name}"];
} 
```

这里发生的第一件事是，我们从 Laravel 应用程序设置中加载必要的配置。这存储在一个名为`filesystems.php`的文件中，其中一个典型的磁盘如下所示:

```
'disks' => [

    'public' => [
        'driver' => 'local',
    'root' => storage_path('app/public'),
    'url' => env('APP_URL') . '/storage',
    'visibility' => 'public',
    ],

    's3' => [
    'driver' => 's3',
    'key' => env('AWS_ACCESS_KEY_ID'),
    'secret' => env('AWS_SECRET_ACCESS_KEY'),
    'region' => env('AWS_DEFAULT_REGION'),
    'bucket' => env('AWS_BUCKET'),
    'url' => env('AWS_URL'),
    ],

], 
```

根据磁盘类型，此配置包含诸如`visibility`、`url`、`region`等键。

首先，Laravel 检查自定义文件系统是否注册在传递给`Storage::disk`的驱动程序名称下。如果是这样，就使用定制的文件系统，跳过 Laravel 的大部分默认功能。

如果没有自定义驱动器，Laravel 接下来会检查是否有适用于指定磁盘的默认驱动程序。例如，如果我们使用本地驱动程序，这个类将寻找一个`createLocalDriver`函数，这个函数已经存在，可以在下面找到:

```
/**
    * Create an instance of the local driver.
    *
    * @param  array  $config
    * @return \Illuminate\Contracts\Filesystem\Filesystem
    */
public function createLocalDriver(array $config)
{
    $permissions = $config['permissions'] ?? [];

    $links = ($config['links'] ?? null) === 'skip'
        ? LocalAdapter::SKIP_LINKS
        : LocalAdapter::DISALLOW_LINKS;

    return $this->adapt($this->createFlysystem(new LocalAdapter(
        $config['root'], $config['lock'] ?? LOCK_EX, $links, $permissions
    ), $config));
} 
```

对我们来说，重要的逻辑是带有返回值的最后一条语句。

我将再次复制它，但使用更多缩进以使层次结构更加清晰:

```
$this->adapt(
    $this->createFlysystem(
        new LocalAdapter(
            $config['root'], 
            $config['lock'] ?? LOCK_EX, 
            $links, 
            $permissions
        ), 
        $config
    )
); 
```

我们可以看到该功能有三个主要级别。自下而上工作:

1.  一个`LocalAdapter`被创建。这是 Flysystem 类，它处理实际处理本地文件的细节。
2.  然后将创建的适配器传递给`createFlysystem`，它将适配器包装在 Flysystem 的文件系统类中。这个文件系统类处理一些更一般的文件系统工作——规范化路径字符串、读取和操作配置选项，等等。
3.  然后，产生的文件系统被交给`adapt`方法。这创建了一个特定于 Laravel 的包装器，允许 Flysystem 与 Laravel 框架完全集成。

本质上，这个函数包含了组成存储 API 的主要层次结构。这里值得注意的是，这个设置是**装饰器**模式的一个很好的例子。

## 装修工和俄罗斯套料娃娃

装饰图案有点类似于俄罗斯套娃。一个较小的基本对象封装在一个稍大的对象中，后者保留了较小对象的所有功能，并添加了一些自己的方法。

[![Russian nesting dolls.](img/97d83b6479b1613b53ed79bafc34446c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6MM1DZcz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images-na.ssl-images-amazon.cimg/I/71FPVhRNgHL._SX425_.jpg)

图为:俄罗斯嵌套娃娃。像软件开发，色彩多，边缘案例少。

注意，只有最底层 Flysystem 适配器——实际上能够操作文件。然而，就其本身而言，适配器无法以一种直观的方式与 Laravel 进行交互，而开发人员无需自己编写大量样板文件就可以学会这种方式。一个潜在的解决方案是派生出 Flysystem 包，并将必要的功能直接添加到 Flysystem 中，但是这种方法有很多缺点。除了冻结 Laravel 的 Flysystem 派生出任何未来可能添加到 Flysystem 的更新之外，这种方法还会在 Flysystem 包中添加大量额外的代码，这将使未来的更改非常麻烦。

这是部署装饰器模式的最佳位置。通过用提供额外方法和属性的类包装 Flysystem 包，底层包可以按原样使用，同时仍然为 Laravel 开发人员提供流畅和直观的 API。

为了说明这一点，让我们看看您可以对图像执行的最基本的任务之一:存储它。记住，自顶向下的层次结构类似于`Laravel Filesystem -> Flysystem -> Flysystem Adapter`，我们将首先查看由上面的`adapt`方法返回的 Laravel 类中的`put`方法，`FilesystemAdapter`:

```
// Illuminate\Filesystem\FilesystemAdapter

/**
    * Write the contents of a file.
    *
    * @param  string  $path
    * @param  string|resource  $contents
    * @param  mixed  $options
    * @return bool
    */
public function put($path, $contents, $options = [])
{
    $options = is_string($options)
                    ? ['visibility' => $options]
                    : (array) $options;

    // If the given contents is actually a file or uploaded file instance than we will
    // automatically store the file using a stream. This provides a convenient path
    // for the developer to store streams without managing them manually in code.
    if ($contents instanceof File ||
        $contents instanceof UploadedFile) {
        return $this->putFile($path, $contents, $options);
    }

    return is_resource($contents)
            ? $this->driver->putStream($path, $contents, $options)
            : $this->driver->put($path, $contents, $options);
} 
```

这个例子中存储的业务逻辑符合我们对 Laravel 类的潜在期望:帮助者确保开发人员沿着通往生产力的“快乐之路”前进。在方法遇到文件或流的情况下，提供不同的分支，确保`put`可以用于任何类型的资源。

但是，请注意，存储图像的工作最终被传递给了`driver->put`方法。本例中的`driver`表示 Flysystem 文件系统类，所以让我们看看这里的`put`方法:

```
// League\Flysystem\Filesystem

/**
    * Create a file or update if exists.
    *
    * @param string $path     The path to the file.
    * @param string $contents The file contents.
    * @param array  $config   An optional configuration array.
    *
    * @return bool True on success, false on failure.
    */
public function put($path, $contents, array $config = [])
{
    $path = Util::normalizePath($path);
    $config = $this->prepareConfig($config);

    if ( ! $this->getAdapter() instanceof CanOverwriteFiles && $this->has($path)) {
        return (bool) $this->getAdapter()->update($path, $contents, $config);
    }

    return (bool) $this->getAdapter()->write($path, $contents, $config);
} 
```

在不深入源代码的情况下，我们可以看到这个函数中的一些逻辑。首先，没有提到任何我们可能与 Laravel 相关联的东西——没有分支路径，没有照亮类，等等。请记住，Filesystem 类跨所有不同类型的文件系统使用，从本地到 S3 甚至更远，我们看到这里的逻辑是任何基于路径的存储系统都需要做的准备工作。使用`Util::normalizePath`规范化路径，用`prepareConfig`格式化适配器的任何必要配置，然后调用底层适配器上的方法，这取决于该方法是否应该覆盖现有内容。

值得记住的是，这里的任何方法都可以通过在存储 API 上使用`getDriver()`方法在 Laravel 中调用。因此，如果出于某种原因你想直接调用 Flysystem 的`put`而不是使用 Laravel 的`put`方法，你可以通过调用`Storage::disk('your-disk')->getDriver()->put()`来实现。

最后，我们看到这个`put`方法通过使用`getAdapter`调用底层适配器实例上的方法。由于您使用的适配器将取决于您使用的文件系统，如果您将文件存储在本地与存储在 AWS S3 上，这种方法看起来会有所不同。我们现在来看看 LocalAdapter 的实现:

```
// 

/**
* Write a new file.
*
* @param string $path
* @param string $contents
* @param Config $config   Config object
*
* @return array|false false on failure file meta data on success
*/
public function write($path, $contents, Config $config)
{
    $location = $this->applyPathPrefix($path);
    $this->ensureDirectory(dirname($location));

    if (($size = file_put_contents($location, $contents, $this->writeFlags)) === false) {
        return false;
    }

    $type = 'file';
    $result = compact('contents', 'type', 'size', 'path');

    if ($visibility = $config->get('visibility')) {
        $result['visibility'] = $visibility;
        $this->setVisibility($path, $visibility);
    }

    return $result;
}

/**
* Set the visibility for a file.
*
* @param string $path
* @param string $visibility
*
* @return array|false file meta data
*/
public function setVisibility($path, $visibility)
{
    $location = $this->applyPathPrefix($path);
    $type = is_dir($location) ? 'dir' : 'file';
    $success = chmod($location, $this->permissionMap[$type][$visibility]);

    if ($success === false) {
        return false;
    }

    return compact('path', 'visibility');
} 
```

这是代码中使用 PHP 的本机函数`file_put_contents`将文件实际存储在文件系统中的地方。这里，开发人员提供的路径和内容与 Laravel 在 FilesystemManager 中创建 LocalAdapter 时定义的 writeFlags 相结合。

类似地，使用 PHP 的`chmod`函数设置文件的可见性，它的工作方式与 Linux 的“chmod”命令几乎相同。

## 包装完毕

通过跟踪 Laravel 存储图像所涉及的堆栈，我们有望了解到两件事:存储 API 如何工作，以及存储 API 的架构如何遵循装饰范例。在 API 的每一层，这个类在把责任交给它下面的 API 之前都会做一些准备工作。最终结果是，您的文件存储在应用程序中的正确位置，并且返回必要的信息供开发人员采取行动——所有这一切都只需一行代码。