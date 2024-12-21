# 前缀树/检索树

## 名字由来

我常常说一个数据结构的名字往往可以说明它是干什么的.

trie(读音try)源于retrieval(检索),trie tree也叫(prefix tree),通常叫做前缀树.

它通常用来处理字符串问题,顾名思义,它能够很快的检索出有相同前缀的字符串个数.

## 定义

这个树的节点本身不会代表字符,但每个节点有两个值(p:字符串经过次数(pass),e:是否是某个字符串的结尾(end)).用每两个节点之间的边代表一个字符.

每个字符串放进来的时候都会从根节点依次往下填入每个字符.

## 为什么要使用这个结构

那有人说,我可以用HashMap放(字符串,其出现次数),增加和查询的时间复杂度可是O(1),你这个还得一个字符一个字符加,增加的时间复杂度是O(m)的,m是字符串长度,那不是更差了吗?

事实上,往哈希表中放入的时间复杂度和key的类型有关系,因为放入时要用哈希函数计算key的位置的,当key是字符串时,他并不是一个固定的时间,他往往也是一个字符一个字符的计算的,也就是说这个时间和字符串的长度是线性相关的.也就是放入的时间复杂度和用前缀树的一致.但哈希表处理某个固定长度的key,比如int或者指针时,我们通常认为计算哈希值是根据它的二进制算的,无论数或者对象怎么变化,二进制数长度都是一样的,所以我们认为时间复杂度是O(1)的.

好处就是前缀树可以支持查询某一前缀的字符串有多少个,即直接查询前缀最后一个字符的变量,复杂度是O(1)的.而哈希表是不支持这个功能的.

## [代码实现](https://leetcode.cn/problems/implement-trie-ii-prefix-tree/)

结构设计有在线测试,我们可以直接去测.我们设计的前缀树是阉割版的,只能接受纯小写字母字符串.每个节点node中还有一个大小为26的node数组,举个例子,如果a节点的node数组中node[0]有一个b节点,代表a连上了b,连线代表小写字母‘a’.

按照我们的设计,删除某个字符串,如果某个节点的p值为0,我们一定要让这个这个节点(以及它指向的节点)从树上断开,这样我们查的时候才不会查到已经删掉的字符.实际上如果是cpp,还需要析构这个节点及其指向的节点,不然会发生内存泄漏,对于Java来说不需要,因为Java有知名的“GC”,会自动回收垃圾内存---这里指没有指针指向的内存.

```Java
class Trie {
    class Node {
        public int pass;
        public int end;
        public Node[] nexts;

        public Node() {
            pass = 0;
            end = 0;
            nexts = new Node[26];
        }
    }

    private Node root;

    public Trie() {
        root = new Node();
    }

    public void insert(String word) {
        if (word == null) {
            return;
        }
        char[] str = word.toCharArray();
        Node node = root;
        node.pass++;
        int path = 0;
        for (char c : str) {
            path = c - 'a';
            if (node.nexts[path] == null) {
                node.nexts[path] = new Node();
            }
            node = node.nexts[path];
            node.pass++;
        }
        node.end++;
    }

    public void erase(String word) {
        if (countWordsEqualTo(word) != 0) {
            char[] chs = word.toCharArray();
            Node node = root;
            node.pass--;
            int path = 0;
            for (char ch : chs) {
                path = ch - 'a';
                if (--node.nexts[path].pass == 0) {
                    node.nexts[path] = null;
                    return;
                }
                node = node.nexts[path];
            }
            node.end--;
        }
    }

    public int countWordsEqualTo(String word) {
        if (word == null) {
            return 0;
        }
        char[] chs = word.toCharArray();
        Node node = root;
        int index = 0;
        for (char ch : chs) {
            index = ch - 'a';
            if (node.nexts[index] == null) {
                return 0;
            }
            node = node.nexts[index];
        }
        return node.end;
    }

    public int countWordsStartingWith(String pre) {
        if (pre == null) {
            return 0;
        }
        char[] chs = pre.toCharArray();
        Node node = root;
        int index = 0;
        for (char ch : chs) {
            index = ch - 'a';
            if (node.nexts[index] == null) {
                return 0;
            }
            node = node.nexts[index];
        }
        return node.pass;
    }
}
```

## API使用文档

```Java
void insert(String word);  //加入一个小写字符串到前缀树中
void erase(String word);  //从前缀树中删除一个小写字符串
int countWordsEqualTo(String word);  //前缀树中查询给定的小写字符串出现次数
int countWordsStartingWith(String pre);  //前缀树的核心功能,查询给定前缀的字符串有多少个
```

## 总结

前缀树/检索树是一个很简单的数据结构,他被设计出来就是为了解决检索有相同前缀的字符串的数量的.如果有这个需求就可以考虑前缀树.
