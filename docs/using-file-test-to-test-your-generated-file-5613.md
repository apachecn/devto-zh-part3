# 使用 file-test 测试您生成的文件

> 原文：<https://dev.to/djyde/using-file-test-to-test-your-generated-file-5613>

当我们编写一个会生成一些文件的程序时，我们如何测试这类程序呢？我总是使用`fs`模块，测试目录或文件是否存在。但是我需要为此写很多无聊的代码。

所以我编写 [file-test](https://github.com/djyde/file-test) ，用于关心生成的测试用例。

假设我写了一个应该生成这个目录结构的程序:

```
- root
  - readme.md
  - A
    - a.js
    - b.js
  - B
    - a.ts 
    - b.ts 
```

有了`file-test`，我可以简单的测试一下:

```
const FileTest = require('file-test')

const ft = new FileTest(path.resolve(__dirname, './root'))

ft.includeFile('readme.md') // => true
ft.includeFile('blabla.md') // => false
ft.includeFile('A/a.js') // => true
ft.includeFile('A/b.js') // => true

ft.readFile('A/a.js') // => console.log('hello js')

ft.includeDirectory('A') // => true
ft.includeDirectory('B') // => true
ft.includeDirectory('A/a.js') // => false

ft.include([
  'readme.md',
  'A/a.js',
  'B/a.ts',
]) // => true

ft.include([
  'readme.md',
  'A/a.ts',
  'B/a.ts',
]) // => false 
```

和笑话一起使用也很容易:

```
test('directory structure', () => {
  expect(ft.includeDirectory('B')).toBe(true)
  expect(ft.includeDirectory('A/a.js')).toBe(false)
  expect(ft.readFile('A/a.js')).toEqual(`console.log('hello js')`)
  expect(ft.include([
    'readme.md',
    'A/a.js',
    'B/a.ts',
  ])).toBe(true)
}) 
```

*   Github 回购:[https://github.com/djyde/file-test](https://github.com/djyde/file-test)