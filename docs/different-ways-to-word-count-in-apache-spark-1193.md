# apache spark 中不同的字数统计方法

> 原文：<https://dev.to/yashwanth2804/different-ways-to-word-count-in-apache-spark-1193>

#### Hi 大数据 Devs，

当谈到为大数据框架提供一个例子时，WordCount 程序就像一个 hello world 程序。主要原因是它为初学者提供了一个地图-洗牌-还原的快照。在这里，我提供了不同的方法来实现它

## ReduceByKey(转换)

*返回类型与输入 RDD 类型*相同

* * *

```
JavaRDD<String> file = sc.textFile("/<Path_To_File>/README.md");

JavaRDD<String> words = file
.flatMap(f -> Arrays.asList(f.split(" ")).iterator())
.filter(f -> !f.isEmpty());

// grouping words with number 1
JavaPairRDD<String,Integer> wordMap_to_pair = words
.mapToPair(f -> new Tuple2<String,Integer>(f,1) );

//ReduceBykey,will merge at partition level and sends result to driver
JavaPairRDD<String,Integer> reducebyKey = wordMap_to_pair.reduceByKey((a,b) -> a+b);

System.out.println(reducebyKey.collect()); 
```

## foldByKey(变换)

* * *

它类似于 reeducebykey，但取零值。用户不需要指定组合器

```
 JavaPairRDD<String, Integer> Foldbykey = wordMap_to_pair.foldByKey(0, ((acc, val) -> acc+val));

System.out.println(Foldbykey.collect()); 
```

## aggregateByKey(转换)

* * *

*返回类型不必与输入 RDD 类型*
*参数*相同

**initalValue**@原始类型。`0`在加法和减法中，`1`在乘法/除法中，`[]`在链表上，`""`在字符串上

**sequence function**@在分区级别内操作，

**combiner function**@跨分区级别

```
JavaPairRDD<String,Integer> aggreagteBykey = wordMap_to_pair.aggregateByKey(
0 // initial value
, ( (a,v) -> a+v ) //seq,counting operation within partition
, ( (a,v) -> a+v )); // merge, counting operation across partition

System.out.println( aggreagteBykey.collect()); 
```

## CombineByKey(转换)

* * *

与 aggregate 和 fold bykeys 不同，用户可以指定一个组合器函数并自定义组合行为。

*返回类型不必与输入 RDD 类型*相同

*参数*

函数接受当前值并返回新值

**merge value**@在分区级别内合并/组合值

**merge combiners**@跨分区级别合并/组合值

```
 wordMap_to_pair.combineByKey(
i -> i, //createCombiner
(a,v) -> (a+v) //mergeValue
,(a,b) -> a+b) //mergeCombiners
.collect()
.forEach(System.out::println); 
```

## groupByKey(变换)

* * *

返回一组键和值的集合。跨分区执行散列连接

```
 JavaPairRDD<String,Iterable<Integer>> groupByKey = wordMap_to_pair.groupByKey();

groupByKey
.mapValues( v -> Iterables.size(v)) // [1,1,1,1] -> [4]
.collect()
.forEach(System.out:println); 
```

* * *