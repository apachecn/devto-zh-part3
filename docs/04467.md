# Node.js 文件系统 API -初学者友好指南

> 原文：<https://dev.to/areknawo/node-js-file-system-api-beginner-friendly-guide-b6l>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

延续“ [**Node.js 由内而外！**](https://areknawo.com/tag/nodejs/) "系列，今天我们将仔细研究一下 [**文件系统 API**](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html)——最大的 Node.js APIs 之一！只是注意一下——我们使用的是 Node.js **LTS v10.15.3** 。现在，事不宜迟，让我们开始吧！

# 文件系统 API

访问文件系统、管理和编辑文件可能是在服务器端完成的最重要的任务之一。因此，Node.js 被认为提供了这样的功能。它以文件系统(FS) API 的形式实现，包含大量的方法和属性。虽然对这个 API 的随意使用通常仅限于读写文件，但是仍然有很多很多东西有待发现...

# 异步性质

对文件执行的操作可能需要一段时间。由于 JS 的单线程特性，FS API 以**异步**的形式为这个问题提供解决方案是显而易见的。因此，所有与读写相关的方法都有异步和同步版本。对于这种情况，异步绝对是一个更好的解决方案，方法的同步版本有适当的 **-Sync** 后缀。你应该记住同步方法的使用只是**极不推荐的**，当使用时，会阻止主线程执行任何其他操作。小心使用它们(并且只有在你真的需要的时候)！

出于上述原因，我们将只关注 API 的异步方法。它们的同步副本看起来非常相似(除了回调和返回值)，如果你想知道更多，你可以随时查看官方文档。

# 阅读

## 访问检查

读取文件的过程，或者有些人可能会说访问文件的过程，相对来说是所有与 FS 相关的过程中最简单的。但是，在阅读文件本身之前，最好检查一下是否有可用的**和 [`fs.access()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_access_path_mode_callback) 方法。** 

```
const fs = require("fs");

fs.access("file.js", fs.constants.F_OK, err => {
    console.log(err ? "Doesn't exist" : "Exists");
}); 
```

Enter fullscreen mode Exit fullscreen mode

像许多其他 FS 方法一样，`fs.access()`将一个**文件路径**作为第一个参数。在传递这个参数时，你要记住它几乎总是有 [**3 种可接受的形式**](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_file_paths) - **字符串**、**缓冲区**和 **URL 对象**。

```
const pathStr = "file.js";
const pathBuff = Buffer.from("file.js");
const pathURL = new URL("file:///current/dir/file.js"); 
```

Enter fullscreen mode Exit fullscreen mode

字符串和缓冲区形式可以表示相对和绝对路径，而 **URL** 仅限于使用**文件、协议**和**绝对路径**。表示路径的数据类型将取决于您的用例，默认情况下很可能是字符串形式。此外，Windows 和它处理绝对路径和驱动器字母的方式也有一些奇怪之处——在[文档](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_url_object_support)中会有更多介绍。

第二个可选参数通常称为 **mode** 可以用来定义对给定文件需要什么类型的访问权限。例如，它可以是读、写访问，或者只是简单地检查文件是否存在。它用一个整数表示，因此，考虑到`fs.access()`方法，您通常会使用 [**文件访问常数**](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_file_access_constants) ，通过 [`fs.constants`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_constants_1) 来设置它。这些是`F_OK`、`R_OK`、`W_OK`和`X_OK`，分别表示文件可见性(如果它甚至存在的话)、读权限、写权限和执行权限。

最后，你需要给**传递一个回调**,如果出了问题，它总是向你提供一个错误，否则不提供任何东西。因此，如果没有抛出错误，您可以确定您已经获得了对文件的正确访问。

## 读取文件

有了正确的访问检查结果，你可以继续**读取文件**。在 Node.js 中并不比调用 [`fs.readFile()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_readfile_path_options_callback) 难。作为参数，您必须传递文件和回调的路径，在这里您可以访问缓冲区格式的文件数据。

```
// ...
fs.readFile("file.js", (err, data) => {
    if (!err) {
        console.log(data); // <Buffer ...>
    }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

但是，缓冲区格式的数据并不真正有用。当然，你仍然可以按照你想要的方式使用它，比如以后解析它，但是假设我们想要访问“文本”格式的文件内容，这意味着使用标准的 **utf8 编码**。为此，我们可以利用可选的**选项参数**并传递一个字符串，指示带有`encoding`和`flag`属性的对象的编码。第二个应该是由可用的 [**文件系统标志**](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_file_system_flags) **，意为例如`"a"`(追加)、`"r"`(读取，默认)或`"w"`(写入)组成的字符串。

```
// ...
fs.readFile("file.js", "utf8", (err, data) => {
    if (!err) {
        console.log(data); // file content
    }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

## 阅读目录

不推荐将`fs.readFile()`与**目录路径**一起使用，因为它具有特定于平台的行为(主要是抛出错误)。相反，你应该使用各自的 [`fs.readdir()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_readdir_path_options_callback) 方法。在形式上，这个方法与它的文件读取对应方法非常相似——相同的参数，只是不同的可选参数和方法名中的小写字母。

```
// ...
fs.readdir("some/dir", (err, entries) => {
    if (!err) {
        console.log(entries); // ["file1.js", "file2.js"]
    }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

在 options 对象中，编码默认为现在的`"utf8"`(如果需要缓冲区，则设置为`"buffer"`)，其次，`withFileTypes`布尔被设置为`true`，产生一组 [`fs.Dirent`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_class_fs_dirent) 实例，通过`.isDirectory()`、`.isFile()`或`.isSymbolicLink()`等方法提供有用的信息。

```
// ...
fs.readdir("some/dir", {withFileTypes: true }, (err, entries) => {
    if (!err) {
        entries.forEach(entry => {
            console.log(entry.isFile()); // true
        });
    }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

## 链接

说到链接，你很容易用 [`fs.readlink()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_readlink_path_options_callback) 得到它的**源路径**。它的形式类似于前面的方法，可选的对象属性只包括编码，默认为`"utf8"`。

```
// ...
fs.readlink("symlinkedFile.js", (err, link) => {
    if (!err) {
        console.log(link); // symlink source path 
    }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

## 文件描述符

最后，我们还有 [`fs.open()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_open_path_flags_mode_callback) 。这是一个较低级的方法，几乎所有与 FS 读写相关的 Node.js 方法都在内部使用。给定路径、系统标志的字符串、可选的模式号和回调函数，它可以创建、读取或重新创建文件，该文件稍后将可用于读取、写入或任何操作，这取决于传递的参数。重要的是要知道，与`fs.access()`相比，这个方法的模式参数也可以用`fs.constants` ( [**【文件打开常数】**](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_file_open_constants) )来设置，但是这次它们是从`O_`开始的——例如`O_RDONLY`、`O_WRONLY`。

```
// ...
fs.open("file.js", "r+", fs.constants.O_RDWR, (err, fd) => {
    if (!err) {
        console.log(fd);
    }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

同样，这是一个更低级的方法，你很可能不会在日常任务中使用它。这里，最重要的事情之一是它返回的值——通常命名为 **fd** 。就是所谓的 [**文件描述符**](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_file_descriptors) 。它基本上是给定文件的一个整数 ID。它经常在整个 FS 模块文档中被引用，并被其他底层方法使用，如 [`fs.read()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_read_fd_buffer_offset_length_position_callback) 或 [`fs.write()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_write_fd_buffer_offset_length_position_callback) 与缓冲区进行更深入的交互。同样，我们不会涉及它们，因为大多数 FS 任务并不真正需要它们。但是，更有趣的是，文件描述符也可以用来代替某些 FS 方法的路径，比如`fs.readFile()`。

```
// ...
fs.open("file.js", "r+", fs.constants.O_RDWR, (err, fd) => {
    if (!err) {
        fs.readFile(fd, "utf8", (err, data) => {
            if (!err) {
                console.log(data);
            }
        });
    }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

用`fs.open()`打开的文件稍后可以用极其简单的 [`fs.close()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_close_fd_callback) 关闭，只需要文件描述符和一个回调。

```
// ...
fs.close(fd, () => {
    if (!err) {
        // ...
    }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，读取文件相当容易。您只需要知道一些关于这些`fs.constants`和文件系统标志的知识。但即使是这样，也只是为了更高级的使用，现代的支持 TS 的 IDE 或带有自动完成功能的代码编辑器很可能会帮你记住它们。另外，不要让上面的例子蒙蔽了你的双眼- **处理 FS 错误**需要比简单的 if-checks 多一点小心。最后，如果你想知道为什么在**的茶包**中只写了`fs.readFile()`(除了 **-Sync** 的对应物)，那是因为**代表了**。用 camelCase 写的方法都是 Node.js 自己实现的，其他的都是仿照 [**POSIX**](https://en.wikipedia.org/wiki/POSIX) C 函数。所有新兴的方法都是如此。当心那些单词单一的！

# 写作

在 FS 相关的东西中，写可以有两种理解——要么你把一些内容写到一个文件，要么你把任何种类的**改变写到文件系统**(包括改变一个文件)。这里，我们采用第二种更广泛的方法。

## 写文件

从一个随意的文件的内容写作开始，我们有了 [`fs.writeFile()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_writefile_file_data_options_callback) 的方法。这里，我们提供了我们的可写文件(或文件描述符)的路径，以字符串或缓冲区的形式将**数据写入**以及回调函数。附加选项对象可以包括标志(默认为文件系统标志字符串- `"w"`)、您提供的数据的编码(默认为`"utf8"`)和模式属性，或者是一个仅指定编码的简单字符串。但是，大多数时候，没有他们你也会过得很好。

```
// ...
fs.writeFile("file.js", "Data to be written", err => {
    if (!err) {
        console.log("Written");
    }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

当`fs.writeFile()`**完全清除**文件以前的内容时，当你想**向文件追加一些内容**时， [`fs.appendFile()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_appendfile_path_data_options_callback) 会做得很好。它的调用结构与带有一个的`fs.writeFile()`几乎相同，唯一的不同是可选的文件系统标志字符串(文件属性)默认为`"a"`以允许追加。

```
// ...
fs.appendFile("file.js", "Data to be appended", err => {
    if (!err) {
        console.log("Appended");
    }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

最后，你还可以用 [`fs.truncate()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_truncate_path_len_callback) 来**截断**给定文件的当前内容。它只接受一个文件的路径和一个数字，该数字表示您想要将其截断到什么长度。哦，当然还有回电。

```
// ...
fs.truncate("file.js", 10, err => {
    if (!err) {
        console.log("Truncated");
    }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

如果提供的长度超过了给定文件的长度，它将被填充以**空字符**，在你的文件中留下奇怪的，很可能是不想要的字符。您不能对文件描述符使用此方法。对于这一点，你可以用它的直接对应物 [`fs.ftruncate()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_ftruncate_fd_len_callback) 。

## 目录

Node.js 还提供了一组与更改/写入目录相关的方法。其中一个为很多终端用户所熟知的就是 [`fs.mkdir()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_ftruncate_fd_len_callback) 。它为您的**新目录**、选项对象(可选)和一个回调函数获取路径。在 options object 中，您可以传递 mode 属性(同样是为了权限之类的东西)和 **recursive** boolean，它们指示是否应该创建指向路径中提供的目录的父目录(如果它们还不存在的话)。

```
// ...
fs.mkdir("my/new/dir", {recursive: true}, err => {
    if(!err) {
        console.log("Created");
    }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

如果你的新目录只打算存储一些**临时数据**，你应该使用 [`fs.mkdtemp()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_mkdtemp_prefix_options_callback) 方法。和`fs.mkdir()`有点不一样。它没有路径，而是有一个**前缀参数**，有点像路径，但是**后面会添加六个随机字符**。Options 对象采用 encoding 属性(默认为`"utf8"`)来表示处理路径的编码，但是您也可以使用字符串格式。最后，提供了一个回调函数(除了标准的 err 参数),其中包含新的临时目录的名称。

```
const fs = require("fs");
const os = require("os");
const path = require("path");

fs.mkdtemp(path.join(os.tmpdir(), "mydir"), (err, tempDir) => {
  if (!err) {
    console.log(tempDir); // e.g. /tmp/mydirY4ciGj on Linux
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

请记住在你的操作系统的临时文件夹中创建一个临时目录——这不会自动完成！

最后，你可以用简单的 [`fs.rmdir()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_mkdtemp_prefix_options_callback) 调用标准的东西**删除给定的目录**。要知道删除之前目录应该是空的！

```
// ...
fs.rmdir("dir/to/remove", err => {
    if (!err) {
        console.log("Removed");
    }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

## 链接

创建硬链接和符号链接也可以被认为是将更改写入 FS 的活动。在 Node.js 中，你可以用一个很好的 [`fs.symlink()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_symlink_target_path_type_callback) 方法**创建符号链接**。它接受链接的目标和路径。

```
// ...
fs.symlink("target/to/symlink", "symlink/dir", err => {
    if (!err) {
        console.log("Symlinked");
    }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

只有 **Windows** 需要一个其他平台都不支持的**类型参数**，这让生活变得更加艰难。它是一个字符串，可以有值`"dir"`、`"file"`或`"junction"`，取决于你的符号链接目标的类型。

**硬链接**可以用 [`fs.link()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_link_existingpath_newpath_callback) 方法创建，就像这次用`fs.symlink()`一样，只是没有 Windows 类型参数。

```
// ...
fs.link("target/to/link", "link/dir", err => {
    if (!err) {
        console.log("Linked");
    }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

一个链接可以用提供其路径的 [`fs.unlink()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_unlink_path_callback) 删除**。只有文件链接可以使用这种方法。对于目录使用前面提到的`fs.rmdir()`。** 

```
// ...
fs.unlink("link/dir", err => {
    if (!err) {
        console.log("Unlinked");
    }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

## 其他变化

除了以上所有的好处，在 Node.js 中你还可以重命名或复制你的文件。第一个操作可以用 [`fs.rename()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_rename_oldpath_newpath_callback) 的方法进行，只需要当前和新的路径...自然还有回调。

```
// ...
fs.rename("file.js", "renamedFile.js", err => {
  if (!err) {
    console.log("Renamed");
  }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

复制文件要求更高一些，需要使用 [`fs.copyFile()`](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_rename_oldpath_newpath_callback) 方法，不仅要传递源路径和目标路径，还要传递一些标志(可选)。这些可以用`fs.constants` ( [**文件复制常量**](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_file_copy_constants) ) - `COPYFILE_EXCL`、`COPYFILE_FICLONE`、`COPYFILE_FICLONE_FORCE`中的常量来设置，都是指源和目的地之间的关系。

```
// ...
fs.copyFile("file.js", "dest.js", err => {
  if (!err) {
      console.log("Copied");
  }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

# 暂停

就这些吗？良好的...当然不是！仍然有很多内容需要介绍，包括**所有其他方法**、**流**以及更多 node . js good！只是，写作，以及阅读(我觉得)这么大的文章不会是一个愉快的经历。因此，请记住**我们还没有完成**，我们将在本系列的后续文章中继续探索。

那么，**你喜欢文章**吗？这对你今天学到新东西有帮助吗？如果有，请**与他人分享**！此外，让我知道你对评论中的**有什么看法，以及下面**的一个反应**。如果你想了解最新内容(包括本系列和文章的续篇)，请务必查看我的个人博客**和**在 Twitter 上关注我的****或在我的脸书页面** 上关注我的 [**。一如既往，感谢你阅读这篇文章，祝你有美好的一天！**](http://facebook.com/areknawoblog)********