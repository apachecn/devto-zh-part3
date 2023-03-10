# 理解 Memcached 源代码- LRU II

> 原文：<https://dev.to/holmeshe/understanding-the-memcached-source-code---lru-ii-4oip>

[![](img/7c4eac2443aa17f5de9f0e916c990eac.png) slab 分配器(I，](https://dev.to/holmeshe/understanding-the-memcached-source-code---slab-i-165h) [ II，](https://dev.to/holmeshe/understanding-the-memcached-source-code---slab-ii-2kc3) [III)](https://dev.to/holmeshe/understanding-the-memcached-source-code---slab-iii-3h8l) 是缓存系统的核心模块，很大程度上决定了瓶颈资源——内存的利用效率。其他 3 个部分，即:

[![](img/d6aed08203351c706eec17806267e7b8.png) LRU 算法(I，](https://dev.to/holmeshe/understanding-the-memcached-source-code---lru-i-2pdm) II -本条)用于录入过期时间；和一个

[![](img/490f5358184017957ba4e8a0cf607710.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tzc6mlU3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/../gallery/club.svg) 基于 libevent 的事件驱动模型(不完整)；和

[![](img/06e0627871e52e5e85a37c2488182acf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g3N4Oenm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/../gallery/diamond.svg) 对数据分布一致苛刻(不完整)、

都是围绕着它建立的。

通常情况下， **LRU** 算法与**哈希映射**结合在一起，被称为 LRU 缓存。

在 **LRU 缓存**中，**哈希映射**支持快速访问*缓存的*对象；并且 **LRU** 通过标记过期的或者所谓的**最近最少使用的**对象来避免*缓存*无限增长。这次我们检查 **memcached** 对**哈希映射**的实现。

# 概述(课本重叠，跳过)

**哈希映射**基本上是一个*固定大小的*数组，它用整数**哈希** from _key_s 来索引 *value_s。在**哈希映射**中，一个数组条目被称为 a _bucket* 。如果**哈希值**超过桶数(即数组大小)，则*使用‘mod’(`%`)翻转*。*当两个以上的 key_s 导致同一个 **hash 值**或不同的 **hash 值** s 滚到同一个桶时，就会发生*碰撞，这时桶上就形成了一个*链表。

*冲突*降低了顺序访问*链表*的查找速度，因此需要增加*桶号*，并在性能恶化之前使用新的**桶号**对条目进行*重散列。这个过程马上就要讨论了。*

# 模块初始化

第一个相关的方法是

## hash_init

它只是确定哈希算法的类型。

```
int hash_init(enum hashfunc_type type) {
    switch(type) {
        case JENKINS_HASH:
            hash = jenkins_hash;
            settings.hash_algorithm = "jenkins";
            break;
        case MURMUR3_HASH:
            hash = MurmurHash3_x86_32;
            settings.hash_algorithm = "murmur3";
            break;
        default:
            return -1;
    }
    return 0;
} 
```

<center>[hash_init@hash.c](mailto:hash_init@hash.c)</center>

在逻辑进入主事件循环之前，从这里调用这个方法作为 init 步骤之一。

```
...
...
    if (hash_init(hash_type) != 0) {
        fprintf(stderr, "Failed to initialize hash_algorithm!\n");
        exit(EX_USAGE);
    }
... 
```

<center>[main@memcached.c](mailto:main@memcached.c):5849</center>

参数`hash_type`被前面提到的命令行参数 modern 设置为`MURMUR3_HASH`。

```
...
            case MODERN:
                /* Modernized defaults. Need to add equivalent no_* flags
                 * before making truly default. */
...
                hash_type = MURMUR3_HASH;
...
                break;
... 
```

<center>[main@memcached.c](mailto:main@memcached.c):5849</center>

第二种方法

## 关联 _ 初始化

分配开头提到的固定大小的数组。

```
void assoc_init(const int hashtable_init) {
    if (hashtable_init) {
        hashpower = hashtable_init;
    }
    primary_hashtable = calloc(hashsize(hashpower), sizeof(void *));
    if (! primary_hashtable) {
        fprintf(stderr, "Failed to init hashtable.\n");
        exit(EXIT_FAILURE);
    }
...// scr: stat
} 
```

<center>[hash_init@hash.c](mailto:hash_init@hash.c)</center>

作为系统引导过程的一部分，该方法在与`hash_init`相似的位置被调用。

```
...
    assoc_init(settings.hashpower_init);
... 
```

<center>[main@memcached.c](mailto:main@memcached.c):5976</center>

实际的初始大小由命令行参数`hashpower`决定。

```
...
case HASHPOWER_INIT:
    if (subopts_value == NULL) {
        fprintf(stderr, "Missing numeric argument for hashpower\n");
        return 1;
    }
    settings.hashpower_init = atoi(subopts_value);
    if (settings.hashpower_init < 12) {
        fprintf(stderr, "Initial hashtable multiplier of %d is too low\n",
            settings.hashpower_init);
        return 1;
    } else if (settings.hashpower_init > 64) {
        fprintf(stderr, "Initial hashtable multiplier of %d is too high\n"
            "Choose a value based on \"STAT hash_power_level\" from a running instance\n",
            settings.hashpower_init);
        return 1;
    }
    break;
... 
```

<center>[main@memcached.c](mailto:main@memcached.c):5677</center>

如前所述，如果由于过多的*冲突*导致性能下降，可以用新分配的更大的数组替换该数组。接下来我们讨论的是

# 扩大规模&入境迁移

在 **memcached** 中，阈值为 1.5，也就是说，如果**项** s 数超过 1.5 * **桶数**，则开始上述扩展过程。

[![Expand hash map](img/29f33fe46cea128730ff620f1831ab71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yWfG2p4C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wlqk1aedozzuq2u4o9p6.png)

```
...
    if (! expanding && hash_items > (hashsize(hashpower) * 3) / 2) {
        assoc_start_expand();
    }
... 
```

<center>[assoc_insert@assoc.c](mailto:assoc_insert@assoc.c):173</center>

`assoc_start_expand`简单地设置一个标志(即`do_run_maintenance_thread`，并发送一个信号来唤醒执行实际工作的维护线程。

```
static void assoc_start_expand(void) {
    if (started_expanding)
        return;

    started_expanding = true;
    pthread_cond_signal(&maintenance_cond);
} 
```

<center>[assoc_insert@assoc.c](mailto:assoc_insert@assoc.c):173</center>

## 维护线程主循环

```
static void *assoc_maintenance_thread(void *arg) {

  mutex_lock(&maintenance_lock);
  while (do_run_maintenance_thread/* scr: the flag*/) {
    int ii = 0;

    /* There is only one expansion thread, so no need to global lock. */
    for (ii = 0; ii < hash_bulk_move && expanding; ++ii) { // scr: ----> 2)
      item *it, *next;
      int bucket;
      void *item_lock = NULL;

      /* bucket = hv & hashmask(hashpower) =>the bucket of hash table
       * is the lowest N bits of the hv, and the bucket of item_locks is
       *  also the lowest M bits of hv, and N is greater than M.
       *  So we can process expanding with only one item_lock. cool! */
      if ((item_lock = item_trylock(expand_bucket))) { // scr: --------> 3)
        for (it = old_hashtable[expand_bucket]; NULL != it; it = next) {
          next = it->h_next; // scr: ----------------------------------> 4)
          bucket = hash(ITEM_key(it), it->nkey) & hashmask(hashpower);
          it->h_next = primary_hashtable[bucket];
          primary_hashtable[bucket] = it;
        }

        old_hashtable[expand_bucket] = NULL; // scr: ----------------> 4.1)

        expand_bucket++; // scr: --------------------------------------> 5)
        if (expand_bucket == hashsize(hashpower - 1)) { // scr: -------> 6)
          expanding = false;
          free(old_hashtable);
... // scr: ---------------------------------------------------> stat & log
        }
      } else {
        usleep(10*1000); // scr: ------------------------------------> 3.1)
      }

      if (item_lock) { // scr: --------------------------------------> 3.2)
        item_trylock_unlock(item_lock);
        item_lock = NULL;
      }
    }

    if (!expanding) {
      /* We are done expanding.. just wait for next invocation */
      started_expanding = false;
      pthread_cond_wait(&maintenance_cond, &maintenance_lock); // scr: > 0)
      /* assoc_expand() swaps out the hash table entirely, so we need
       * all threads to not hold any references related to the hash
       * table while this happens.
       * This is instead of a more complex, possibly slower algorithm to
       * allow dynamic hash table expansion without causing significant
       * wait times.
       */
      pause_threads(PAUSE_ALL_THREADS);
      assoc_expand(); // scr: -----------------------------------------> 1)
      pause_threads(RESUME_ALL_THREADS);
    }
  }
  return NULL;
} 
```

<center>[assoc_maintenance_thread@assoc.c](mailto:assoc_maintenance_thread@assoc.c)</center>

0)这是线程从睡眠中等待并开始工作的地方，并且当没有事情要做时进入睡眠。

1) assoc_expand 为新的**散列图**分配资源，这意味着替换从这里初始化的旧散列图。

```
/* grows the hashtable to the next power of 2\. */
static void assoc_expand(void) {
    old_hashtable = primary_hashtable;

    primary_hashtable = calloc(hashsize(hashpower + 1), sizeof(void *));
    if (primary_hashtable) {
... // scr: log
        hashpower++;
        expanding = true;
        expand_bucket = 0;
... // scr: stat
    } else {
        primary_hashtable = old_hashtable;
        /* Bad news, but we can keep running. */
    }
} 
```

<center>[assoc_expand@assoc.c](mailto:assoc_expand@assoc.c)</center>

2)一次只迁移一定数量的**项**。hash_bulk_move 避免了调用 stop_assoc_maintenance_thread 时线程挂起时间过长。与讨论的`assoc_start_expand`相反，stop_assoc_maintenance_thread 重置标志`do_run_maintenance_thread`并发送信号唤醒线程退出。

```
#define DEFAULT_HASH_BULK_MOVE 1 int hash_bulk_move = DEFAULT_HASH_BULK_MOVE; 
```

<center>[assoc.c:207](https://play.google.com/store/apps/details?id=me.holmeshe.sc)</center>

```
...
    char *env = getenv("MEMCACHED_HASH_BULK_MOVE");
    if (env != NULL) {
        hash_bulk_move = atoi(env);
        if (hash_bulk_move == 0) {
            hash_bulk_move = DEFAULT_HASH_BULK_MOVE;
        }
    }
... 
```

<center>[start_assoc_maintenance_thread@assoc.c](mailto:start_assoc_maintenance_thread@assoc.c):281</center>

```
void stop_assoc_maintenance_thread() {
    mutex_lock(&maintenance_lock);
    do_run_maintenance_thread = 0;
    pthread_cond_signal(&maintenance_cond);
    mutex_unlock(&maintenance_lock);

    /* Wait for the maintenance thread to stop */
    pthread_join(maintenance_tid, NULL);
} 
```

<center>[stop_assoc_maintenance_thread@assoc.c](mailto:stop_assoc_maintenance_thread@assoc.c)</center>

3)(“物品锁”实际上是对整个*桶*起作用的，所以我改称之为*桶锁*)使用低优先级的 item_trylock ( `i.e., pthread_mutex_trylock`)来访问*桶锁*；3.1)当**项**不可用时，休眠 10 秒；和 3.2)当迁移(该*桶*的)完成时，使用 item_trylock_unlock 释放锁。

```
void *item_trylock(uint32_t hv) {
    pthread_mutex_t *lock = &item_locks[hv & hashmask(item_lock_hashpower)];
    if (pthread_mutex_trylock(lock) == 0) {
        return lock;
    }
    return NULL;
} 
```

<center>[item_trylock@thread.c](mailto:item_trylock@thread.c)</center>

```
void item_trylock_unlock(void *lock) {
    mutex_unlock((pthread_mutex_t *) lock);
} 
```

<center>[item_trylock@thread.c](mailto:item_trylock@thread.c)</center>

4)将桶中的所有**项**重新散列，并将它们迁移到新的散列映射中。

5)移动到下一个*桶*。

6)到达最后一个*桶*->转到 0)

## 维护线程启动

```
int start_assoc_maintenance_thread() {
    int ret;
    char *env = getenv("MEMCACHED_HASH_BULK_MOVE");
    if (env != NULL) {
        hash_bulk_move = atoi(env);
        if (hash_bulk_move == 0) {
            hash_bulk_move = DEFAULT_HASH_BULK_MOVE;
        }
    }
    pthread_mutex_init(&maintenance_lock, NULL);
    if ((ret = pthread_create(&maintenance_tid, NULL,
                              assoc_maintenance_thread, NULL)) != 0) {
        fprintf(stderr, "Can't create thread: %s\n", strerror(ret));
        return -1;
    }
    return 0;
} 
```

<center>[start_assoc_maintenance_thread@assoc.c](mailto:start_assoc_maintenance_thread@assoc.c)</center>

类似于初始化方法，它在系统引导期间被调用。

```
...
if (start_assoc_maintenance_thread() == -1) {
    exit(EXIT_FAILURE);
}
... 
```

<center>[main@memcached.c](mailto:main@memcached.c):5992</center>

## 维护线程停止

该方法在系统关闭过程中被调用，因此在逻辑上与`start_assoc_maintenance_thread`相反。然而，该方法的操作与`assoc_start_expand`机制相反。

```
...
    stop_assoc_maintenance_thread();
... 
```

<center>[main@memcached.c](mailto:main@memcached.c):6098</center>

```
void stop_assoc_maintenance_thread() {
    mutex_lock(&maintenance_lock);
    do_run_maintenance_thread = 0;
    pthread_cond_signal(&maintenance_cond);
    mutex_unlock(&maintenance_lock);

    /* Wait for the maintenance thread to stop */
    pthread_join(maintenance_tid, NULL);
} 
```

<center>[stop_assoc_maintenance_thread@assoc.c](mailto:stop_assoc_maintenance_thread@assoc.c)</center>

如前所述，这里讨论的扩展和迁移过程会对所有与 **hash map** 相关的操作的逻辑产生影响。在下一节中，我们来看看这些操作。

# 积垢

注意，`assoc_delete`已经在[上一篇](https://holmeshe.me/understanding-memcached-source-code-IV/#assoc-delete-remove-from-hash-map)中讨论过；而在键值系统中*更新*和*插入*本质上是相同的，因此，本节将只讨论 *C* (创建)和 *R* (读取)的操作。

## 关联 _ 插入

```
int assoc_insert(item *it, const uint32_t hv) {
    unsigned int oldbucket;

    if (expanding &&
        (oldbucket = (hv & hashmask(hashpower - 1))) >= expand_bucket)
    {
        it->h_next = old_hashtable[oldbucket]; // scr: -------------------> 1)
        old_hashtable[oldbucket] = it;
    } else {
        it->h_next = primary_hashtable[hv & hashmask(hashpower)]; // scr: > 2)
        primary_hashtable[hv & hashmask(hashpower)] = it;
    }

    pthread_mutex_lock(&hash_items_counter_lock);
    hash_items++; // scr: ------------------------------------------------> 3)
    if (! expanding && hash_items > (hashsize(hashpower) * 3) / 2) {
        assoc_start_expand();
    }
    pthread_mutex_unlock(&hash_items_counter_lock);

    MEMCACHED_ASSOC_INSERT(ITEM_key(it), it->nkey, hash_items);
    return 1;
} 
```

<center>[assoc_insert@assoc.c](mailto:assoc_insert@assoc.c)</center>

1)如果扩展过程正在进行，并且*哈希键*关联的桶没有被迁移，则将该项插入`old_hashtable`。注意，这里我们使用旧的*桶号*(即`hashmask(hashpower - 1))`)来计算哈希索引。

2)否则，直接将**项**插入到`primary_hashtable`中。

3)增加全局变量 hash_items(第**项**的个数)。添加**项**后，如果超过阈值，则开始扩展&迁移过程。注意，这也是上一节的序言。

```
...
static unsigned int hash_items = 0;
... 
```

<center>[assoc.c:51](https://play.google.com/store/apps/details?id=me.holmeshe.sc)</center>

## 关联 _ 查找

```
item *assoc_find(const char *key, const size_t nkey, const uint32_t hv) {
    item *it;
    unsigned int oldbucket;

    if (expanding &&
        (oldbucket = (hv & hashmask(hashpower - 1))) >= expand_bucket)
    {
        it = old_hashtable[oldbucket]; // scr: ---------------------------> 1)
    } else {
        it = primary_hashtable[hv & hashmask(hashpower)]; // scr: --------> 2)
    }

    item *ret = NULL;
    int depth = 0;
    while (it) { // scr: -------------------------------------------------> 3)
        if ((nkey == it->nkey) && (memcmp(key, ITEM_key(it), nkey) == 0)) {
            ret = it;
            break;
        }
        it = it->h_next;
        ++depth;
    }
    MEMCACHED_ASSOC_FIND(key, nkey, depth);
    return ret;
} 
```

<center>[assoc_find@assoc.c](mailto:assoc_find@assoc.c)</center>

1)类似于 assoc_insert 的步骤，当键还没有被重新散列时，该步骤从`old_hashtable`定位桶。

2)否则直接使用`primary_hashtable`。

3)在*冲突*的情况下，遍历*链表*，直接比较*键*(而不是*哈希索引*)查找**项**。

有一点值得注意的是，`assoc_find`和[上一篇](https://holmeshe.me/understanding-memcached-source-code-IV/#assoc-delete-remove-from-hash-map)已经讨论过的`_hashitem_before`非常相似。这里的区别是，`_hashitem_before`返回找到的 ( `pos = &(*pos)->h_next;`)之前的元素的下一个成员的**地址，这是从*单链表*中删除条目时需要的；而这个方法返回直接找到的元素(`ret = it;`)。**