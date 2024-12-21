## 并查集的定义

HashSet用来作为表很方便,增删改查时间复杂度都是O(1),但是合并两个Set为一个新的Set的时间复杂度是O(n)的.

但有些算法需要大量使用这样的操作,于是就发明了“并查集”这样的数据结构.

这种数据结构对数据集合的**合并(Union)**是通过指针进行的,时间复杂度为O(1).

同时对元素隶属集合的**查询(Find)**时间复杂度也是O(1)的,这是在查询次数至少达到O(n)的情况下均摊下来得到的(类似于哈希表增加记录),具体不需要证明(相当复杂).

这里还有[测试连接](https://www.nowcoder.com/questionTerminal/e7ed657974934a30b2010046536a5372),可以直接线上测试.

```java
public class UnionFind {
    private int[] parent;
    private int[] size;
    private int[] help;
    private int sets;

    public UnionFind(int N) {
        parent = new int[N];
        size = new int[N];
        help = new int[N];
        sets = N;
        for (int i = 0; i < N; i++) {
            parent[i] = i;
            size[i] = 1;
        }
    }

    public void union(int i, int j) {
        int f1 = find(i);
        int f2 = find(j);
        if (f1 != f2) {
            if (size[f1] >= size[f2]) {
                size[f1] += size[f2];
                parent[f2] = f1;
            } else {
                size[f2] += size[f1];
                parent[f1] = f2;
            }
            sets--;
        }
    }

    public int find(int i) {
        int hi = 0;
        while (i != parent[i]) {
            help[hi++] = i;
            i = parent[i];
        }
        for (hi--; hi >= 0; hi--) {
            parent[help[hi]] = i;
        }
        return i;
    }

    public boolean isSameSet(int x, int y) {
        return find(x) == find(y);
    }

    public int getSetsNum() {
        return sets;
    }
}
```

### 一些说明:

- 每个集合都有一个代表节点,在father数组中记录.
- 这里的最大节点数是一百万.
- 每个集合的大小被存储在size数组中,这里注意非代表节点作为索引没有意义.
- 用help数组模拟栈结构,进行路径压缩.
- 所谓路径压缩,就是指第一次查的时候优化查询结构,让查询链上的所有节点直接指向代表节点,这样下一次查询可以一步查询到,这也是最关键的优化,经过证明当数据量规模达到O(n),均摊下来每次查询时间复杂度O(1).
- 我们这个结构的节点是输入数组的索引.
- 并查集也可以用哈希表实现,但是时空占用较高,写起来也不够简洁,所以并不常用.感兴趣可以自行查阅.

### 总结

并查集是一个很简单,很高效的数据结构,专门用来解决数据连通的问题,通常会和矩阵(二维数组)一起考察.可以理解为HashSet合并代价为0的版本.注意查询次数一定至少要O(n)规模.