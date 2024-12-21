# 二叉树

## 二叉树节点

```Java
public static class Node {
    public int value;
    public Node left;
    public Node right;

    public Node(int v) {
       value = v;
    }
}
```

二叉树也是一种单向的结构,就比单链表多了一个指针.树和链表一样,都是特殊的“图”.

## 序列化打印二叉树

这有很多意义:存储二叉树到硬盘中,算法优化......

有五种遍历二叉树的方式可以序列化二叉树.序列中是节点的值.

### 递归序

深度优先遍历(dfs).使用栈实现.

在不打印空节点的情况下,只有递归序可以唯一的还原出一棵任意二叉树.

这里的递归指一种特殊的递归关系:要得到父节点信息需要先收集左孩子信息再收集右孩子信息.注意所谓节点信息指该节点作为输入时的递归函数返回值.这种递归关系可以保证遍历到所有树中节点.

递归序就是在这种递归关系中,从根节点出发,进入节点“本层”的次序.每个节点的递归层都会进入三次,分别是第一次进入,左孩子返回时进入,右孩子返回时进入.每次进入都打印本层节点,就会生成“递归序”.

```Java
public static void f(Node head) {
    if (head == null) {
       return;
    }
    // 先序在这里打印head
    f(head.left);
    // 中序在这里打印head
    f(head.right);
    // 后序在这里打印head
}
```

先/中/后序就是递归序的特殊情况,处理节点的先后顺序有规则,都可以由递归生成.

给出树的图,通常有两种方式得到先/中/后序序列.

1. 填格子法:即按照先/中/后序的特点往序列中填入节点值
2. 画点法:先/中/后序分别是在每个节点左/下/右方画点,然后从根节点逆时针连接,连接的顺序就是对应的序列.

#### 先序

先序序列中任意值满足“父左右”的关系.

非递归版本:

```Java
    public static void pre(Node head) {
        System.out.print("pre-order: ");
        if (head != null) {
            Stack<Node> stack = new Stack<>();
            stack.push(head);
            while (!stack.isEmpty()) {
                head = stack.pop();
                System.out.print(head.value + " ");
                if (head.right != null) {
                    stack.push(head.right);
                }
                if (head.left != null) {
                    stack.push(head.left);
                }
            }
        }
        System.out.println();
    }
```

#### 中序

中序序列中任意值满足“左父右”的关系.

非递归版本:

```Java
	public static void in(Node cur) {
		System.out.print("in-order: ");
		if (cur != null) {
			Stack<Node> stack = new Stack<>();
			while (!stack.isEmpty() || cur != null) {
				if (cur != null) {
					stack.push(cur);
					cur = cur.left;
				} else {
					cur = stack.pop();
					System.out.print(cur.value + " ");
					cur = cur.right;
				}
			}
		}
		System.out.println();
	}
```

#### 后序

后序序列中任意值满足“左右父”的关系.

非递归版本:

```Java
//改写先序版本,多用了一个栈来逆序
public static void post(Node head) {
        System.out.print("pos-order: ");
        if (head != null) {
            Stack<Node> s1 = new Stack<>();
            Stack<Node> s2 = new Stack<>();
            s1.push(head);
            while (!s1.isEmpty()) {
                head = s1.pop(); 
                s2.push(head);
                if (head.left != null) {
                    s1.push(head.left);
                }
                if (head.right != null) {
                    s1.push(head.right);
                }
            }
            while (!s2.isEmpty()) {
                System.out.print(s2.pop().value + " ");
            }
        }
        System.out.println();
    }
```

```Java
//只用申请一个栈
    public static void postOneStack(Node h) {
        System.out.print("pos-order: ");
        if (h != null) {
            Stack<Node> stack = new Stack<>();
            stack.push(h);
            Node c;
            while (!stack.isEmpty()) {
                c = stack.peek();
                if (c.left != null && h != c.left && h != c.right) {
                    stack.push(c.left);
                } else if (c.right != null && h != c.right) {
                    stack.push(c.right);
                } else {
                    System.out.print(stack.pop().value + " ");
                    h = c;
                }
            }
        }
        System.out.println();
    }
```

### 层序

广度优先遍历(bfs),使用队列实现.

按层遍历二叉树并打印就会生成层序.

```Java
    public static void level(Node head) {
        if (head == null) {
            return;
        }
        Queue<Node> queue = new LinkedList<>();
        queue.add(head);
        while (!queue.isEmpty()) {
            Node cur = queue.poll();
            System.out.println(cur.value);
            if (cur.left != null) {
                queue.add(cur.left);
            }
            if (cur.right != null) {
                queue.add(cur.right);
            }
        }
    }
```

## 常见结论

### 一.小问题

给定一课二叉树的先序和后序序列,求任一节点n的“长辈”.

答案是先序n之前的值和后序n之后的值的交集.

分析:节点之间只有三种关系---长辈,兄弟,孩子.先序遍历n的后面都是它孩子,后序遍历中n前面都是它孩子,所以那个交集中肯定没有孩子,先序遍历中,n作为左的话,所有右兄弟都在其右边,后序遍历中,n作为右的话,所有左兄弟都在其左边,所以交集中就只有长辈了.

### 二.怎么唯一序列化一棵任意二叉树

1. 如果忽略空节点
   - 递归序
   - 中序 + 先/后序
2. 序列化空节点
   - 除了中序都行

## 序列化二叉树

实现将一棵二叉树“编码”成一种线性结构.同时可以“解码”.

这里的线性结构采用队列,它存储的不是真实的节点,而是节点值转的字符串对象(空节点也会放入).

为什么要使用队列,因为队列可以实现O(1)的时间复杂度拿出序列中最前面的值.我们的算法是这么设计的.(当然可以使用别的线性结构,那样就得设计别的算法).

### 一.序列化

#### 1.先/中/后序

```Java
    public static Queue<String> serial(Node head) {
        Queue<String> ans = new LinkedList<>();
        f(head, ans);
        return ans;
    }
    
    public static void f(Node head, Queue<String> ans) {
        if (head == null) {
            ans.add(null);
        } else {
            //ans.add(String.valueOf(head.value))在这里是先序
            f(head.left, ans);
            //ans.add(String.valueOf(head.value))在这里是中序
            f(head.right, ans);
            //ans.add(String.valueOf(head.value))在这里是后序
        }
    }
```

递归法中把之前的打印换成存入.

#### 2.层序

```Java
    public static Queue<String> levelSerial(Node head) {
        Queue<String> ans = new LinkedList<>();
        if (head == null) {
            ans.add(null);
        } else {
            ans.add(String.valueOf(head.value));
            Queue<Node> queue = new LinkedList<>();
            queue.add(head);
            while (!queue.isEmpty()) {
                head = queue.poll();
                if (head.left != null) {
                    ans.add(String.valueOf(head.left.value));
                    queue.add(head.left);
                } else {
                    ans.add(null);
                }
                if (head.right != null) {
                    ans.add(String.valueOf(head.right.value));
                    queue.add(head.right);
                } else {
                    ans.add(null);
                }
            }
        }
        return ans;
    }
```

要用到两个队列,除了之前层序遍历用到的辅助队列,还需要有一个真正的存储队列.只有非空节点才会放入辅助队列,而存储队列没有限制.

### 二.反序列化

#### 1.先序

```Java
    public static Node buildByPreQueue(Queue<String> prelist) {
        if (prelist == null || prelist.isEmpty()) {
            return null;
        }
        return preb(prelist);
    }

    public static Node preb(Queue<String> prelist) {
        String value = prelist.poll();
        if (value == null) {
            return null;
        }
        Node head = new Node(Integer.parseInt(value));
        head.left = preb(prelist);
        head.right = preb(prelist);
        return head;
    }
```

使用递归,好理解.

#### 2.后序

```Java
    public static Node buildByPosQueue(Queue<String> poslist) {
        if (poslist == null || poslist.isEmpty()) {
            return null;
        }
        Stack<String> stack = new Stack<>();
        while (!poslist.isEmpty()) {
            stack.push(poslist.poll());
        }
        return posb(stack);
    }

    public static Node posb(Stack<String> posstack) {
        String value = posstack.pop();
        if (value == null) {
            return null;
        }
        Node head = new Node(Integer.parseInt(value));
        head.right = posb(posstack);
        head.left = posb(posstack);
        return head;
    }
```

改写先序反序列化,要利用一个栈逆序.为啥反序列化不是通用的模板,主要是因为没有办法通过子节点找到父节点.

#### 3.层序

和层序序列化逻辑一样.

```Java
    public static Node buildByLevelQueue(Queue<String> levelList) {
        if (levelList == null || levelList.isEmpty()) {
            return null;
        }
        Node head = generateNode(levelList.poll());
        Queue<Node> queue = new LinkedList<>();
        if (head != null) {
            queue.add(head);
        }
        Node node;
        while (!queue.isEmpty()) {
            node = queue.poll();
            node.left = generateNode(levelList.poll());
            node.right = generateNode(levelList.poll());
            if (node.left != null) {
                queue.add(node.left);
            }
            if (node.right != null) {
                queue.add(node.right);
            }
        }
        return head;
    }

    //由字符串生成节点
    public static Node generateNode(String val) {
        if (val == null) {
            return null;
        }
        return new Node(Integer.parseInt(val));
    }
```

