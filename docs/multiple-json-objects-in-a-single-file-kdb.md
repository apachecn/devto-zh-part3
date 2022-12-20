# 单个文件中的多个 Json 对象

> 原文：<https://dev.to/vinodgarg/multiple-json-objects-in-a-single-file-kdb>

我们将多个 json 对象以数组格式存储在一个文件中(每个文件 30K 条记录)。我在寻找 java 代码，可以用来分裂成多个对象的 json 对象，并提供变量中的 json 对象。此外，有什么方法可以用来读取数组元素一个接一个，而不是加载整个数据在内存中的文件大小和记录计数是巨大的，这可能会导致内存问题。