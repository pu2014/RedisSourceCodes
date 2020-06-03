>RDB 是一个压缩的二进制文件，实现 Redis 的持久化
## RDB文件生成
> save & bgsave
```
def SAVE():
    rdbSave();
def BFSAVE():
    pid = fork();
    if pid == 0 :
        rdbSave()
        signal_parent();
    elif pid > 0
        handle_request_and_wait_signal()
    else:
        handle_fork_error()
```
1. save: 客户端的所有命令请求都会被拒绝
2. BGREWRITESAOF时， BGSAVE会被拒绝
3. BGSAVE时，BGREWRITESAOF会等待BGSAVE完成再执行

### RDB 与 AOF
>由于AOF更新频率比RDB高，所以：
1. 开启AOF持久化，服务器会优先使用AOF文件来还原数据库状态
2. 不开启AOF持久化，服务器才会选择RDB文件来还原数据库状态

### RDB文件结构
>REDIS 文件结构： REDIS DB_VERSION databases EOF check_sum

### 分析 RDB 文件
> od -c dump.rdb
