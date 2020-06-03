title:redisObject


### redis 的 对象
Redis 使用对象来表示数据库中的键和值，每次当我们在redis的数据库创建一个键值对时，至少会创建两个对象，一个键对象，一个值对象。
```set msg "hello world"```

### redisObject 的对象结构
```
typedef struct redisObject{
    //类型  REDIS_STRING/REDIS_LIST/REDIS_HASH/REDIS_SET/REDIS_ZSET
    unsigned type: 4;
    //编码  REDIS_ENCODING_INT/REDIS_ENCODING_EMBSTR/REDIS_ENCODING_RAW/REDIS_ENCODING_HT/REDIS_ENCODING_LINKEDLIST/REDIS_ENCODING_ZOPLIST/REDIS_ENCODING_INTSET/SKIPLIST
    unsigned encoding:4;
    //指向底层实现数据结构的指针
    void *ptr;
    //..
}
```
>使用object encoding 可以实现数据库键的值对象的编码
