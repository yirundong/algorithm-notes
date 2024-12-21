## 完全二叉树(complete binary tree)

它是一种按顺序逐层填满的树.通常认为空树和单节点树也是完全二叉树.

## 数组表示

因为完全二叉树的性质,我们常常用它的层序遍历数组表示一棵完全二叉树.

如果i是树中节点在数组中的index,数组长度为N.则左孩子是`2i+1`,右孩子是`2i+2`,父亲是`(i-1)/2`,高度是logN.

##  堆

堆是一种满足特定规则的完全二叉树.如上所述,我们常用层序遍历数组表示堆.

`大根堆(max heap)`:对任意节点,父>=子;    `小根堆(min heap)`:对任意节点,父<=子

同时堆是一种特殊的队列

往堆中放值offer是放在堆尾然后它自己去调整堆结构,取值poll是从堆顶拿值.

大根堆的堆顶是堆中数据的最大值,小根堆的堆顶是堆中数据的最小值.

有一点要说明:堆只保证堆顶是最大/最小,堆中是无顺序的,它不会排序!

## Java中的堆

Java中的PriorityQueue(优先队列)就是堆.

通常使用时需要传入比较器来确定是大根堆还是小根堆,以及是按照什么属性定义的大小.

```Java
PriorityQueue<T> heap = new PriorityQueue<>(new MyComparator());
PriorityQueue<T> heap = new PriorityQueue<>((o1,o2) -> o1.xx - o2.xx);
```

例如这题的lambda,就表示是小根堆,并且是看的xx这个属性的大小进行比较的.

换句话说,实际上在Java中堆顶就是比较器定义的排列顺序的第一位!父的排列顺序>=子的排列顺序.

在Java中,如果不往PriorityQueue传入比较器,默认是小根堆.

## 修堆

当堆中某个值发生变化,堆结构可能被破坏,我们需要进行修堆,把这个变化的值放到正确的地方.

堆的结构只需要关注父子之间的关系,这个变化的值也只可能在它那条分支上移动.

这里以大根堆为例.

### heapInsert(堆插入)

把变化值往上放.这个名字是因为offer本身就是把堆尾新建元素往上放.你也可以叫它upHeapify.

一路跟父亲比,如果该值比父亲大就交换,直到该值没有父亲或者比父亲小为止.

时间复杂度是O(logn).

```Java
private void heapInsert(int[] arr, int index) {
    while (arr[index] > arr[(index - 1) / 2]) {
        swap(arr, index, (index - 1) / 2);
        index = (index - 1) / 2;
    }
}
```

### heapify(堆化)

把变化值往下放.也可以叫它downHeapify.

这个相对来说麻烦点,要保证父比所有儿子大,也就是要和较大的子比大小.一路向下,只要出现“大儿子”比该值大就交换,直到无子或者该值比“大儿子”大为止.

时间复杂度是O(logn).

```Java
private void heapify(int[] arr, int index, int heapSize) {
    int left = index * 2 + 1;
    while (left < heapSize) {
        int largest = left + 1 < heapSize && arr[left + 1] > arr[left] ? left + 1 : left;
        largest = arr[largest] > arr[index] ? largest : index;
        if (largest == index) {
            break;
        }
        swap(arr, largest, index);
        index = largest;
        left = index * 2 + 1;
    }
}
```

### 某值变化怎么修堆

把这俩函数都调用一遍,因为逻辑上如果某值变化破坏了堆结构,那么它要么往上动要么往下动,你只要都调用一遍就可以维持整体的堆结构了,它只会调用一个函数,相当于分支结构.两个函数都调用就可以实现通用的修堆函数了.

## 大根堆代码示例

```Java
    public class MyMaxHeap {
        private int[] heap;
        private final int limit;
        private int heapSize;

        public MyMaxHeap(int limit) {
            heap = new int[limit];
            this.limit = limit;
            heapSize = 0;
        }

        public boolean isEmpty() {
            return heapSize == 0;
        }

        public boolean isFull() {
            return heapSize == limit;
        }

        public void push(int value) {
            if (heapSize == limit) {
                throw new RuntimeException("heap is full");
            }
            heap[heapSize] = value;
            heapInsert(heap, heapSize++);
        }

        public int pop() {
            int ans = heap[0];
            swap(heap, 0, --heapSize);
            heapify(heap, 0, heapSize);
            return ans;
        }

        private void swap(int[] arr, int i, int j) {
            int tmp = arr[i];
            arr[i] = arr[j];
            arr[j] = tmp;
        }

        private void heapInsert(int[] arr, int index) {
            while (arr[index] > arr[(index - 1) / 2]) {
                swap(arr, index, (index - 1) / 2);
                index = (index - 1) / 2;
            }
        }

        private void heapify(int[] arr, int index, int heapSize) {
            int left = index * 2 + 1;
            while (left < heapSize) {
                int largest = left + 1 < heapSize && arr[left + 1] > arr[left] ? left + 1 : left;
                largest = arr[largest] > arr[index] ? largest : index;
                if (largest == index) {
                    break;
                }
                swap(arr, largest, index);
                index = largest;
                left = index * 2 + 1;
            }
        }
    }
```

## 自定义堆/加强堆

Java的堆不支持那种性价比不高的功能,但是有时候我们在用堆的时候会很需要某些功能或者需要提高复杂度,所以我们写一个自己的增强版本.

> 反向索引:我们只知道索引index,比如在数组中,可以通过它的位置查到这个位置的值是什么,当然不是所有数据结构都支持索引,最典型的支持索引的数据结构是数组,恰恰好堆在代码中就是数组.
>
> 什么是反向索引?就是知道值是什么,看他在数组中哪个位置.

Java中的堆是不支持反向索引的,所以当我们查找/更新/删除堆中某个值的时候,它会遍历去找,然后再进行操作.时间复杂度是O(n),如果它支持反向索引,那么我们找到这个值就是O(1)的时间复杂度.在一些频繁使用堆的查找/更新/删除的算法中非常高效.

### 相关知识点

动态数组ArrayList的get(查找),set(修改),后面add都是O(1)的时间复杂度.

我们使用哈希表实现反向索引功能,当传入相同key的记录时,key不变value覆盖,实际上只有最后加入哈希表的值有索引,其他相等的值都是都是这个索引,这显然不对,事实上包装类和基础类型(Java中支持自动拆装箱)都是按值传递的,那如果数据中有相等的岂不是无法被反向索引了?

山人自有妙计!我们只要使用的时候把这些数据加入数组,用数组下标表示不就行了吗.直接使用数组下标会自动装箱成包装类,数组下标的值肯定不会重复,可以使用.

以及我们在设计这些自定义数据结构时,尽可能的要模仿Java的做法,一方面有一个示范,另一方面是追求一种使用的统一.所以我们设计在new这个堆时要传入一个比较器,当然这也是必须的,堆得知道父子间怎么比较.别的方面也类似,比如说它不会自己检查堆是否为空,这是你调用者的责任.这都是异常处理的部分.

### 代码实现

```Java
public class HeapGreater<T> {

    private ArrayList<T> heap;
    private HashMap<T, Integer> indexMap;
    private int heapSize;
    private Comparator<? super T> comp;

    public HeapGreater(Comparator<? super T> c) {
        heap = new ArrayList<>();
        indexMap = new HashMap<>();
        heapSize = 0;
        comp = c;
    }

    public boolean isEmpty() {
        return heapSize == 0;
    }

    public int size() {
        return heapSize;
    }

    public boolean contains(T obj) {
        return indexMap.containsKey(obj);
    }

    public T peek() {
        return heap.get(0);
    }

    public void push(T obj) {
        heap.add(obj);
        indexMap.put(obj, heapSize);
        heapInsert(heapSize++);
    }

    public T pop() {
        T ans = heap.get(0);
        swap(0, heapSize - 1);
        indexMap.remove(ans);
        heap.remove(--heapSize);
        heapify(0);
        return ans;
    }

    public void remove(T obj) {
        T replace = heap.get(heapSize - 1);
        int index = indexMap.get(obj);
        indexMap.remove(obj);
        heap.remove(--heapSize);
        if (obj != replace) {
            heap.set(index, replace);
            indexMap.put(replace, index);
            resign(replace);
        }
    }

    public void resign(T obj) {
        heapInsert(indexMap.get(obj));
        heapify(indexMap.get(obj));
    }

    public List<T> getAllElements() {
        List<T> ans = new ArrayList<>();
        for (T c : heap) {
            ans.add(c);
        }
        return ans;
    }

    private void heapInsert(int index) {
        while (comp.compare(heap.get(index), heap.get((index - 1) / 2)) < 0) {
            swap(index, (index - 1) / 2);
            index = (index - 1) / 2;
        }
    }

    private void heapify(int index) {
        int left = index * 2 + 1;
        while (left < heapSize) {
            int best = left + 1 < heapSize && comp.compare(heap.get(left + 1), heap.get(left)) < 0 ? (left + 1) : left;
            best = comp.compare(heap.get(best), heap.get(index)) < 0 ? best : index;
            if (best == index) {
                break;
            }
            swap(best, index);
            index = best;
            left = index * 2 + 1;
        }
    }

    private void swap(int i, int j) {
        T o1 = heap.get(i);
        T o2 = heap.get(j);
        heap.set(i, o2);
        heap.set(j, o1);
        indexMap.put(o2, i);
        indexMap.put(o1, j);
    }
}
```

### 使用文档

这个加强堆有12个方法,注意这里的索引index都是指的堆的层序遍历数组的下标.这里的对象obj都是指的哈希表中的key(相等性由equals方法决定).

```Java
boolean isEmpty() //查看堆是否为空
int size()  //看堆的大小
boolean contains(T obj)  //看堆中是否包含某个元素
T peek()  //查看堆顶元素
void push(T obj)  //往堆中加入元素
T pop()  //弹出堆顶元素
void remove(T obj)  //在堆中删除元素
void resign(T obj)  //把堆中的obj调整到堆中的正确位置,这就是实际上的"改"堆中值的方法  
List<T> getAllElements()  //返回堆的层序遍历数组(以动态数组形式)
void heapInsert(int index)  //向上调整index位置的值
void heapify(int index)  //向下调整index位置的值   
void swap(int i, int j)  //交换堆中两位置的值,同时会更改反向索引表中的索引    
```

### 为什么要使用加强堆

Java中的PriorityQueue访问指定元素是遍历找的,时间复杂度是O(n).而我们的加强堆访问元素直接查表,时间复杂度是O(1).

删除某个元素具体操作是差不多的,时间复杂度是O(logn)的,所以删除操作时间复杂度从O(n)优化到O(logn).

如果改变某个自定义对象的比较属性,那么就需要使用resign方法了,只需要O(logn)的时间复杂度就可以完成修堆.但是Java提供的没有这个方法,只能删了重新加进去,我们知道它删除时间复杂度O(n),加是O(logn),总体时间复杂度是O(n),我们通过加强堆就把某个值改变之后破坏了堆结构重新修堆的时间复杂度从O(n)优化到O(logn).

查就更不用讲了,直接是O(1),Java提供的堆的查是通过遍历找的,时间复杂度是O(n).

综上所述,加强堆优化了删/改/查的时间复杂度,极大的优化了时间复杂度.

### 展示怎么使用自定义堆使用

为了防止你不会用,这里提供了使用范例.

```Java
public static void main(String[] args) {
    // 展示非基础类型的用法
    Student s1 = new Student(17, "A同学");
    Student s2 = new Student(10, "B同学");
    Student s3 = new Student(29, "C同学");
    Student s4 = new Student(33, "D同学");
    Student s5 = new Student(54, "E同学");
    Student s6 = new Student(93, "F同学");

    HeapGreater<Student> heap1 = new HeapGreater<>((a, b) -> a.age - b.age);

    heap1.push(s1);
    heap1.push(s2);
    heap1.push(s3);
    heap1.push(s4);
    heap1.push(s5);
    heap1.push(s6);

    s5.age = 4;
    heap1.resign(s5);

    while (!heap1.isEmpty()) {
        Student cur = heap1.pop();
        System.out.println("年龄 : " + cur.age + " , 名字 : " + cur.name);
    }

    System.out.println("======================");

    // 展示基础类型的用法
    int[] arr = {3, 3, 2, 5, 3};

    HeapGreater<Integer> heap2 = new HeapGreater<>((i, j) -> arr[i] - arr[j]);

    heap2.push(0);
    heap2.push(1);
    heap2.push(2);
    heap2.push(3);
    heap2.push(4);

    arr[1] = -9;
    heap2.resign(1);

    while (!heap2.isEmpty()) {
        int curIndex = heap2.pop();
        System.out.println("下标 : " + curIndex + " , 数字 :" + arr[curIndex]);
    }
}
```

