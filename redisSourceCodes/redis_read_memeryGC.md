title：内存回收

## reids 的 内存回收问题 
> C 语言不具备自动内存回收的功能。

### GC 算法
> redis 采用 引用计数 技术实现内存回收机制

### 引用计数法
> 当一个键创建了包含整数值100的字符串作为对象，如果另一个键也要创建一个同样保存了整数值100的字符串对象作为值对象，那么服务器会让两个键共享同一个对象。

### 对象的空转时长 lru
>lru 属性记录对象最后一次被命令程序访问的时间

```
typedef struct redisObject{
    type;
    encoding;
    ptr;
    refcount;
    lru;
}

```