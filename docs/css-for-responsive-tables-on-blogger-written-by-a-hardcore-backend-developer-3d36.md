# Blogger |上的响应式表格 CSS，由铁杆后端开发人员编写

> 原文：<https://dev.to/full_stackgeek/css-for-responsive-tables-on-blogger-written-by-a-hardcore-backend-developer-3d36>

作为一个铁杆后端开发人员，我对 CSS 知之甚少，因此，当我遇到一个挑战(是的，对于后端开发人员来说，这些东西是一个挑战 xD)需要在我的上显示一些表格数据时，我实际上在互联网上尝试了这么多东西，最终想出了我自己的(几乎:P) CSS 解决方案。

在这里，我将展示我对响应式表格的解决方案，并敦促所有 CSS 极客提出更好的解决方案。

所以这是我的解决方案:

```
.data_table_custom {
    display: block;
    overflow-x: auto;
    border-collapse: collapse;
    border-spacing: 0;
    width: 100%;
    margin-bottom: 1em;
    border: none;
    line-height: 2;
}

.data_table_custom tbody {
    border: 3px solid yellow;
}

.data_table_custom td {
    padding: 8px;
    border: none;
    text-align: left;
}

.first_block {
    background-color: #00FFFF;
}

.data_table_custom td{
    word-wrap: break-word;
    width: 16%;
}

.data_table_custom .odd{
   background-color: #dfdfdf;
} 

```

注意:
1)数据 _ 表格 _ 自定义- >表格类
2)表头的第一个 _ 块- >

我在一些博客上用过这个:

1) 

2) [InnoDB 与 MyISAM |存储引擎|表格差异](https://fullstackgeek.blogspot.com/2019/05/main-differences-between-innodb-and-myisam.html)