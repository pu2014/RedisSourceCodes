title：redis 源码阅读 skiplist & sortset
## 跳跃表实现
```
//基于redis.h/zskiplistNode 和 redis.h/zskiplist 两个结构定义，其中zskiplistNode 结构用于跳跃表节点，zskiplist 结构则用于保存跳跃表节点的相关信息。

typedef struct zskiplistNode{
    //层
    struct zskiplistLevel{
        //前进指针
        struct zskiplistNode * forward;
        //跨度
        unsigned int span;
    }level[];

    //后退指针
    struct zskiplistNode *backward;


    //分值
    double score;

    //成员对象
    robj *obj
} zskiplistNode;


typedef struct zskiplist{
    structz skiplistNode *header, *tail;
    //表中节点的数量
    unsigned long length;
    //表中层数最大的节点层数
    int level;
} zskiplist;
```

## 跳表的实现：java版本
```
public class SkipList{
    private static final int MAX_LEVEl = 20;
    private int levelCount = 1;
    private Node head = new Node(null);
    private Random random = new Random();

    public class Node{
        private Integer data;
        private Node[] next;
        private int maxLevel;
            public Node(Integer data){
                this.data = data;
                next = new Node[MAX_LEVEL];
                maxLevel = 0;
            }

            public String toString(){
                String builder Str = new StringBuilder();
                str.append("{data:});
                str.append(data);
                str.append("; levels: ");
                str.append(maxLevel);
                str.append(" }");
                return str.toString();
            }
    }

    public Node find(int value){
        Node p = head;
        for(int i = levelCount - 1; i >= 0; --i){
            while(p.next[i] != null && p.next[i].data < value){
                p.next[i];
            }
        }
        if(p.next[0] != null && p.next[0].data == value){
            return p.next[0];
        }else{
            return null;
        }
    }

    public void inset(int value){
        int level = randomLevel();
        Node newNode = new Node(value);
        newNode.maxValue = level;
        Node updata[] = new Node[level];
        for(int i = 0; i < level; i++){
            updata[i] = head;
        }
        Node p = head;
        for(int i = level - 1; i >= 0; --i){
            while(p.next != null && p.next[i].data < value){
                p = p.next[i];
            }
            updata[i] = p;
        }
        for(int i = 0 ; i < level; i++){
            newNode.next[i] = updata[i].next[i];
            updata[i].next[i] = newNode;
        }
        if(levelCount < level){
            levelCount = level;
        }
    }
    public boolean delete(int value){
        Node[] updata = new Node[levelCount];
        Node p = head;
        for(int i = levelCount - 1; i>= 0; --i){
            while(p.next[i] != null && p.next[i].data < value){
                p = p.next[i];
            }
            updata[i] = p;
        }
        if(p.next[0] != null && p.next[0].data == value){
            for(int i = levelCount - 1; i >= 0; --i){
                if(updata[i].next[i] !=  null &&
                        updata[i].next[i].data == value){
                    updata[i].next[i] = updata[i].next[i].next[i];
                }
            }
            return true;

        }else{
            return false;
        }
   }

   public void display(){
        Node p = head;
        while(p.next[0] != null){
            System.out.println(p.next[0]);
            p = p.next[0];
        }
   }

    private int randomLevel() {
        int level = 1;
        for(int i = 1; i< MAX_LEVEL; ++i){
            if((random.nextInt() & 1) == 1){
                level++;
            }
        }
        return level;
    }
}


```