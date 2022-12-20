# Tin Ear:使用 python 减少警报电子邮件噪音

> 原文：<https://dev.to/lbonanomi/tin-ear-reducing-alert-email-noise-with-python-nbh>

> 或者也许你不应该把你碰巧捡到的每一个小垃圾都带给我。
> <small>-搏击俱乐部</small>

成熟的 IT 企业似乎总是有一堆杂乱无章的外壳脚本来监控至少部分基础设施并向管理员发送电子邮件。电子邮件脚本可以很好，因为它们可以快速设置和调整，但通常没有限制警报或指示问题已经解决的指示器，过于频繁的警报只会成为 ops 的噪音。

*让我们使用[耳语](https://github.com/graphite-project/whisper)时序数据库来包装`/bin/mail`并削减我们的警报噪音。*

### 已确认问题的预警

让我们就邮件包装器的一些规则达成一致:

*   一个错误条件必须出现 3 次，我们才会发出警报，所以我们不会一直喊狼来了。
*   在最初的警报之后，我们应该减少发送后续警报的频率，以减少警报疲劳。
*   如果错误条件停止，我们得到三个好的结果，我们将发送一个退出消息。

我们将通过实例化一个基于邮件主题的 whisper 数据库来开始我们的邮件包装器项目，该邮件主题是*将被*发送到:

```
import base64
import os
import whisper

RETAINER = [(300, 3)]                       # [(seconds_in_period, slots_in_period)] 
whisper_db_dir = '/var/tmp/whisperDB/'
whisper_db_name = str(whisper_db_dir + base64.b64encode(sys.argv[1]) + '.wsp')

if not os.path.exists(whisper_db_dir):
    os.path.mkdir(whisper_db_dir)

if not os.path.exists(whisper_db_name):
    whisper.create(whisper_db_name, RETAINER, aggregationMethod='last') 
```

这个块将为每个提醒邮件主题创建目录`/var/tmp/whisperDB`和一个空的密语数据库。每个数据库将包含 3 个 5 分钟长的时间段“窗口”来存储检查状态，我们可以通过简单的函数调用来更新这些状态:`whisper.update(whisper_db_name, 0)`表示正常状态，`whisper.update(whisper_db_name, 1)`表示失败。

接下来，让我们确定检查脚本的状态是否频繁到足以发出警报。如果相同的检查失败了 3 次，我们将发送一封初始电子邮件，然后将我们的 5 分钟状态窗口更改为 15 分钟状态窗口，这样管理员就不会开始忽略重复的邮件:

```
(times, fail_buffer) = whisper.fetch(db_name, 315550800)

if fail_buffer.count(1) > 2:
    new_whisper_db_name = db_name + '.wsp2'
    whisper.create(new_whisper_db_name, FOLLOWUP, aggregationMethod='last')
    whisper.update(new_whisper_db_name, 1)
    os.rename(new_whisper_db_name, db_name)

    for admin in sys.argv[2:]:
        os.system('mail -s "' + sys.argv[1] + '" ' + admin + 'M/dev/null') 
```

当我们在这里的时候，让我们也准备好发送一个解除警报的消息，如果当前的警报窗口是三倍的，并且最后 3 次检查已经返回一个正常状态:

```
(times, fail_buffer) = whisper.fetch(db_name, 315550800)

if fail_buffer.count(1) == 0:
    if whisper.info(db_name)['archives'][0]['secondsPerPoint'] == FOLLOWUP[0][0]:
        new_whisper_db_name = db_name + '.wsp2'
        whisper.create(new_whisper_db_name, RETAINER, aggregationMethod='last')
        whisper.update(new_whisper_db_name, 0)
        os.rename(new_whisper_db_name, db_name)

        for admin in sys.argv[2:]:
            os.system('mail -s "' + sys.argv[1] + '" ' + admin + 'M/dev/null') 
```

我找不到在 shell 之外捕获`$?`的方法，所以我们将使用一些符号链接来解决这个问题。让我们将基础脚本`tinear`链接到一个表示检查失败的名称(`tinear.nok`)和一个表示检查成功的名称(`tinear.ok`)，这样我们就可以根据被调用的符号链接的名称来更新我们的 whisper 数据库:

```
if os.path.basename(sys.argv[0]) == "tinear.ok":
    whisper.update(whisper_db_name, 0)

if os.path.basename(sys.argv[0]) == "tinear.nok":
    whisper.update(whisper_db_name, 1) 
```

现在我们可以修改我们的脚本，在现有的 shell 脚本中使用 bash 三元运算符，就像我们对`mail -s` :
的普通调用一样

```
some_check && tinear.ok "some_check is recovered" admin@example.com || tinear.nok "some_check has failed" admin@example.com 
```

### 让我们把这一切联系起来:

```
#!/bin/python """alert concentrating mail handler"""

import base64
import os
import whisper
import sys

RETAINER = [(300, 3)]                       # [(seconds_in_period, slots_in_period)] FOLLOWUP = [(900, 3)]

def waterlevel(db_name):
    """Reduce alert frequency after initial alert, reset on all-clear"""

    (times, fail_buffer) = whisper.fetch(db_name, 315550800)

    if fail_buffer.count(1) > 2:
        new_whisper_db_name = db_name + '.wsp2'
        whisper.create(new_whisper_db_name, FOLLOWUP, aggregationMethod='last')
        whisper.update(new_whisper_db_name, 1)
        os.rename(new_whisper_db_name, db_name)

        for admin in sys.argv[2:]:
            os.system('mail -s "' + sys.argv[1] + '" ' + admin + '</dev/null')

    if fail_buffer.count(1) == 0:
        if whisper.info(db_name)['archives'][0]['secondsPerPoint'] == FOLLOWUP[0][0]:
            new_whisper_db_name = db_name + '.wsp2'
            whisper.create(new_whisper_db_name, RETAINER, aggregationMethod='last')
            whisper.update(new_whisper_db_name, 0)
            os.rename(new_whisper_db_name, db_name)

            for admin in sys.argv[2:]:
                os.system('mail -s "' + sys.argv[1] + '" ' + admin + '</dev/null')

    return(0)

whisper_db_dir = '/var/tmp/whisperDB/'
whisper_db_name = str(whisper_db_dir + base64.b64encode(sys.argv[1]) + '.wsp')

if not os.path.exists(whisper_db_dir):
    os.path.mkdir(whisper_db_dir)

if not os.path.exists(whisper_db_name):
    whisper.create(whisper_db_name, RETAINER, aggregationMethod='last')

if os.path.basename(sys.argv[0]) == "tinear.ok":
    whisper.update(whisper_db_name, 0)

if os.path.basename(sys.argv[0]) == "tinear.nok":
    whisper.update(whisper_db_name, 1)

waterlevel(whisper_db_name) 
```