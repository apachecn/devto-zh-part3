# 使用 html-react-table NPM 包的简单 html 表格

> 原文：<https://dev.to/vikas336/simple-html-table-using-html-react-table-npm-package-2m8l>

使用 NPM install-save html-react-table 安装这个包

从“html-react-table”导入 ReactHtmlTable

const columns = [{ key: 'id '，name: 'ID' }，{ key: 'title '，name: 'Title' }，
{ key: 'company '，name:' Company ' }]；

//在列中传递表的 header 属性

const rows = [{ id: x，title: 'Title x '，company: 'Company x' }，...];

//根据表头属性传递表体 json

const reactable =()= > {
return columns = { columns }
rows = { rows }
/>)；
}

更多信息:[https://www.npmjs.com/package/html-react-table](https://www.npmjs.com/package/html-react-table)