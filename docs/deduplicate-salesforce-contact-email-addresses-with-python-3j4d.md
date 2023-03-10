# 使用 Python 删除重复的 Salesforce 联系人电子邮件地址

> 原文：<https://dev.to/katiekodes/deduplicate-salesforce-contact-email-addresses-with-python-3j4d>

sales force[# awesome admin](https://twitter.com/hashtag/awesomeadmin)Matthew([@ matthewsalibi](https://twitter.com/matthewsalibi))[问他如何使用 Python 对 Salesforce NPSP 的电子邮件地址](https://katiekodes.com/salesforce-python-qa-001/)进行跨多个字段的重复数据删除。今天，我将为您提供一个脚本，您可以复制、粘贴并尝试自己的数据。对于极客，我将分解它并解释每一行是如何工作的。

## 数据&预期

### 输入数据

概括一下，Matthew 建议我们从 CSV 格式导出他的“Contacts”表开始，“ **contacts.csv** ”，看起来像这样*(我合并了他的例子，增加了一点复杂性)*:

| salesforceiq | 人类可读的 ID | 西方人名的第一个字 | 姓 | 首选电子邮件 | 个人电子邮件 | 工作电子邮件 | 备用电子邮件 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 003…01 | 双侧束支传导阻滞 | 希迪 | Anagonye | 工作 | [ca@gp.com](mailto:ca@gp.com) | [ca@hotmail.com](mailto:ca@hotmail.com) | [ca@hotmail.com](mailto:ca@hotmail.com) |
| 003…02 | 嗒嗒球拍 | 埃莉诺 | 壳牌石油公司 | 个人的 | [es@gp.com](mailto:es@gp.com) |  |  |
| 003…03 | 咿咿 | 伊阿宋 | 门多萨 | 工作 | [jm@gp.com](mailto:jm@gp.com) | [jm@gp.com](mailto:jm@gp.com) | [jm@gp.com](mailto:jm@gp.com) |
| 003…04 | FFFF | 埃莉诺 | 壳牌石油公司 | 交替的 | [es@hotmail.com](mailto:es@hotmail.com) |  | [es@gp.com](mailto:es@gp.com) |
| 003…05 | GGGG | 珍妮特 | 从-投诉 | 交替的 | [jd@gp.com](mailto:jd@gp.com) |  | [jm@gp.com](mailto:jm@gp.com) |
| 003…06 | 哈哈哈哈 | 塔哈里 | 贾米勒 | 交替的 | [ta@gp.com](mailto:ta@gp.com) | [ta@yahoo.com](mailto:ta@yahoo.com) | [ta@gp.com](mailto:ta@gp.com) |
| 003…07 | IIII | 希迪 | Anagonye | 工作 |  | [ca@gp.com](mailto:ca@gp.com) |  |
| 003…08 | 斤斤计较（网络用语） | 明迪（Minna 的异体）（f.） | 圣克莱尔 | 个人的 | [ms@mp.com](mailto:ms@mp.com) |  |  |
| 003…09 | 单色模式 | Kamilah | 贾米勒 | 个人的 | [ka@kafoundation.org](mailto:ka@kafoundation.org) |  |  |
| 003…10 | AAAA | 埃莉诺 | 壳牌石油公司 | 交替的 |  |  | [es@gp.com](mailto:es@gp.com) |
| 003…11 | CCCC | 埃莉 | 壳牌石油公司 | 工作 |  | [es@gp.com](mailto:es@gp.com) |  |
| 003…12 | left lower lung lobe 左下方肺叶 | 明迪（Minna 的异体）（f.） | 圣克莱尔 | 个人的 | [ms@yahoo.com](mailto:ms@yahoo.com) | [ms@hotmail.com](mailto:ms@hotmail.com) | [ms@hotmail.com](mailto:ms@hotmail.com) |
| 003…13 | 嗯 | 明迪（Minna 的异体）（f.） | 圣克莱尔 | 工作 | [ms@z.com](mailto:ms@z.com) | [ms@z.com](mailto:ms@z.com) | [ms@z.com](mailto:ms@z.com) |
| 003…14 | NNNN | 塔哈里 | 贾米勒 | 交替的 |  |  | [ta@gp.com](mailto:ta@gp.com) |

### 输出文件#1

Matthew [希望看到](https://katiekodes.com/salesforce-python-qa-001/#de-duping-named-email-fields-within-a-single-record)一个" **output1.csv** "来识别行中的重复*，并提供一个“清理”文件，为数据加载器做好准备，如下所示:*

| salesforceiq | 人类可读的 ID | 西方人名的第一个字 | 姓 | 首选电子邮件 | 个人电子邮件 | 工作电子邮件 | 备用电子邮件 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 003…01 | 双侧束支传导阻滞 | 希迪 | Anagonye | 工作 | [ca@gp.com](mailto:ca@gp.com) | [ca@hotmail.com](mailto:ca@hotmail.com) |  |
| 003…03 | 咿咿 | 伊阿宋 | 门多萨 | 工作 |  | [jm@gp.com](mailto:jm@gp.com) |  |
| 003…06 | 哈哈哈哈 | 塔哈里 | 贾米勒 | 交替的 |  | [ta@yahoo.com](mailto:ta@yahoo.com) | [ta@gp.com](mailto:ta@gp.com) |
| 003…13 | 嗯 | 明迪（Minna 的异体）（f.） | 圣克莱尔 | 工作 |  | [ms@z.com](mailto:ms@z.com) |  |

### 输出文件#2A

为了识别被怀疑是冗余联系人的整个*行*，马修[提出了一个“ready for DemandTools”文件](https://katiekodes.com/salesforce-python-qa-001/#de-duping-named-email-fields-across-records)“**output 2a . CSV**”，看起来像下面的数据。

该数据与 DemandTools 的“单表重复数据删除”模块的“从文件中选择主/非主记录”功能兼容。

每一行表示在多个联系人记录*中发现的“相同的电子邮件地址，相同的名字”配对(其中“电子邮件地址”可以出现在 NPSP 附带的任何“电子邮件”字段中)*。

每列是在其中找到电子邮件地址的联系人的 Salesforce ID。

| 掌握 | 重复 _1 | 重复 _2 |
| --- | --- | --- |
| 003…01 | 003…07 |  |
| 003…10 | 003…02 | 003…04 |
| 003…06 | 003…14 |  |

### 输出文件#2B

对于共享一个电子邮件地址但*没有*名字的联系人组，Matthew 希望在输出文件“ **output2b.csv** ”中看到更多的细节，以便于人工审查。他建议看起来像下面的输出。

| 电子邮件地址 | 掌握 | Master_FN | 主人 _LN | 重复 _1 | 重复 _1_FN | 重复 _1_LN |
| --- | --- | --- | --- | --- | --- | --- |
| [es@gp.com](mailto:es@gp.com) | 003…10 | 埃莉诺 | 壳牌石油公司 | 003…11 | 埃莉 | 壳牌石油公司 |
| [jm@gp.com](mailto:jm@gp.com) | 003…03 | 伊阿宋 | 门多萨 | 003…05 | 珍妮特 | 从-投诉 |

* * *

[![](img/47f7ca605ec5d2d9093f6e6d476e7fe3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5NugSxh8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://3.bp.blogspot.com/-5_6YUIk62jc/T9XkEZOZDlI/AAAAAAAAEcU/vyuTeWex3DA/s1600/Solar-powered-car-fan-large.jpg)

## 自己运行代码

不幸的是，我还没有写一篇关于在你自己的电脑上运行 Python 代码的博文，所以如果你对 Python 完全陌生，你会有点困惑——但是把这段代码展示给一位*知道如何操作的同事吧！*

或者，如果你喜欢冒险，下载一个适用于你电脑操作系统的“蟒蛇套装”安装程序，试试看。

*   当它询问您是否想安装 VSCode 时，不要费心安装。
*   完成后，打开“Anaconda Navigator”并打开“Spyder”
    *   在左侧的代码编辑面板中键入`print('hello')`，点击靠近顶部的“运行”(如果弹出一个框，点击“取消”并再次执行)，然后检查是否在输出控制台*(可能是右下角)*中的屏幕右侧打印了`hello`。
    *   如果是这样，恭喜你——你刚刚写了一个程序！退格键退出`print(hello)`并复制/粘贴我的代码，然后开始调整它。

[请继续关注](//twitter.com/katiekodes)我的“办公时间”和“实践培训”，在那里我教**所有非程序员**运行这样的代码。

* * *

[![](img/feef61b2e508e1ddfc67c1c56bf4d28e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HYKrSGsa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://c.pxhere.com/photos/30/93/coding_code_programming_programming_language_source_code-89334.jpg%21d)

## 代码

简单地复制这段代码，将其粘贴到您的 Python“IDE”中，并做一些调整，这并不可耻！

当然，如果您的列没有精确拼写为“PersonalEmail”、“WorkEmail”、“人类可读 ID”等，您将不得不做一些“查找和替换”来修复列名引用*(顺便说一下，这些引用**区分大小写，以匹配您的输入 CSV 文件** )* 中的大小写)。

### 代码只产生" Output1 "

```
import time
start_time = time.time()
import pandas
pandas.set_option('expand_frame_repr', False)

emFieldNames = ['PersonalEmail','WorkEmail','AlternateEmail'] # Edit this before running
pickToField = {'Personal':'PersonalEmail','Work':'WorkEmail','Alternate':'AlternateEmail'} # Edit this before running
inputFilePath = r'c:\example\contacts.csv' # Edit this before running
output1FilePath = r'c:\example\output1.csv' # Edit this before running
output1NoneFoundFilePath = r'c:\example\nonefound1.txt' # Edit this before running

df = pandas.read_csv(inputFilePath, dtype=object)

df['ChangedAnything'] = False

validPrefEmailIndicatorTFSeries = df['PreferredEmail'].map(pickToField).isin(df.columns)
df.loc[validPrefEmailIndicatorTFSeries,'PrefEmAddr'] = df.lookup(validPrefEmailIndicatorTFSeries.index, df[validPrefEmailIndicatorTFSeries]['PreferredEmail'].map(pickToField))

for fieldName in emFieldNames:
    needsClearingTFSer = (df['PreferredEmail'].map(pickToField) != fieldName) & (df[fieldName] == df['PrefEmAddr'])
    df.loc[needsClearingTFSer,fieldName] = None
    df.loc[needsClearingTFSer,'ChangedAnything'] = True

output1df = df[df['ChangedAnything']]

output1df = output1df.drop(columns=['PrefEmAddr','ChangedAnything'])

if len(output1df) > 0:
    output1df.to_csv(output1FilePath, index=False, quoting=1)
else:
    with open(output1NoneFoundFilePath, 'w') as f:
        f.writelines('No records found')

print('Done running script. Took ' + str(round(time.time()-start_time,2)) + ' seconds.') 
```

### 代码只产生“Output2A”和“Output2B”

```
import time
start_time = time.time()
from collections import OrderedDict
import pandas
pandas.set_option('expand_frame_repr', False)

emFieldNames = ['PersonalEmail','WorkEmail','AlternateEmail'] # Edit this before running
pickToField = {'Personal':'PersonalEmail','Work':'WorkEmail','Alternate':'AlternateEmail'} # Edit this before running
inputFilePath = r'c:\example\contacts.csv' # Edit this before running
output2aFilePath = r'c:\example\output2a.csv' # Edit this before running
output2aNoneFoundFilePath = r'c:\example\nonefound2a.txt' # Edit this before running
output2bFilePath = r'c:\example\output2b.csv' # Edit this before running
output2bNoneFoundFilePath = r'c:\example\nonefound2b.txt' # Edit this before running

df = pandas.read_csv(inputFilePath, dtype=object)

df = df.sort_values('Human-Readable ID')
df = df.reset_index(drop=True)
df = df.set_index(['Human-Readable ID','SalesforceID','FirstName','LastName','PreferredEmail'])

allEmailsSeries = df[emFieldNames].stack()

groupedEmailSeriesGroupItems = allEmailsSeries.groupby(allEmailsSeries).groups.items()

emsAndTheirCellsFound = {emaddr:([x for x in indices]) for emaddr,indices in groupedEmailSeriesGroupItems if len(indices) > 1}

output2aList = []
output2bList = []
for emAddr, foundCells in emsAndTheirCellsFound.items():
    seenFirstNamesAndTheirContacts = {}
    numCellsFound = len(foundCells)
    output2bFact = OrderedDict()
    if numCellsFound > 1:
        for cellLoopCounter, cell in enumerate(foundCells):
            if cell[2] not in seenFirstNamesAndTheirContacts:
                seenFirstNamesAndTheirContacts[cell[2]] = [cell[:-1]]
                if cellLoopCounter == 0:
                    output2bFact['emAddr'] = emAddr
                    output2bFact['Master'] = cell[1]
                    output2bFact['Master_FN'] = cell[2]
                    output2bFact['Master_LN'] = cell[3]
                else:
                    uniqueFirstNamesSeenCountMinus1Str = str(len(seenFirstNamesAndTheirContacts) - 1)
                    output2bFact['Duplicate_'+str(uniqueFirstNamesSeenCountMinus1Str)] = cell[1]
                    output2bFact['Duplicate_'+str(uniqueFirstNamesSeenCountMinus1Str)+'_FN'] = cell[2]
                    output2bFact['Duplicate_'+str(uniqueFirstNamesSeenCountMinus1Str)+'_LN'] = cell[3]
            elif cell[:-1] not in seenFirstNamesAndTheirContacts[cell[2]]:
                seenFirstNamesAndTheirContacts[cell[2]].append(cell[:-1])
            if cellLoopCounter == numCellsFound-1:
                if 'Duplicate_1' in output2bFact:
                    output2bList.append(output2bFact)
                for firstName, contactsForFN in seenFirstNamesAndTheirContacts.items():
                    output2aFact = OrderedDict()
                    if len(contactsForFN) > 1:
                        for contactLoopCounter, contact in enumerate(contactsForFN):
                            if contactLoopCounter == 0:
                                output2aFact['Master'] = contact[1]
                            else:
                                output2aFact['Duplicate_'+str(contactLoopCounter)]=contact[1]
                    if 'Duplicate_1' in output2aFact:
                        output2aList.append(output2aFact)

if len(output2aList) > 0:
    output2adf = pandas.DataFrame(output2aList)
    output2adf.to_csv(output2aFilePath, index=False, quoting=1)
else:
    with open(output2aNoneFoundFilePath, 'w') as f:
        f.writelines('No records found')

if len(output2bList) > 0:
    output2bdf = pandas.DataFrame(output2bList)
    output2bdf.to_csv(output2bFilePath, index=False, quoting=1)
else:
    with open(output2bNoneFoundFilePath, 'w') as f:
        f.writelines('No records found')

print('Done running script. Took ' + str(round(time.time()-start_time,2)) + ' seconds.') 
```

### 代码一次产生所有 3 个输出文件

```
import time
start_time = time.time()
from collections import OrderedDict
import pandas
pandas.set_option('expand_frame_repr', False)

emFieldNames = ['PersonalEmail','WorkEmail','AlternateEmail'] # Edit this before running
pickToField = {'Personal':'PersonalEmail','Work':'WorkEmail','Alternate':'AlternateEmail'} # Edit this before running
inputFilePath = r'c:\example\contacts.csv' # Edit this before running
output1FilePath = r'c:\example\output1.csv' # Edit this before running
output1NoneFoundFilePath = r'c:\example\nonefound1.txt' # Edit this before running
output2aFilePath = r'c:\example\output2a.csv' # Edit this before running
output2aNoneFoundFilePath = r'c:\example\nonefound2a.txt' # Edit this before running
output2bFilePath = r'c:\example\output2b.csv' # Edit this before running
output2bNoneFoundFilePath = r'c:\example\nonefound2b.txt' # Edit this before running

df = pandas.read_csv(inputFilePath, dtype=object)

# ### Output1 portion starts here ###
df['ChangedAnything'] = False

validPrefEmailIndicatorTFSeries = df['PreferredEmail'].map(pickToField).isin(df.columns)
df.loc[validPrefEmailIndicatorTFSeries,'PrefEmAddr'] = df.lookup(validPrefEmailIndicatorTFSeries.index, df[validPrefEmailIndicatorTFSeries]['PreferredEmail'].map(pickToField))

for fieldName in emFieldNames:
    needsClearingTFSer = (df['PreferredEmail'].map(pickToField) != fieldName) & (df[fieldName] == df['PrefEmAddr'])
    df.loc[needsClearingTFSer,fieldName] = None
    df.loc[needsClearingTFSer,'ChangedAnything'] = True

output1df = df[df['ChangedAnything']]

output1df = output1df.drop(columns=['PrefEmAddr','ChangedAnything'])

if len(output1df) > 0:
    output1df.to_csv(output1FilePath, index=False, quoting=1)
else:
    with open(output1NoneFoundFilePath, 'w') as f:
        f.writelines('No records found')
# ### Output1 portion ends here ###

# ### Output2 portion starts here ###
df = df.sort_values('Human-Readable ID')
df = df.reset_index(drop=True)
df = df.set_index(['Human-Readable ID','SalesforceID','FirstName','LastName','PreferredEmail'])

allEmailsSeries = df[emFieldNames].stack()

groupedEmailSeriesGroupItems = allEmailsSeries.groupby(allEmailsSeries).groups.items()

emsAndTheirCellsFound = {emaddr:([x for x in indices]) for emaddr,indices in groupedEmailSeriesGroupItems if len(indices) > 1}

output2aList = []
output2bList = []
for emAddr, foundCells in emsAndTheirCellsFound.items():
    seenFirstNamesAndTheirContacts = {}
    numCellsFound = len(foundCells)
    output2bFact = OrderedDict()
    if numCellsFound > 1:
        for cellLoopCounter, cell in enumerate(foundCells):
            if cell[2] not in seenFirstNamesAndTheirContacts:
                seenFirstNamesAndTheirContacts[cell[2]] = [cell[:-1]]
                if cellLoopCounter == 0:
                    output2bFact['emAddr'] = emAddr
                    output2bFact['Master'] = cell[1]
                    output2bFact['Master_FN'] = cell[2]
                    output2bFact['Master_LN'] = cell[3]
                else:
                    uniqueFirstNamesSeenCountMinus1Str = str(len(seenFirstNamesAndTheirContacts) - 1)
                    output2bFact['Duplicate_'+str(uniqueFirstNamesSeenCountMinus1Str)] = cell[1]
                    output2bFact['Duplicate_'+str(uniqueFirstNamesSeenCountMinus1Str)+'_FN'] = cell[2]
                    output2bFact['Duplicate_'+str(uniqueFirstNamesSeenCountMinus1Str)+'_LN'] = cell[3]
            elif cell[:-1] not in seenFirstNamesAndTheirContacts[cell[2]]:
                seenFirstNamesAndTheirContacts[cell[2]].append(cell[:-1])
            if cellLoopCounter == numCellsFound-1:
                if 'Duplicate_1' in output2bFact:
                    output2bList.append(output2bFact)
                for firstName, contactsForFN in seenFirstNamesAndTheirContacts.items():
                    output2aFact = OrderedDict()
                    if len(contactsForFN) > 1:
                        for contactLoopCounter, contact in enumerate(contactsForFN):
                            if contactLoopCounter == 0:
                                output2aFact['Master'] = contact[1]
                            else:
                                output2aFact['Duplicate_'+str(contactLoopCounter)]=contact[1]
                    if 'Duplicate_1' in output2aFact:
                        output2aList.append(output2aFact)

if len(output2aList) > 0:
    output2adf = pandas.DataFrame(output2aList)
    output2adf.to_csv(output2aFilePath, index=False, quoting=1)
else:
    with open(output2aNoneFoundFilePath, 'w') as f:
        f.writelines('No records found')

if len(output2bList) > 0:
    output2bdf = pandas.DataFrame(output2bList)
    output2bdf.to_csv(output2bFilePath, index=False, quoting=1)
else:
    with open(output2bNoneFoundFilePath, 'w') as f:
        f.writelines('No records found')
# ### Output2 portion ends here ###

print('Done running script. Took ' + str(round(time.time()-start_time,2)) + ' seconds.') 
```

* * *

## 不想学 Python 就此打住

您不必完全理解上面的代码就可以使用它。

只要确保你的“输出”文件和“输入”文件是不同的文件名，并且你不能做太多的破坏。

最糟糕的情况是，你会因为文件太大而让运行程序的电脑死机——但是几百万行根本不算什么***(一分钟或几分钟)*，所以起来伸展一下吧。对你有好处！**

 **对于好奇的人来说，这篇文章的其余部分完全是在引擎盖下窥视的极客。

* * *

[![](img/00496fb276590feba47bb199c9f538cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T3K_JcsV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.publicdomainpictures.net/pictures/60000/velka/woman-teacher-cartoon.jpg)

## 代码解释

我正在努力让所有的格式在 DEV 上正确显示，所以请查看我在[的原始博文](https://katiekodes.com/salesforce-python-qa-001-answer/#code-explanations)中的分解，并带着问题、评论或更正回到这里。我期待收到你的来信！**