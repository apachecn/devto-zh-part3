# gnu time(usr/bin/time)可以检查进程内存使用情况，不需要 vmstat 或 sar

> 原文：<https://dev.to/yuyabu/time-2-can-check-process-memory-usage-without-vmstat-or-sar-1no0>

时间命令有两种类型。

*   bash 内置时间命令
*   gnu 时间

gnu time 命令可以测量 cpu 使用率、平均内存使用率、最大内存使用率等。虽然默认的输出格式很难看到，但用户可以定义自己的格式，只选择必要的信息。

环境信息

```
$ cat /etc/lsb-release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=18.04
DISTRIB_CODENAME=bionic
DISTRIB_DESCRIPTION="Ubuntu 18.04.2 LTS" 
```

```
FORMATTING THE OUTPUT  

| %  | A literal `%'.                                                                                                                                                                                                                                 | 
|----|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------| 
| C  | Name and command line arguments of the command being timed.                                                                                                                                                                                    | 
| D  | Average size of the process's unshared data area, in Kilobytes.                                                                                                                                                                                | 
| E  | Elapsed real (wall clock) time used by the process, in [hours:]minutes:seconds.                                                                                                                                                                | 
| F  | Number of major, or I/O-requiring, page faults that occurred while the process was running.  These are faults where the page has actually migrated out of primary memory.                                                                      | 
| I  | Number of file system inputs by the process.                                                                                                                                                                                                   | 
| K  | Average total (data+stack+text) memory use of the process, in Kilobytes.                                                                                                                                                                       | 
| M  | Maximum resident set size of the process during its lifetime, in Kilobytes.                                                                                                                                                                    | 
| O  | Number of file system outputs by the process.                                                                                                                                                                                                  | 
| P  | Percentage of the CPU that this job got.  This is just user + system times divided by the total running time.  It also prints a percentage sign.                                                                                               | 
| R  | Number of minor, or recoverable, page faults.  These are pages that are not valid (so they fault) but which have not yet been claimed by other virtual pages.  Thus the data in the page is still valid but the system tables must be updated. | 
| S  | Total number of CPU-seconds used by the system on behalf of the process (in kernel mode), in seconds.                                                                                                                                          | 
| U  | Total number of CPU-seconds that the process used directly (in user mode), in seconds.                                                                                                                                                         | 
| W  | Number of times the process was swapped out of main memory.                                                                                                                                                                                    | 
| X  | Average amount of shared text in the process, in Kilobytes.                                                                                                                                                                                    | 
| Z  | System's page size, in bytes.  This is a per-system constant, but varies between systems.                                                                                                                                                      | 
| c  | Number of times the process was context-switched involuntarily (because the time slice expired).                                                                                                                                               | 
| e  | Elapsed real (wall clock) time used by the process, in seconds.                                                                                                                                                                                | 
| k  | Number of signals delivered to the process.                                                                                                                                                                                                    | 
| p  | Average unshared stack size of the process, in Kilobytes.                                                                                                                                                                                      | 
| r  | Number of socket messages received by the process.                                                                                                                                                                                             | 
| s  | Number of socket messages sent by the process.                                                                                                                                                                                                 | 
| t  | Average resident set size of the process, in Kilobytes.                                                                                                                                                                                        | 
| w  | Number of times that the program was context-switched voluntarily, for instance while waiting for an I/O operation to complete.                                                                                                                | 
| x  | Exit status of the command.                                                                                                                                                                                                                    
(this table from manpage of  time) 
```

您可以在环境变量$ TIME 中注册格式，或者用-f 选项指定格式。

```
export TIME="time result\ncmd:%C\nreal %es\nuser %Us \nsys  %Ss \nmemory:%MKB \ncpu %P"

or

time -f "time result\ncmd:%C\nreal %es\nuser %Us \nsys  %Ss \nmemory:%MKB \ncpu %P" <command> 
```

执行结果(运行和检查`sleep`命令)

```
$ /usr/bin/time sleep 1
time result
cmd:sleep 1
real 1.00s
user 0.00s 
sys  0.00s 
memory:2128KB 
cpu 0% 
```