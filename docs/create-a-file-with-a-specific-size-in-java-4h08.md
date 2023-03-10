# 用 Java 创建一个特定大小的文件

> 原文：<https://dev.to/codebyamir/create-a-file-with-a-specific-size-in-java-4h08>

# 概述

java.io 包提供了帮助我们创建和管理文件的类。在本文中，我们将演示如何基于文件名创建一个空文件，以及如何创建一个具有特定大小的文件。

# 代码示例

## 创建一个空文件

```
private File createFile(final String filename) throws IOException {
  File file = new File(filename);
  file.createNewFile();
  return file;
} 
```

请注意，这不会创建丢失的父文件夹。如果您需要这样做，请在第 2 行调用`file.getParentFile().mkdirs()`。

## 创建一个特定大小的文件

我们可以使用 RandomAccessFile 类创建一个特定大小的文件。

```
private File createFile(final String filename, final long sizeInBytes) throws IOException {
  File file = new File(filename);
  file.createNewFile();

  RandomAccessFile raf = new RandomAccessFile(file, "rw");
  raf.setLength(sizeInBytes);
  raf.close();

  return file;
} 
```

注意，以这种方式创建的文件由随机数据填充，可能被 JVM 实现和底层操作系统视为“稀疏文件”。