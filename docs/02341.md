# 如何将 latex 矩阵显示为代码单元的输出

> 原文：<https://dev.to/callas1900/how-to-display-latex-matrix-as-output-of-a-code-cell-40ck>

```
from IPython.display import display, Math

def print_matrix(array):
    data = ''
    for line in array:        
        if len(line) == 1:
            data += ' %.3f &'%line + r' \\\n'
            continue
        for element in line:
            data += ' %.3f &'%element
        data += r' \\' + '\n'
    display(Math('\\begin{bmatrix} \n%s\end{bmatrix}'%data)) 
```