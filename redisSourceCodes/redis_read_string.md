title: redis 源码阅读—string与SDS


## SDS
```
struct sdshdr{
    //记录buf数组中已使用字节的数量
    //等于SDS所保存的字符串长度
    int len;
    //记录buf数组中未使用的字节的数量
    int free;
    //字节数组，用于保存字符串
    char buf[];
}
```
>优点1： 常数复杂度获取字符串的长度

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;C字符串中不记录字符串的自身长度信息，获取长度必须遍历整个字符串。时间复杂度O(n)<br>
>优点2： 杜绝缓冲区溢出

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;C语言不记录自身长度，调用```char* strcat(char *dest, const char *src);``` 容易产生缓冲区溢出。而SDS有free参数先检查SDS的空间是否足够，不够的话会先扩展其空间。
>优点3：减少修改字符串带来的内存重分配次数

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;C字符串执行增长或者缩短字符串的操作需要内存的重新分配。而SDS采用空间预分配和惰性空间释放两种优化策略。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 1. 空间预分配：如果修改后长度小于1MB，程序会分配和len属性大小相同的未使用空间。如果修改后的长度为12，则会分配 12 + 12 + 1 = 25的字节（额外1字节保存空字符串）。如果大于1MB，程序会分配1MB的未使用空间，如果修改后的SDS长度为20MB，则buff数组的实际长度为30MB + 1MB + 1byte。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.  惰性空间释放，不主动释放空间。当字符串缩短时，记录在free中，为将来的字符串的可能的增长提供优化。
>优化4：二进制安全

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;C字符串不能存储空字符串。所以不能存储特殊的数据格式。因为它是利用空字符来表示字符串的结尾。而SDS利用len来判断是否是字符串的结尾。保证了二进制的安全。
>优化5：兼容部分C字符串的函数。