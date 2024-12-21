> 栈和队列都是算法中常用的数据结构
>
> 一般用双链表或者数组实现,或者他俩之间也可以互相转化
>
> Java中常常把一些较为高级的数据结构定义为接口,然后用低级数据结构实现这些接口,实现类的复用
>
> 推荐多使用接口,有很多好处
>
> 在本章,我们自己实现栈和队列

## Java常用的在内存中创建方式

```java
Stack<Integer> stack = new Stack<>();
Queue<Integer> queue = new LinkedList<>();
Deque<Integer> deque = new LinkedList<>();
//api也会有区别,栈:push,pop;队列,堆:offer,poll
```

## Queue

> 单向队列是一种先进先出(FIFO)的数据结构

### 用双端队列实现

```Java
public static class MyQueue<T> {
        private DoubleEndsQueue<T> queue;

        public MyQueue() {
            queue = new DoubleEndsQueue<T>();
        }

        public void push(T value) {
            queue.addFromHead(value);
        }

        public T poll() {
            return queue.popFromBottom();
        }

        public boolean isEmpty() {
            return queue.isEmpty();
        }

    }
```

### 使用环形数组实现队列

```Java
public class RingArray {
    public static class MyQueue {
        private int[] arr;
        private int pushi;
        private int polli;
        private int size;
        private final int limit;

        public MyQueue(int limit) {
            arr = new int[limit];
            pushi = 0;
            polli = 0;
            size = 0;
            this.limit = limit;
        }

        public void push(int value) {
            if (size == limit) {
                throw new RuntimeException("队列满了，不能再加了");
            }
            size++;
            arr[pushi] = value;
            pushi = nextIndex(pushi);
        }

        public int pop() {
            if (size == 0) {
                throw new RuntimeException("队列空了，不能再拿了");
            }
            size--;
            int ans = arr[polli];
            polli = nextIndex(polli);
            return ans;
        }

        public boolean isEmpty() {
            return size == 0;
        }

        private int nextIndex(int i) {
            return i < limit - 1 ? i + 1 : 0;
        }
    }
}
```



## Deque

> 双端队列就像一个通道一样,头尾两端可以自由进出,和链表一样,我们通常使用更加灵活的双端队列

### 用双向链表实现

```Java
public static class Node<T> {
        public T value;
        public Node<T> last;
        public Node<T> next;

        public Node(T data) {
            value = data;
        }
    }

    public static class DoubleEndsQueue<T> {
        public Node<T> head;
        public Node<T> tail;

        public void addFromHead(T value) {
            Node<T> cur = new Node<T>(value);
            if (head == null) {
                head = cur;
                tail = cur;
            } else {
                cur.next = head;
                head.last = cur;
                head = cur;
            }
        }

        public void addFromBottom(T value) {
            Node<T> cur = new Node<T>(value);
            if (head == null) {
                head = cur;
                tail = cur;
            } else {
                cur.last = tail;
                tail.next = cur;
                tail = cur;
            }
        }

        public T popFromHead() {
            if (head == null) {
                return null;
            }
            Node<T> cur = head;
            if (head == tail) {
                head = null;
                tail = null;
            } else {
                head = head.next;
                cur.next = null;
                head.last = null;
            }
            return cur.value;
        }

        public T popFromBottom() {
            if (head == null) {
                return null;
            }
            Node<T> cur = tail;
            if (head == tail) {
                head = null;
                tail = null;
            } else {
                tail = tail.last;
                tail.next = null;
                cur.last = null;
            }
            return cur.value;
        }

        public boolean isEmpty() {
            return head == null;
        }
    }
```



## Stack

> 栈是一种后进先出(LIFO)的数据结构

### 用双端队列实现

```java
public static class MyStack<T> {
    private DoubleEndsQueue<T> queue;

    public MyStack() {
        queue = new DoubleEndsQueue<T>();
    }

    public void push(T value) {
        queue.addFromHead(value);
    }

    public T pop() {
        return queue.popFromHead();
    }

    public boolean isEmpty() {
        return queue.isEmpty();
    }

}
```

用数组实现栈很容易,这里不写,太简单了,只需要一个栈顶指针即可

### 最小栈

> 加强栈,实现O(1)的时间复杂度得到栈中最小值
>
> 可以实现加强的功能,这就是我们自定义数据结构的意义!
>
> 这种做法是典型的空间换时间,用了两个栈结构
>
> 当然,最大栈也是相同的做法

```Java
public class MinStack {
    public static class MyStack {
        private Stack<Integer> stackData;
        private Stack<Integer> stackMin;

        public MyStack() {
            stackData = new Stack<Integer>();
            stackMin = new Stack<Integer>();
        }

        public void push(int newNum) {
            if (stackMin.isEmpty() || newNum < getMin()) {
                stackMin.push(newNum);
            } else {
                stackMin.push(stackMin.peek());
            }
            stackData.push(newNum);
        }

        public int pop() {
            if (stackData.isEmpty()) {
                throw new RuntimeException("你的栈是空的");
            }
            stackMin.pop();
            return stackData.pop();
        }

        public int getMin() {
            if (stackMin.isEmpty()) {
                throw new RuntimeException("你的栈是空的");
            }
            return stackMin.peek();
        }
    }
}
```

## 队列和栈的相互转换

这样做没有什么实质性的意义,就是有时候会这么问

### 双栈成队列

> 注意点:push栈往pop栈中倒数据需要满足两个条件,一是push栈需要一次性全倒完,二是pop栈必须是空的才能倒
>
> 要求比较严苛,所以每一步操作之前都要倒一次以防万一

```Java
public class TwoStacksImplementQueue {
    public static class TwoStacksQueue {
        public Stack<Integer> stackPush;
        public Stack<Integer> stackPop;

        public TwoStacksQueue() {
            stackPush = new Stack<Integer>();
            stackPop = new Stack<Integer>();
        }
        
        private void pushToPop() {
            if (stackPop.empty()) {
                while (!stackPush.empty()) {
                    stackPop.push(stackPush.pop());
                }
            }
        }

        public void add(int pushInt) {
            stackPush.push(pushInt);
            pushToPop();
        }

        public int poll() {
            if (stackPop.empty() && stackPush.empty()) {
                throw new RuntimeException("Queue is empty!");
            }
            pushToPop();
            return stackPop.pop();
        }

        public int peek() {
            if (stackPop.empty() && stackPush.empty()) {
                throw new RuntimeException("Queue is empty!");
            }
            pushToPop();
            return stackPop.peek();
        }
    }
}
```

### 双队列成栈

> 两个队列来回倒腾,属于既费时间又费空间的做法

```Java
public class TwoQueueImplementStack {
    public static class TwoQueueStack<T> {
        public Queue<T> queue;
        public Queue<T> help;

        public TwoQueueStack() {
            queue = new LinkedList<>();
            help = new LinkedList<>();
        }

        public void push(T value) {
            queue.offer(value);
        }

        public T poll() {
            while (queue.size() > 1) {
                help.offer(queue.poll());
            }
            T ans = queue.poll();
            Queue<T> tmp = queue;
            queue = help;
            help = tmp;
            return ans;
        }

        public T peek() {
            while (queue.size() > 1) {
                help.offer(queue.poll());
            }
            T ans = queue.poll();
            help.offer(ans);
            Queue<T> tmp = queue;
            queue = help;
            help = tmp;
            return ans;
        }

        public boolean isEmpty() {
            return queue.isEmpty();
        }
    }
}
```

