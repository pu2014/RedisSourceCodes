## 服务器中的数据库
>Redis 服务器将所有数据库都保存在redisServer结构中
```
struct redisServer{
    //。。
    //一个数组 16位的保存服务器中的所有数据库
    redisDb *db;
    //服务器的数据库数量
    int dbnum;
    //..
}

struct redisDb{
    //数据库键空间，保存着数据库中的所有键值对
    dict *dict;
    //键空间的值也就是数据库的键，每个键都是一个字符串对象。
    //键空间的值也就是数据库的值，每个值可以是字符串、列表、哈希、集合、有序集合的对象

    //过期字典，保存键的过期时间
    dict *expires;
} redisDb;
```
1. 增加更新键 SET 
2. 删除键 DEL
3. 取值 GET LRANGE
4. 清空 FLUSHDB
5. 过期时间设置 EXPIRE PEXPIRE SETEX

## 过期键的删除和更新
> Redis 采用惰性删除和定期删除两种策略来删除过期的键：惰性删除策略只在碰到过期键才删除，定期删除策略每隔一段时间主动查找并删除过期键
1. SAVE BGSAVE 所产生的新RDB文件不会包含已过期的键
2. BGREWRITEAOF 所产生的AOF文件不会产生已过期的键
3. 当一个过期键被删除后，服务器会追加DEL语句到AOF文件的末尾
