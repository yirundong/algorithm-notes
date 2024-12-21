## Java中怎么使用

### 申请结构

```Java
HashMap<Integer, String> test = new HashMap<>();
HashSet<String> set = new HashSet<>();
TreeMap<Integer, String> treeMap = new TreeMap<>(比较器);
```

### 一些使用api

1. #### **HashMap使用api**

   - 增和改:put(key,value)
   - 查:containsKey(key)看是否存在,get(key)查对应的值
   - 删:remove(key)

2. #### **TreeMap使用api**

   - 基本上和HashMap一致

   - 但是因为其有序,会有很多别的方法

   - 表中key最小值:firstKey()

   - 表中key最大值:lastKey()

   - <= 4的最大值:floorKey(4)

   - \>= 4的最小值:ceilingKey(4)

3. #### Set使用api

   - set就是模拟的数学中的集合,一般不允许重复值出现,一旦重复会覆盖
   - 使用api一般和Map类似

### 一些特性

- Map的key和Set中的元素一般都是不允许重复的,Java中采用的是覆盖策略
- HashMap中如果出现不同的key经过hash函数之后的结果一样就会出现hash碰撞
- 此时这些value会连成一个链表,到一定长度之后会变成红黑树,以维持查找的高效
- 实际上,HashMap的增删改查效率都是O(1),TreeMap的增删改查效率都是O(logn)
- TreeMap叫做有序表,增删改查效率都是O(logn),在Java中使用红黑树实现有序表
- 我们后面还会使用avl树,sb树,跳表实现我们的有序表
- 有序表还需要比较器来进行非系统类型的比较,这个可以用lambda
- 实际上HashMap和TreeMap以及对应的set都是很聪明的,已经对系统类型进行了优化,只要是系统自带的类型,只要值相同它们就会认为就是相同的,这和我们的自然直观思维是一致的,这个和类型内部重写了hashcode方法有关系

### 未完待续...

这章有很多知识点在以后会持续讲到:

比如:什么是有序表?还有什么结构可以实现有序表?什么是红黑树?什么是Hash?它们的复杂度是怎么算的?我们怎么使用它们?它们有什么用?等等很多都得到后面才能知道,这章我们抛砖引玉,做一个引子

