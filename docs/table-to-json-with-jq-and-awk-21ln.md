# 带有 jq 和 awk 的 json 表

> 原文：<https://dev.to/foursixnine/table-to-json-with-jq-and-awk-21ln>

## 问题

假设您有一个如下所示的表:

```
AGGREGATE_NEEDED 1
ARCH x86_64
BASE_TEST_ISSUES NUMBER
BUILD :NUMBER:PACKAGE
DISTRI DISTRIBUTION
FLAVOR Server-DVD-Incidents-Install
INCIDENT_ID 99999 
```

只是它包含了大约 78 个甚至更多的条目。当然，对于一个非常熟练的工程师或一个有很多技巧的人来说，这可能是 vim 中一个非常琐碎的任务，或者类似这样的事情，我想通过一些替换，你会有所收获；但是除了吃饭，我不擅长。

## 解

所以我把我的键值表保存到一个文件中，在谷歌了一下之后，现在我对 awk :D 更加熟悉了:

```
cat FILE.txt | \ 
    awk 'BEGIN { print "{" } \ 
        { printf "\"%s\":\"%s\",", $1,$2} \ 
        END { print "\"MANUALLY_GENERATED_ISO_POST\":1 }" }'
        | jq > x86_64-ready.json'"}' 
```

我想这本来可以做得更容易更漂亮，但符合我的需要，在某些时候也可能救你。确保你已经安装了`jq`好吗？