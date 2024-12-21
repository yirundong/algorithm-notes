> 链表无小事!
>
> 在Java中List一半分为两种,ArrayList也叫动态数组,LinkedList也叫链表
>
> 具体的使用场景,使用方法如果感兴趣自行查阅,实际上也是相当符合逻辑的,不需要专门记
>
> 在这里说一下所谓的“指向-->”实际上就是一种通过某个节点可以找到另一个节点的抽象表达,并不是真的有这个操作符.在Java中通常表示为一个节点中含有另一个节点或者节点数组(当然所谓节点实际上是指向节点的指针).

### Java中常见的创建方式

```Java
List<Integer> list = new ArrayList<>();
List<Integer> list = new LinkedList<>();
```

### ArrayList

这个比较少见到,他就是数组加强版,可以动态增大的数组,如果发现容量不够了会找一个double容量的地方转移数据

### LinkedList

#### 单链表

单链表只有一个指后指针next,不灵活用的比较少

```Java
public static class Node {
    public int value;
    public Node next;

    public Node(int data) {
        value = data;
    }
}
```

#### 双链表

Java中内置的LinkedList就是双链表,包含前驱指针prev和后置指针next,用的比较多,我们之后默认链表指的就是双链表

```Java
public static class DoubleNode {
    public int value;
    public DoubleNode prev;
    public DoubleNode next;

    public DoubleNode(int data) {
        value = data;
    }
}
```

### 反转链表

反转链表用到额外两个结点指针变量,last和post

```Java
    public static Node reverseLinkedList(Node head) {
        Node last = null;
        Node post = null;
        while (head != null) {
            post = head.next;
            head.next = last;
            last = head;
            head = post;
        }
        return last;
    }

    public static DoubleNode reverseDoubleList(DoubleNode head) {
        DoubleNode last = null;
        DoubleNode post = null;
        while (head != null) {
            post = head.next;
            head.next = last;
            head.prev = post;
            last = head;
            head = post;
        }
        return last;
    }
```

### 删除链表中所有指定value的节点

```java
public static Node removeValue(Node head, int num) {
    while (head != null) {
        if (head.value != num) {
            break;
        }
        head = head.next;
    }
    Node pre = head;
    Node cur = head;
    while (cur != null) {
        if (cur.value == num) {
            pre.next = cur.next;
        } else {
            pre = cur;
        }
        cur = cur.next;
    }
    return head;
}
//这是单链表
```