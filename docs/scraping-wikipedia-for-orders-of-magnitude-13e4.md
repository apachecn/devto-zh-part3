# 在维基百科上搜寻数量级

> 原文：<https://dev.to/benjcorc/scraping-wikipedia-for-orders-of-magnitude-13e4>

[![](img/03072d5b0d26461a25fae993e47cde32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SOoZoDWB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AU9kX0C5_QBtSJH2p.jpg)

[*把笔记本叉到这里*](https://github.com/benjamincorcoran/scrapingWikipediaOrdersOfMagnitude)

Pint 是一个很棒的 python 包，允许单位之间的简单转换。事实上，它涵盖了几乎所有的科学 SI 单位和大部分的英制单位。这意味着我可以很容易地在英寻和米之间转换。Pint 也使得扩展一个单元或者包含一个全新的定制单元变得非常容易。

当涉及到你在计算中使用的标准单位时，很少有一个简单的真实世界对这个单位的实际值的理解。数字感对于有效的科学交流非常重要。这可能是一个人投入工作和完全放弃之间的区别。

这个多雨的下午项目抓取[维基百科的数量级部分](https://en.wikipedia.org/wiki/Category:Orders_of_magnitude)以提取潜在有用的真实世界比较，并生成一个品脱配置以使这些值作为单位可访问。

#### 刮维基百科

我们需要做的第一件事是找到一些有用的数据。经过大约两个小时的谷歌搜索，我设法找到了维基百科的数量级类别。这些是各种概念的一系列表格，列出了不同数量级的概念值的示例，例如长度。

查看[数量级部分](https://en.wikipedia.org/wiki/Category:Orders_of_magnitude)，几乎所有页面都有一个带有“值”和“项目”列的表格。“值”列包含各种单位的数值。“项目”列是对“值”列所代表内容的描述。“数据”、“数字”、“辐射”、“温度”和“时间”页面与其他表格的结构不同。不像维基百科那样不一致！我们可能不得不在晚些时候回到这些问题上来。

对于其他人，我们只需要找到一种方法从那些表中提取数据。谢天谢地，比我聪明的人已经解决了这个问题。在他们解决了这个问题之后，他们在溶液中加入了稳定剂。如今，你真的必须尝试在 python 上失败。

Robobrowser 让网页抓取变得异常简单。让我们打开 Robobrowser 的一个实例，并将其指向详细描述各种不同数量级部分的页面。

```
import re
import pandas as pd 
import robobrowser

BASEURL = 'https://en.wikipedia.org/wiki/Category:Orders\_of\_magnitude'

rb = robobrowser.RoboBrowser(history=False)
rb.open(BASEURL) 
```

此时，我们可以使用内置的 select 函数选择页面上的所有链接。select 函数的行为与 jQuery $()函数非常相似。在# MW-pages 部分我们可以找到所有的链接。然后，我们可以用一点点正则表达式将它们过滤到真正的“数量级”页面。让我们把这些都塞进字典里，以备后用。

```
pages = {x.contents[0]:x for x in rb.select('#mw-pages li a') if re.match(r'^Orders',x.contents[0]) is not None} 
```

现在只是迭代字典的过程，告诉 robobrowser 跟随链接并解析我们在该页面上找到的任何表。下面的代码展示了这个过程是如何工作的。所写内容的最大部分是处理每页的古怪之处。希望这些评论能够对每个步骤中发生的事情提供一些清晰的说明。

```
# Initialise lastTableColumns variable. This will be used to capture the structure of 
# the table in the case that the header row is missing. 
lastTableColumns = None

# Create empty dictionary to store processed items
OrdersOfMagnitude = dict()

print("Begining wikipedia scape...\n")

for pageId, link in pages.items():

 # The order of magnitude pages for Pressure and Money have have renamed the 'Value' column. 
 # This dictionary allows for these values to be looked up.

 wikiValueColumns = dict(pressure='Pressure',currency='Money')

 # Get value column header

 if pageId in wikiValueColumns.keys():
 valueColumn = wikiValueColumns[pageId]
 else:
 valueColumn = 'Value'

 # Follow the link to the order of magnitude table 
 rb.follow\_link(link)

 # Select all tables on the page with the .wikitable class
 rawTables = rb.select('.wikitable')

 # Create a list to store parsed page tables
 pageTables = []

 for i, rawTable in enumerate(rawTables):

 # Parse the html table using pandas
 table = pd.read\_html(str(rawTable))[0]

 # Search the parsed columns for names similar to 'Value'
 # and 'Item'. Some of the tables have additional text 
 # in the header. Using the filter/like combo we can avoid
 # manually defining each column name. 

 valColList = table.filter(like=valueColumn).columns
 itmColList = table.filter(like='Item').columns

 # Check that the table has a Value and Item column

 if len(valColList) \> 0 and len(itmColList) \> 0:
 valCol = valColList[0]
 itmCol = itmColList[0]

 # Some pages only show the header on the first table, 
 # in that case use the previously parsed table's header

 elif i\>0 and lastTableColumns is not None and len(lastTableColumns)==len(table.columns):
 table.columns = lastTableColumns
 valCol = table.filter(like=valueColumn).columns[0]
 itmCol = table.filter(like='Item').columns[0]

 # If neither of the above conditions are met then we 
 # cannot parse this table as it doesn't meet our defined
 # structure.

 else:
 continue

 # Set lastTableColumns for part of this table 

 lastTableColumns = table.copy().columns

 # Some tables have the unit in the Value header rather 
 # than within the column. In that instance we want to 
 # pull that information out. Using some RegEx we search
 # for any Value column head with brackets. 

 if len(re.findall(r'\((.\*)\)',valCol)) \>0 :
 tableUnit = re.sub(r'[^A-Za-z0-9\/]','', re.findall(r'\((.\*)\)',valCol)[0])
 else:
 tableUnit = ''

 # Throw away all columns except for the Value and Item
 # and throw away all rows with a null Value.

 table = table[[valCol,itmCol]]
 table = table[table[valCol].notnull()]

 # Split the Value column into a numeric value and a unit
 table[['Value (Numeric)','Unit']] = table[valCol].str.extract(r'([^\s]+)\s\*([^\s]\*)$', expand=True)

 # Standardise the scientific notation, replacing '×10' with 'e'
 table['Value (Numeric)'].replace(regex=True, inplace=True, to\_replace=r'×10', value='e')

 # Filter out any complex values such as '50 to 100' or '20-25'. 
 table=table[table['Value (Numeric)'].map(lambda x: re.match(r'.\*(?:\d\*\.)?\d+[^\de.]+(?:\d\*\.)?\d+',x) is None)]

 # Remove any additional text or symbols that are still present in the string
 table['Value (Numeric)'].replace(regex=True, inplace=True, to\_replace=r'[^.–\-/e−\d]', value='')

 # Convert wikipedia stylistic choice of '−' into the common '-' character.
 table['Value (Numeric)'].replace(regex=True, inplace=True, to\_replace=r'−', value='-')

 # Convert any digits displayed as superscript in the unit to inline powers
 table['Unit'].replace(regex=True, inplace=True, to\_replace=r'((?:-)?\d)', value=r'^\1')

 # Finally pass the string to to\_numeric in order to parse this into a floating 
 # point. Then filter any values that failed to get through the conversion as well 
 # as any values who resolve to 0\. 
 table['Value (Numeric)'] = pd.to\_numeric(table['Value (Numeric)'],errors='coerce')
 table = table[table['Value (Numeric)']!=0]
 table = table[table['Value (Numeric)'].notnull()]

 # Extract any wiki references from the Item column for potential future use 
 table[['Reference']] = table[itmCol].str.extract(r'((?:\[.+\])+)$', expand=True)

 # Create a detail column containing the Item text without any references
 table['Detail'] = table[itmCol].replace(regex=True, to\_replace=r'(?:\[.\*\])+', value='')

 # Remove any null detail columns
 table = table[table['Detail'].notnull()]

 # In the case that the unit is defined in the header, add this unit to the unit 
 # column
 table[['Unit']]=table['Unit'].replace('',tableUnit)

 pageTables.append(table)

 # Concatenate all the tables on the page and add this to our dictionary of Orders of Magnitude
 if len(pageTables) \> 0:

 OrdersOfMagnitude[pageId] = pd.concat(pageTables)

 # Print summary of the results. 
 print('{}: {} approximations found.'.format(pageId,len(OrdersOfMagnitude[pageId]))) 
```

当我们运行以上代码时，我们得到以下输出，表明我们的抓取没有错误。

```
Begining wikipedia scape...

 Orders of magnitude (acceleration): 52 approximations found.
 Orders of magnitude (angular momentum): 8 approximations found.
 Orders of magnitude (area): 64 approximations found.
 Orders of magnitude (bit rate): 45 approximations found.
 Orders of magnitude (charge): 21 approximations found.
 Orders of magnitude (currency): 43 approximations found.
 Orders of magnitude (current): 37 approximations found.
 Orders of magnitude (energy): 170 approximations found.
 Orders of magnitude (entropy): 1 approximations found.
 Orders of magnitude (force): 33 approximations found.
 Orders of magnitude (frequency): 54 approximations found.
 Orders of magnitude (illuminance): 14 approximations found.
 Orders of magnitude (length): 118 approximations found.
 Orders of magnitude (luminance): 26 approximations found.
 Orders of magnitude (magnetic field): 32 approximations found.
 Orders of magnitude (mass): 156 approximations found.
 Orders of magnitude (molar concentration): 25 approximations found.
 Orders of magnitude (power): 80 approximations found.
 Orders of magnitude (probability): 45 approximations found.
 Orders of magnitude (specific heat capacity): 36 approximations found.
 Orders of magnitude (speed): 96 approximations found.
 Orders of magnitude (voltage): 27 approximations found. 
```

#### 使用 TextBlob 生成格式名称

既然我们已经收集了数据，我们需要弄清楚这些值的含义。我们从各种表格中获得的信息栏给了我们一个很好的描述。Info 列不适合作为标识符。其中一些信息串持续两三句话。

我们需要想出一种方法将这些长字符串转换成可用的变量名。我们可以在“加速度 1”、“加速度 2”、“加速度 3”等等中为每一个生成一个唯一的代码。但这并没有给我们立即可以理解的东西。如果我们能够从刚刚解析的表的 Item 列中提取相关信息，那就太好了。使用 TextBlob 我们可以。

Textblob (& nltk)是一个让自然语言处理变得轻而易举的包。为了得到我们想要的信息，我们需要做的就是将字符串转换成一个 TextBlob 对象。一旦我们完成了这些，我们就可以提取文本中所有的名词短语，并将它们转换成标识符。因为我们主要是在处理有形的“事物”,用它们的名词短语来指代它们是有意义的。项目文本中的其他内容都是胡扯。

```
import nltk
from textblob import TextBlob

# Download natural language processing libraries in order to use TextBlob
nltk.download('brown')
nltk.download('punkt')

def getNouns(string): 

 # Remove any information contained in brackets and any symbols other than alpha numeric ones
 # Then create the textblob object and extract the noun phrases.
 np = TextBlob(re.sub(r'[^a-zA-Z0-9\s]','',re.sub(r'\(.\*\)','',string))).noun\_phrases

 # If any noun phrases are found then join all these phrases into one large string. 
 # Split this string by anything that isn't alpha numeric, filter out anything else
 # that isn't alpha numeric. Capitalize each value and stick all these processed 
 # strings together. The result should be all the noun phrases in camelCase as one 
 # string. 
 if len(np)\>0:
 return ''.join([x.capitalize() for x in re.split('([^a-zA-Z0-9])',' '.join(np)) if x.isalnum()])

# Loop over our scraped orders of magnitude and create our noun phrase camelCase string
for area, data in OrdersOfMagnitude.items():

 # Apply the getNouns function
 data['id']=data['Detail'].apply(getNouns)
 # Throw away any values that were we couldn't create an id
 data=data[data['id'].notnull()] 
```

#### 品脱和发电配置

我们有数据，有标签。现在我们只需要把它扔进一品脱，我们就完成了。为了做到这一点，我们需要建立一个自定义的单位注册。

现在 python 中的一切都简单得可笑。按照他们详细的[指导](https://pint.readthedocs.io/en/latest/defining.html)我们可以看到，格式就是 id = value*unit。我们解析的大多数值都是在 Pint 的基本单位注册表中已经定义的单位。这意味着我们所有的定制单位都可以与 pint 提供的任何其他单位相互转换。他们还会表现出品脱[维度检查](https://pint.readthedocs.io/en/latest/wrapping.html#checking-dimensionality)。

```
# Iterate through the rows of our various magnitudes writing out to a text file
# string configurations for pint
with open('pintConfig.txt','w') as out:
 for area, data in OrdersOfMagnitude.items():
 for id, row in data.iterrows():
 pintConfig = '{} = {}\*1{} # {}\n'.format(row['id'],row['Value (Numeric)'],row['Unit'],row['Detail'])
 out.write(pintConfig) 
```

现在我们已经建立了我们的单位注册，我们可以做各种奇怪的转换。想看看如果把成年咸水鳄的一口咬在最大的阿根廷龙身上，能提供多少航天飞机的加速度吗？简单，看下面。

```
from pint import UnitRegistry

ureg = UnitRegistry()
ureg.load\_definitions('pintConfig.txt')

x = 1\*ureg.Large67mAdultSaltwaterCrocodile
acc = x/(1\*ureg.LargestArgentinosaurus)
print(acc.to('Shuttle')) 
```

答案？

```
0.01629664619744922 Shuttle 
```

抛开商业因素不谈，现在我们可以很容易地将任何计算、数据分析、表格或数组转换成更容易理解的数字。由于 Pint 很容易与 pandas 和 numpy 接口，它可以使数据的发布更加用户友好。在制作任何面向公众的数据分析时节省时间和精力。另外，你知道，0.016 梭，这是一个很大的咬。

*最初发表于* [*本杰明·科克兰*](https://benjamincorcoran.com/scraping-wikipedia-for-orders-of-magnitude/) *。*