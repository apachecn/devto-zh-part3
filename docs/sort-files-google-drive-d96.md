# 分类文件 Google Drive

> 原文：<https://dev.to/davidlacarta/sort-files-google-drive-d96>

简单的脚本按照大小对 google drive 文件进行排序

```
[...document.getElementsByClassName("a-t-J a-Wa-ka l-oi-cc l-t-Q a-t-J-yl")]
  .map(element => {
    const [
      size,
      unit
    ] = element.childNodes[3].childNodes[0].childNodes[1].childNodes[0].innerText.split(
      "  "
    );
    const SIZES_KB = { MB: 1024, GB: 1048576, KB: 1 };
    return {
      size: Number(size) * SIZES_KB[unit],
      node: element
    };
  })
  .sort((elementA, elementB) => elementB.size - elementA.size)
  .forEach(element => {
    const files = document.getElementsByClassName("a-t-J-Rf")[0];
    files.appendChild(element.node);
  }); 
```

Enter fullscreen mode Exit fullscreen mode