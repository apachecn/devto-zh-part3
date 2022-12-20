# 用 JS、Python 和 Java 学习算法#10:金字塔

> 原文：<https://dev.to/tommy3/learning-algorithms-with-js-python-and-java-10-pyramid-4mga>

这一系列文章以三种不同的语言讲述了斯蒂芬·格里德的 Udemy 课程。

今天的问题和上一个问题略有不同。

> -说明
> 写一个接受正数 N 的函数。
> 这个函数应该使用#字符控制台记录一个有 N 层的金字塔形状
> 。确保
> 金字塔的左侧*和右侧*都有空间
> -示例
> 金字塔(1)
> '#'
> 金字塔(2)
> ' # '
> '###'
> 金字塔(3)
> ' # '
> ' # # '
> ' # # # # # # '

# 1:迭代求解

JavaScript:

```
function pyramid(n) {
    const columnCount = 2 * n - 1;
    const midColumn = Math.floor(columnCount / 2);

    for (let row = 0; row < n; row++) {
        let level = '';

        for (let column = 0; column < columnCount; column++) {
            if (Math.abs(column - midColumn) <= row) {
                level += '#';
            } else {
                level += ' ';
            }
        }

        console.log(level);
    }
} 
```

Python:

```
def pyramid(n):
    column_count = 2 * n - 1
    mid_column = column_count // 2

    for row in range(n):
        level = ''

        for column in range(column_count):
            if abs(column - mid_column) <= row:
                level += '#'
            else:
                level += ' '

        print(level) 
```

Java:

```
static void pyramid1(int n) {
    int columnCount = 2 * n - 1;
    int midColumn = columnCount / 2;

    for (int row = 0; row < n; row++) {
        StringBuilder level = new StringBuilder();

        for (int column = 0; column < columnCount; column++) {
            if (Math.abs(column - midColumn) <= row) {
                level.append("#");
            } else {
                level.append(" ");
            }
        }

        System.out.println(level);
    }
} 
```

# 2:递归求解

在这里，我采用了一种与课程中介绍的方法略有不同的方法。

JavaScript:

```
function pyramid(n, row = 0, level = '#') {
    if (n === row) {
        return;
    }

    if (level.length === 2 * n - 1) {
        console.log(level);
        return pyramid(n, row + 1);
    }

    level = level.length < 2 * row ? `#${level}#` : ` ${level} `;
    pyramid(n, row, level);
} 
```

Python:

```
def pyramid(n, row=0, level='#'):
    if n == row:
        return

    if len(level) == 2 * n - 1:
        print(level)
        return pyramid(n, row+1)

    level = f'#{level}#' if len(level) < 2 * row else f' {level} '
    pyramid(n, row, level) 
```

Java:

```
static void pyramid(int n) {
    pyramid(n, 0, new StringBuilder("#"));
}

static void pyramid(int n, int row, StringBuilder level) {
    if (n == row) {
        return;
    }

    if (level.length() == 2 * n - 1) {
        System.out.println(level);
        pyramid(n, row + 1, new StringBuilder("#"));
        return;
    }

    if (level.length() < 2 * row) {
        level.insert(0, "#").append("#");
    } else {
        level.insert(0, " ").append(" ");
    }

    pyramid(n, row, level);
} 
```

感谢您的阅读。我确实计划更经常地写这个系列，所以我希望很快见到你！