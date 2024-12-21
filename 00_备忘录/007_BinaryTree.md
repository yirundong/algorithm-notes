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

二叉树也是一种单向的结构,就比单链表多了一个指针.

## 二叉树的遍历

我们想用一个数组表示二叉树,比较直观,只需要给出值的数组以及遍历方式就可以唯一的表示一棵二叉树了.用数组就可以表示出一棵二叉树可以在某些算法中大大简化复杂度.并且实现序列化也需要这种数组式的结构来存储.

通常我们说有三种遍历二叉树的方法,分别是先序遍历(pre-order),中序遍历(in-order),后序遍历(post-order).

但是二叉树还可以按照递归序进行遍历.递归序源于一种二叉树常见的递归关系,这种递归关系可以保证遍历到所有节点.当然所谓的“遍历”是一种抽象,就是会处理到所有节点作为输入的本层函数.要注意递归序是可以代表一棵二叉树的,每个不同的二叉树得到的都不一样的,它代表了一棵二叉树从头开始的完整节点访问顺序.

递归表达的是一种依赖关系,即某个输入的输出要依赖于与之相关的其他输入的输出.我们所说的递归关系,就是父作为输入的结果依赖两子作为输入的结果.只有当某个父节点的左,右孩子信息都收上来才能返回父节点信息.每个节点既是父又是子,所以就形成了一种递归关系.而且还有一种潜规则,就是我们默认会先收集左孩子的信息再收集右孩子的信息.

这种递归关系保证了所有节点都会被处理到,粗俗的讲,就是你要干我,就必须把我俩孩子都干一遍,那你从根节点开始必然可以干完所有的节点.当然不是说二叉树只能由这种递归关系,这只是一种二叉树节点之间常见的递归关系.

我们常常用“层”这种抽象概念来阐述递归关系,也就是上层会依赖下层,每层的操作逻辑是相同的.所谓“递归序”,就是在这种特殊的递归中,我们来到不同层的次序.用“输入节点”代表这里的“层”.显而易见的,每层都会来到三次,抽象的说就是每个节点都会遍历三次.

如果要打印递归序只需要在来到每一层的时候打印代表节点即可,三种遍历只是递归序的特殊版,先序遍历只在第一次来到某层时会打印代表节点,中序是第二次,后序是第三次.在最后打印出来的结果中,递归序每个节点都会有三个,三种遍历任意节点都“头左右”,”左头右”,”左右头”这样的关系(你可以用填格子的写法写).

还可以用画点法来得到一棵二叉树的三种遍历.先序在每个节点左边画点,中序在下面画点,后序在右边画点.从根节点开始,逆时针连接每个点就是遍历顺序.

最后,当然有人会问,那我不能先收集右孩子信息再收集左孩子信息吗,当然可以,只不过还是一个词“约定俗成”,再多三种遍历顺序本身的实际意义不大,真的需要也可以用现有的三种遍历改.想要实现“中右左”“右中左”“右左中”这样的遍历太简单了.

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

最后,二叉树还有层序遍历,即一层一层的遍历.

## 递归序的底层表示

当然,涉及到递归一定避不开系统栈,这里写出递归序使用递归关系的底层过程.

先把根节点压栈.然后放入左节点,它也是该父节点左子树的父,最终它子树的信息都会归到该节点上,这个节点得到它的值之后还会保留在父节点之上,它的值我们就称之为`左信息`.再放入右节点,它也是该父节点右子树的父,最终它子树的信息都会归到该节点上,这个节点最终结算完还会在之前结算的左节点上,它的值我们称之为`右信息`.最终左右节点出栈,左右信息共同组成父节点的`父信息`.

当然,这个递归本身也不是为了传递信息,也就是只有递归动作,每一层没有返回值,压根用不了下层信息,是一种无用递归,只代表了一种递归关系.我们使用这种递归只是需要这种关系带来的打印时机.

讲点题外话,我们使用递归要特别注意base-case和每层都用下一层信息干什么了.每层中的非递归操作都是一样的.通过经过这题,我们也可以看出,递归本身的递归序也是有用的,哪怕是无用递归.

## 关于二叉树遍历的常见结论

已知一棵二叉树的先序遍历和后序遍历,节点n的前序遍历的前面节点和它后序遍历中的后面节点的交集是节点n的所有祖先节点.

证明:前序遍历n的后面都是它孩子,后序遍历中n前面都是它孩子,所以交集是肯定没有孩子的,那为什么兄弟也可以排除呢,先序遍历中,n作为左的话,所有右兄弟都在其右边,后序遍历中,n作为右的话,所有左兄弟都在其左边,所以交集中就只有长辈了.

## 怎么确定一棵二叉树

- 递归序
- 中序+先序
- 中序+后序
- 如果遍历结果不忽略null,则除了中序遍历不行其他都可以.

## 非递归实现三种遍历打印

1. 先序遍历:

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

   这个做法比系统栈简单高效,比它更好.如果想要系统栈做法可以参考中序遍历.

2. 后序遍历:

   ```Java
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

   就是对先序的一种改写,这里要用到两个栈.

3. 中序遍历:
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

   和系统栈做法一样.

4. 后序遍历只用一个栈的做法

   `````Java
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
   `````

   指针c是栈顶指针,指针h记录着上个被打印的节点.这就是系统栈的做法.

5. 层序遍历

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

   

## 二叉树的序列化和反序列化

   将内存中的二叉树结构存入外存中,需要进行序列化,从外存中读入二叉树需要进行反序列化.

   考虑到实现的难度,通常采用打印空节点的做法.我们使用队列作为放置数组,基于效率方面的考量.

   当然,序列化本质上就是遍历,所以代码

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

   反序列化

- 先序

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

  

- 后序

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

  为啥反序列化不是通用的模板,主要是因为没有办法通过子节点找到父.

- 层序遍历

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
  ```

  