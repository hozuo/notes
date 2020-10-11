## AUTH <password> called without any password configured for the default user. Are you sure your configuration is correct?

redis没有设置密码但是java填写了密码，将密码设为null可以解决

## redis特性详解

### redis的缓存淘汰策略

缓存淘汰指的是redis使用的内存超过了设置的最大内存限制时，redis自身删除键值对使用的策略。可以选择LRU，LFU，随机。

LRU（Least Recently Used）最近最少使用算法，理论上指的是删除最长时间没有使用过的数据，redis使用了一种近似实现，在每个KEY中维护了一个系统时钟，进行LRU时从全部集合中抽取一定数量的样本，比较样本和系统时钟的差值中差距最大的数据删除。这个样本数量默认为5，删除的数据在冷数据和温数据中成散列分布；最大支持取10，很接近严格LRU的结果，但是代价是更高的CPU开销。

LFU（Least Frequently Used）最少使用频率算法，旨在删除使用频率最少的数据，redis在KEY中维护一个系统时钟和一个8位的使用频率计数器，每次命中这个值会有增加趋势，一段时间不使用就会衰减，增加的幅度和衰减的间隔可以通过影响因子和衰减因子进行设置。这样可以大致判断出一个数据当前的使用频率。

随机就是在全部集合中随机抽取一个键删除，如果没有任何一个键比另一个键更重要就选择这种算法，减少cpu的开销。以上三种算法均支持使用全部KEY或者设置了过期时间的KEY作为全部候选集合。

还可以将ttl最小的一个键删除，也可以拒绝删除KEY（默认），报出OOM并让redis无响应，总共8种设置。

```redis
volatile-lru -> Evict using approximated LRU, only keys with an expire set.

allkeys-lru -> Evict any key using approximated LRU.

volatile-lfu -> Evict using approximated LFU, only keys with an expire set.

allkeys-lfu -> Evict any key using approximated LFU.

volatile-random -> Remove a random key having an expire set.

allkeys-random -> Remove a random key, any key.

volatile-ttl -> Remove the key with the nearest expire time (minor TTL)

noeviction -> Don't evict anything, just return an error on write operations.
```

参考资料：https://www.jianshu.com/p/c8aeb3eee6bc