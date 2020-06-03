title: redis源码阅读：map 哈希表

## map 字典
```
typedef struct dictht{
 dictEntry **table;
 unsigned long size;
 //哈希表大小掩码，用于计算索引值，总是等于size-1
 unsigned long sizemask;
 unsigned long used;
}dictht

typedef struct dictEntry{
    void *key;
    union{
        void *val;
        uint64_t u64;
        int64_t s64;

    }v;
    struct dictEntry *next;
}dictEntry;

typedef struct dict{
    dictType *type;
    void *privdata;
    //字典只使用ht[0]哈希表，ht[1]哈希表只会对ht[0]哈希表进行rehash时使用
    dictht ht[2];
    //rehash 索引
    int trehashidx;
}dict;
```

## 哈希算法
```
// 使用字典设置的哈希函数计算哈希值
hash = dict->type->hashFunction(key);
index = hash & dict->ht[x].sizemask;
```
>使用链地址法解决hash冲突。头插法。（插入时间复杂度为O(1))
### rehash
1. 为字典的ht[1] 哈希表分配空间，这个哈希表的空间大小取决于要执行的操作，以及ht[0]当前包含的键值对数量（ht[0].used)
    * 扩展： 第一个大于ht[0].used*2的2^n;
    * 收缩： ht[1]的大小为ht[0].used的2^n;
2. ht[0]中所有键值对rehash到ht[1]上面。然后放到ht[1]哈希表的指定位置上。
3. 全部迁移后，释放ht[0]的空间。ht[1]设置为ht[0],并在ht[1]新建一个空白哈希表，为下一次的rehash做准备。

### 负载因子
1. 服务器目前没有执行BGSAVE命令或者BGREWRIRTEAOF命令，并且哈希表负载因子大于等于1。
2. 服务正在执行BGSAVE或者BGREWRITEAOF命令，并且负载因子大于等于5。
```
load_factor = ht[0].used / ht[0].size
```
3. 负载因子小于0.1程序自动对哈希表执行收缩操作
