# Python Linux 命令执行器

> 原文：<https://dev.to/mu/python-linux-command-executor-4ib1>

```
# IMPORTING LIBRARIES import subprocess
import shlex

def cmdExecutor(cliCmd):
    # COLLECT THE COMMAND AND SPLIT USING SHLEX
    cmd = shlex.split(cliCmd)
    # EXECUTE THE SUBPROCESS WITH COMMUNICATE()
    process,error = subprocess.Popen(cmd, stdout = subprocess.PIPE, stderr= subprocess.PIPE).communicate()
    print("Command executed successfully")
    # DECODE THE RESPONSE FROM THE SUBPROCESS TO UTF-8
    error = error.decode("utf-8")
    if(str(error)==None or str(error)==""):
        output="SUCCESS"
    else:
        output = "ERROR => "+str(error)
    return output 
```