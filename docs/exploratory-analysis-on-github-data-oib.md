# GitHub 数据的探索性分析

> 原文：<https://dev.to/n1try/exploratory-analysis-on-github-data-oib>

# 背景

几天前，我写了一个爬虫(带有 NodeJS 和 [Sequelize](http://docs.sequelizejs.com/) )，它从 GitHub 的 [GraphQL API](https://developer.github.com/v4/) 中获取公开可用的数据。更准确地说，我下载了关于用户、存储库、编程语言和主题的信息。

运行爬虫几天后，我得到了 **154，248 个用户档案**、 **993，919 个库**和 **351 种语言**，其中许多我从未听说过(例如，你知道 *PogoScript* 吗？).然而，尽管我的 MySQL 数据库已经有 953 MB 的大小，只有这些数据，但我只抓取了所有用户资料的 0.4 %(约 3100 万)。

我的数据库的第一个(不太广泛的)版本——我对其进行了以下分析——如下所示。

[![](img/d5a9d1db37e74c354ab6b9f59513043d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pK7deNF---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://muetsch.img/gh_eer.png)

虽然有人可能会说我收集的数据不具有代表性，但我仍然想对它进行一些数据分析——只是为了好玩。

# 分析

为了进行分析，我将 Python 3 与 Pandas 和 Matplotlib 结合使用。

```
import apriori
import pymysql
import pandas as pd
import matplotlib.pyplot as plt
from sqlalchemy import create_engine

%matplotlib inline

pymysql.install_as_MySQLdb()

sql_engine = create_engine('mysql://user:heheyouwish@localhost:3306/github_data', echo=False)
connection = sql_engine.raw_connection() 
```

Enter fullscreen mode Exit fullscreen mode

## 最流行的编程语言

首先要检查的也是最明显的事情之一(为了简洁起见，我将跳过基本的数据集统计，如计数、平均值、方差，...)就是哪些语言使用最广泛。

```
df_top_langs = pd.read_sql_query('''
    select LanguageName, count(LanguageName) as count from RepositoryLanguages
    group by LanguageName
    order by count(LanguageName) desc
    limit 10;
''', con=connection)
df_top_langs.set_index('LanguageName').plot.bar(figsize=(12,8)) 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/84b078bfd9e44551f4033676cf17bdd5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qPjCRDr0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://muetsch.img/gh_popular_lang.png)

不足为奇的是，由 JavaScript、HTML 和 CSS 组成的典型 web 堆栈是最受欢迎的编程语言之一，这取决于它们在存储库中出现的频率。

## 最不受欢迎的编程语言

更有趣的是看看，哪些编程语言出现的最少。

```
df_last_langs = pd.read_sql_query('''
    select LanguageName, count(LanguageName) as count from RepositoryLanguages
    group by LanguageName
    order by count(LanguageName) asc
    limit 10;
''', con=connection)
print(df_last_langs) 
```

Enter fullscreen mode Exit fullscreen mode

这是结果。你听说过他们中的任何一个吗？我没有。

```
 LanguageName  count
0          Nit      1
1       Myghty      1
2   Public Key      1
3  DCPU-16 ASM      1
4   TI Program      1
5        Genie      1
6           Ox      1
7   PogoScript      1
8        Cirru      1
9        JFlex      2 
```

Enter fullscreen mode Exit fullscreen mode

# 用户技能

让我们从语言的角度来分析用户的技能。我决定，如果一个用户至少有 10 %的存储库代码是用某种语言编写的，我就认为这个用户“精通”这种语言。

```
N = int(1e7)
MIN_SUPP = .0005
MIN_CONF = .45
MIN_LANG_RATIO = .1

df_skills = pd.read_sql_query(f'''
    select RepositoryLanguages.LanguageName, RepositoryLanguages.size, Users.login, Users.location from RepositoryLanguages
    left join Repositories on Repositories.id = RepositoryLanguages.RepositoryId
    right join Users on Users.login = Repositories.userLogin
    limit {N} ''', con=connection)

df_skills = df_skills.merge(pd.DataFrame(df_skills.groupby('login')['size'].sum()), how='left', on='login').rename(columns={'size_x': 'size', 'size_y': 'totalSize'})
df_skills = df_skills[df_skills['totalSize'] > 0]
df_skills['sizeRatio'] = df_skills['size'] / df_skills['totalSize']

print(f"{df_skills['login'].unique().size} users")
print(f"{df_skills['LanguageName'].unique().size} languages")

# Output:
# 130402 users
# 351 languages 
```

Enter fullscreen mode Exit fullscreen mode

### 关联规则

我想看的是不同技能的组合，即通常作为开发人员技能一起出现的语言。获得这种洞察力的一种方法是挖掘数据中的*关联规则*，例如使用像 [Apriori](https://en.wikipedia.org/wiki/Apriori_algorithm) 这样的算法(正如我所做的)。我使用的实现是 [asaini/Apriori](https://github.com/asaini/Apriori) 。

```
user_langs = df_skills[df_skills['sizeRatio'] >= MIN_LANG_RATIO].groupby('login')['LanguageName'].apply(set).values
items1, rules1 = apriori.runApriori(user_langs, MIN_SUPP, MIN_CONF)
rules1 = sorted(rules1, key=lambda e: e[1], reverse=True)
print(rules1) 
```

Enter fullscreen mode Exit fullscreen mode

**输出:**

```
[((('ShaderLab',), ('C#',)), 0.904),
 ((('Vue',), ('JavaScript',)), 0.671277997364954),
 ((('Vue', 'CSS'), ('JavaScript',)), 0.656140350877193),
 ((('GLSL',), ('C#',)), 0.625),
 ((('CMake',), ('C++',)), 0.6229508196721312),
 ((('CSS',), ('JavaScript',)), 0.5807683959192532),
 ((('Tcl',), ('Python',)), 0.5658914728682171),
 ((('Kotlin',), ('Java',)), 0.5655375552282769),
 ((('ASP',), ('C#',)), 0.5488215488215488),
 ((('Vue', 'HTML'), ('JavaScript',)), 0.5404411764705882),
 ((('CoffeeScript',), ('JavaScript',)), 0.5339578454332553),
 ((('CSS', 'PHP'), ('JavaScript',)), 0.5116117850953206),
 ((('Elm',), ('JavaScript',)), 0.4951923076923077),
 ((('CSS', 'HTML'), ('JavaScript',)), 0.4906486271388778),
 ((('Smarty',), ('PHP',)), 0.4788732394366197),
 ((('TypeScript',), ('JavaScript',)), 0.4739540607054964),
 ((('CSS', 'C#'), ('JavaScript',)), 0.464926590538336),
 ((('Groovy',), ('Java',)), 0.4604651162790698)] 
```

Enter fullscreen mode Exit fullscreen mode

每一行的左边部分是表示关联规则的编程语言元组的元组。右边部分是该规则的[置信度](https://en.wikipedia.org/wiki/Association_rule_learning#Confidence)。

**比如:**
把`((('ShaderLab',), ('C#',)), 0.904)`读作“所有知道 *ShaderLab* 的人，90 %也知道 C#”。

结果反映了常识。例如，开发人员知道 *VueJS* ，也知道 *JavaScript* 的规则似乎是有道理的，因为 VueJS 是一个 JavaScript 框架。类似的， *CMake* 是 *C++* 等的常用构建工具。这里没什么特别的，除了我不知道的*沙得拉布*和 *GLSL* 。

## 地点

我们来看看大多数 GitHub 用户来自哪里。显然，这只考虑用户设置了位置的配置文件。

```
df_locations = df1.reindex(['location'], axis=1).groupby('location').size()
df_locations = df_locations.sort_values(ascending=False)[:20]
df_locations.plot.bar(figsize=(12,8)) 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/256a7789384fce2ae44e1b8608247b3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mUCOCjuS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://muetsch.img/gh_locations.png)

显然，旧金山似乎是最受开发者欢迎的城市(或者至少对那些活跃在 GitHub 上的人来说)。

## 技能按位置排列

为了更进一步，我们来看看用户在哪些城市倾向于拥有哪些技能。

```
def language_replace(df):
    df = df.copy()
    # Little bit of manual cleaning
    replace = {'San Francisco': 'San Francisco, CA',
               'Berlin': 'Berlin, Germany',
               'New York': 'New York, NY',
               'London': 'London, UK',
               'Beijing': 'Beijing, China',
               'Paris': 'Paris, France'}
    for (k, v) in replace.items():
        if isinstance(df, pd.DataFrame):
            if k in df.columns and v in df.columns:
                df[k] = df[k] + df[v]
                df = df.drop([v], axis=1, errors='ignore')
        else:
            if k in df.index and v in df.index:
                df[k] = df[k] + df[v]
                #df = df.drop([v], axis=1)
                del df[v]
    return df

langs_by_loc = {}
for l in df_locations.index:
    langs_by_loc[l] = df1[df1['location'] == l][['LanguageName']].groupby('LanguageName').size()
df_loc_langs = pd.DataFrame.from_dict(langs_by_loc).fillna(0)

df_loc_langs = language_replace(df_loc_langs)
df_loc_langs = df_loc_langs.T
df_loc_langs = df_loc_langs.drop([c for c in df_loc_langs.columns if c not in df_top_langs['LanguageName'].values], axis=1)

df_loc_langs = (df_loc_langs.T / df_loc_langs.T.sum()).T # normalize heights df_loc_langs.plot.bar(stacked=True, figsize=(16,10)) 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/1f3a63dc6a47f0e1f2f469b84512c002.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VqP4UO1E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://muetsch.img/gh_location_langs.png)

看起来在世界不同城市之间的开发人员技能分布中没有真正的异常值。也许你可以这么说，例如，印度人喜欢 web 前端比命令行黑客多一点。

## 技能:卡尔斯鲁厄 vs 世界

虽然概览很酷，但我发现更有趣的是具体比较城市。所以在下面的图表中，我将卡尔斯鲁厄(我生活和学习的城市)的特定语言编程技能与世界其他地方的平均水平进行了比较。

```
df_ka = df1[df1['location'] == 'Karlsruhe'][['LanguageName']].groupby('LanguageName').size()
df_ka = pd.DataFrame(df_ka, index=df_ka.index, columns=['Karlsruhe']) / df_ka.sum()
df_world = pd.DataFrame(df_loc_langs.mean(), index=df_loc_langs.mean().index, columns=['World'])
df_compare = df_world.merge(df_ka, how='left', left_index=True, right_index=True)
ax = df_compare.plot.barh(title='Languages: World vs. Karlsruhe', legend=True, figsize=(10,5))
ax.set_xlabel('Percentage (Top 10)')
ax.set_ylabel('Programming Language Skills') 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/e084e979d72377b35790c75a73087598.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nUiULrCO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://muetsch.img/gh_ka_world.png)

这些结果让我有点吃惊。显然，与这个世界相比，基于卡尔斯鲁厄的开发人员似乎不喜欢 JavaScript。但是，这和我在这里的几份学生工作和实习经历是不一样的。

## 项目技术堆栈

最后但同样重要的是，让我们再次应用先验知识，但这一次的方式略有不同。让我们不要看用户技能，而是看在每个存储库的基础上一起出现的语言。而不是试图寻找规则，让我们只看*频繁项集*(它们是规则的基础)。我的期望是取回常用的技术堆栈。

```
N = int(1e7)
MIN_SUPP = .0005
MIN_CONF = .45
MIN_LANG_RATIO = .1

df_stacks = pd.read_sql_query(f'''
    select LanguageName, size, RepositoryId from RepositoryLanguages
    order by RepositoryId
    limit {N} ''', con=connection)

df_stacks = df_stacks.merge(pd.DataFrame(df_stacks.groupby('RepositoryId')['size'].sum()), how='left', on='RepositoryId').rename(columns={'size_x': 'size', 'size_y': 'totalSize'})
df_stacks = df_stacks[df_stacks['totalSize'] > 0]
df_stacks['sizeRatio'] = df_stacks['size'] / df_stacks['totalSize']

print(f"{df_stacks['RepositoryId'].unique().size} repositories")
print(f"{df_stacks['LanguageName'].unique().size} languages")

# Output: 
# 853114 repositories
# 351 languages 
```

Enter fullscreen mode Exit fullscreen mode

```
repo_langs = df_stacks[df_stacks['sizeRatio'] >= MIN_LANG_RATIO].groupby('RepositoryId')['LanguageName'].apply(set).values
items2, rules2 = apriori.runApriori(repo_langs, MIN_SUPP, MIN_CONF)
itemsets2 = sorted(list(filter(lambda i: len(i[0]) > 2, items2)), key=lambda i: i[1], reverse=True)
print(itemsets2) 
```

Enter fullscreen mode Exit fullscreen mode

**输出:**

```
[(('CSS', 'JavaScript', 'HTML'), 0.04360026913167525),
 (('CSS', 'JavaScript', 'PHP'), 0.0045574213997191465),
 (('Ruby', 'CSS', 'HTML'), 0.004456614239128651),
 (('TypeScript', 'JavaScript', 'HTML'), 0.0042034241613664765),
 (('TypeScript', 'HTML', 'CSS'), 0.0035024627423767517),
 (('Python', 'JavaScript', 'HTML'), 0.002962089474560258),
 (('Python', 'HTML', 'CSS'), 0.002769852563666755),
 (('Ruby', 'JavaScript', 'HTML'), 0.0022400288824236856),
 (('JavaScript', 'HTML', 'PHP'), 0.0022154131804190294),
 (('Ruby', 'CSS', 'JavaScript'), 0.0021532878372644217),
 (('CSS', 'HTML', 'PHP'), 0.0019915275098052547),
 (('JavaScript', 'Objective-C', 'Java'), 0.0018614159420663593),
 (('CSS', 'JavaScript', 'Python'), 0.0017992905989117516),
 (('Python', 'JavaScript', 'Objective-C'), 0.0017735027206211597),
 (('Python', 'JavaScript', 'Java'), 0.001508590879999625),
 (('CSS', 'JavaScript', 'TypeScript'), 0.0014745977677074812),
 (('Python', 'Objective-C', 'Java'), 0.0014066115431231934),
 (('Python', 'JavaScript', 'Objective-C', 'Java'), 0.0013222148505358019),
 (('Vue', 'CSS', 'JavaScript'), 0.0012554008022374501)] 
```

Enter fullscreen mode Exit fullscreen mode

这里，左边是频繁出现的语言组合。右边是集合的[支持度](https://en.wikipedia.org/wiki/Association_rule_learning#Support)，是该集合在整个数据集中的相对出现次数。
很明显，其中许多实际上是常见的“技术栈”,几乎所有的都是 web 技术。我猜 GitHub 在 web 开发者中最受欢迎。

# 结论

有很多更复杂的分析可以在像这样的丰富数据上进行，可能像 [BigQuery](https://cloud.google.com/bigquery/public-data/) 这样的工具比 Pandas 更适合在小样本上操作。然而，我用这个小项目来提高我的 EDA 技能，并希望给你们一篇有趣的文章阅读。喜欢就告诉我！-

最初发布于 [muetsch.io](https://muetsch.io/exploratory-analysis-on-github-data.html)