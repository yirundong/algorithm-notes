## 小和问题

### 题意
给定一个int数组,所谓"小和",就是数组每一位前面所有比它小的数的和,把每一位的小和加起来就是数组的小和.

现在给定一个int数组,让你给出这个数组的“小和”.

### 思路

正常遍历的话时间复杂度是O(N2),而利用归并排序时间复杂度是O(NlogN)--基本就是归并排序,只加了一行代码,并且我甚至把顺序都顺便排了一下.

核心思想就是从小到大的merge过程中,只要找到左对比组中的每个数在其对照组中有几个比它大.举个例子,如果右组中有3个比左组中的5大的数,就在答案中加上3 * 5即可.这就是为归并排序量身定做的题目.唯一不同的就是当相同的时候要先拷贝右组的,这样才能正确结算左组中的数.

### 代码

```java
public class SmallSum {
    public static int smallSum(int[] arr) {
        if (arr == null || arr.length < 2) {
            return 0;
        }
        return process(arr, 0, arr.length - 1);
    }

    public static int process(int[] arr, int l, int r) {
        if (l == r) {
            return 0;
        }
        int mid = l + ((r - l) >> 1);
        return process(arr, l, mid) + process(arr, mid + 1, r) + merge(arr, l, mid, r);
    }

    public static int merge(int[] arr, int L, int m, int r) {
        int[] help = new int[r - L + 1];
        int i = 0;
        int p1 = L;
        int p2 = m + 1;
        int res = 0;
        while (p1 <= m && p2 <= r) {
            res += arr[p1] < arr[p2] ? (r - p2 + 1) * arr[p1] : 0;
            help[i++] = arr[p1] < arr[p2] ? arr[p1++] : arr[p2++];
        }
        while (p1 <= m) {
            help[i++] = arr[p1++];
        }
        while (p2 <= r) {
            help[i++] = arr[p2++];
        }
        for (i = 0; i < help.length; i++) {
            arr[L + i] = help[i];
        }
        return res;
    }
}
```

### 总结

这题如果不懂可以用一个普通位置上的数试试,看看是否不重不漏.这题就是逆向思维,看一个数是否可以成为数组小和的一部分而不是统计所有数的小和,这题在逻辑上是非常严密的,在形式上是非常简单的,可以称之为“美”

## 逆序对问题

### 题意

“逆序对”指的是在一个数组中,所有不改变顺序的降序对,也就是一对数,左边的index比右边的小并且值比右边大,问给定一个数组,有多少“逆序对”.

### 思路

刚才那题找的是右边比自己大的,这题就是找右边比自己小的

典型的做法是倒着merge,对比的两子数组从最后开始比,大的放入help,然后相等时放右边组的.

这题也顺便把顺序排了,复杂度也和归并排序一致

### 代码

```Java
public class ReversePair {
    public static int reversePairNumber(int[] arr) {
        if (arr == null || arr.length < 2) {
            return 0;
        }
        return process(arr, 0, arr.length - 1);
    }

    public static int process(int[] arr, int l, int r) {
        if (l == r) {
            return 0;
        }
        int mid = l + ((r - l) >> 1);
        return process(arr, l, mid) + process(arr, mid + 1, r) + merge(arr, l, mid, r);
    }

    public static int merge(int[] arr, int L, int m, int r) {
        int[] help = new int[r - L + 1];
        int i = help.length - 1;
        int p1 = m;
        int p2 = r;
        int res = 0;
        while (p1 >= L && p2 > m) {
            res += arr[p1] > arr[p2] ? (p2 - m) : 0;
            help[i--] = arr[p1] > arr[p2] ? arr[p1--] : arr[p2--];
        }
        while (p1 >= L) {
            help[i--] = arr[p1--];
        }
        while (p2 > m) {
            help[i--] = arr[p2--];
        }
        for (i = 0; i < help.length; i++) {
            arr[L + i] = help[i];
        }
        return res;
    }
}
```

### 总结

这题就是对第一题的举一反三,就相当于熟悉归并排序.

## [有规则的逆序对问题](https://leetcode.com/problems/reverse-pairs/)

### 题意

对第二题的举一反三,找出数组中满足特殊规则的“逆序对”,这题中的逆序对是右边的值乘2都小于左边的值,找出给定数组中有多少个这样的逆序对.

### 思路

虽然说形似第二题,但是实际上还是有很大不同的,最明显的就是merge又是从头开始比了,这题会使用类似窗口那样*不回退的双指针*,一个在左组,一个在右组.

具体思路就是左指针固定,右指针向右移动,移动到满足不了规则,右指针走过的步数就是左指针指向值所拥有的逆序对个数.然后再移动左指针走完左子数组,这个算法的精髓在于左右子数组都是有序的并且易知这俩指针都是不回退的!

### 代码

```Java
public class BiggerThanRightTwice {
    public static int reversePairs(int[] arr) {
        if (arr == null || arr.length < 2) {
            return 0;
        }
        return process(arr, 0, arr.length - 1);
    }

    public static int process(int[] arr, int l, int r) {
        if (l == r) {
            return 0;
        }
        int mid = l + ((r - l) >> 1);
        return process(arr, l, mid) + process(arr, mid + 1, r) + merge(arr, l, mid, r);
    }

    public static int merge(int[] arr, int L, int m, int r) {
        int ans = 0;
        int windowR = m + 1;
        for (int i = L; i <= m; i++) {
            while (windowR <= r && (long) arr[i] > (long) arr[windowR] * 2) {
                windowR++;
            }
            ans += windowR - m - 1;
        }
        int[] help = new int[r - L + 1];
        int i = 0;
        int p1 = L;
        int p2 = m + 1;
        while (p1 <= m && p2 <= r) {
            help[i++] = arr[p1] <= arr[p2] ? arr[p1++] : arr[p2++];
        }
        while (p1 <= m) {
            help[i++] = arr[p1++];
        }
        while (p2 <= r) {
            help[i++] = arr[p2++];
        }
        for (i = 0; i < help.length; i++) {
            arr[L + i] = help[i];
        }
        return ans;
    }
}
```

### 总结

这题才是真正会考的题目,考的永远是变体.这题merge和找结果是分离的,先找逆序对个数再merge,但是时间复杂度肯定不变,是并行的嘛,而且这题用到了双指针技巧,这种技巧以后在窗口那里也会讲到.

并且这题要特别注意边界,比如右指针的设定和乘2比较放溢出的设置,总之要注意细节.

而且你能想象吗,这是leetcode的hard难度!是不是循序渐进就特别简单.

## [子数组和问题](https://leetcode.com/problems/count-of-range-sum/)

### 题意

过不过瘾?再来一题hard难度的!

这题给定一个数组,给定一个范围,让你求其中有多少子数组的和在给定范围内.

### 思路

暴力求法就是求每一个子数组的和然后再对比,子数组的个数是等差数列,根据求和公式,有O(n2)规模个,每个都求和,线性求和时间复杂度是O(n)的,因为每段都是a * n个数,a是小数.这样时间复杂度就是O(n3).你可以认为有一个算法专门从事数组求和,这个算法的复杂度当然是O(n)的,现在就是有O(n2)规模个数组来进行求和而已.

这个对于初学者确实很让人费解,如果这个说服不了你,可以这么想,长度为1的子数组有n个,每个求和需要1个时间;长度为2的子数组有n-1个,每个求和需要2个时间;长度为3的子数组有n-2个,每个求和需要3个时间......长度为k的子数组有n-k+1个,每个求和需要k个时间.那么整体求和你可以用数学的方式具体算出来,事实上我算出的结果就是O(n3)

我们可以用前缀和数组省去求和的O(n),这样就是O(n2).更进一步,我们可以再利用归并排序做到O(nlogn)复杂度.

我们通常反过来想:数组中每个数作为子数组的尾,可以找到所有子数组.给定范围进行转化,例如我们把5位置的数作为尾,找到一系列子数组,根据前缀和数组我们知道5的前缀和为100,范围是[-20,30],那么实际上我们只需要在前缀和数组中[0,4]范围中找到值为[70,120]的索引即可,我们所要的子数组就是[index+1,5].经过这种转化,我们只要遍历完数组,就能找到所有的子数组.

我们怎么运用归并排序呢?综上所述,可以认为我们就是固定右组,找对应左组中所有在特定范围内的值有多少个!这个形式不就是第三题中的含有规则的逆序对倒过来吗.事实上,因为左右两组是有序的,我们把第三题中的左指针做成一个真正的窗口结构,第三题是类窗口结构,固定住右指针,左窗口包含的数据数量就是右指针指向值拥有的个数.

### 代码

```Java
public class CountOfRangeSum {
    public static int countRangeSum(int[] nums, int lower, int upper) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        long[] sum = new long[nums.length];
        sum[0] = nums[0];
        for (int i = 1; i < nums.length; i++) {
            sum[i] = sum[i - 1] + nums[i];
        }
        return process(sum, 0, sum.length - 1, lower, upper);
    }

    public static int process(long[] sum, int L, int R, int lower, int upper) {
        if (L == R) {
            return sum[L] >= lower && sum[L] <= upper ? 1 : 0;
        }
        int M = L + ((R - L) >> 1);
        return process(sum, L, M, lower, upper) + process(sum, M + 1, R, lower, upper) + merge(sum, L, M, R, lower, upper);
    }

    public static int merge(long[] arr, int L, int M, int R, int lower, int upper) {
        int ans = 0;
        int windowL = L;
        int windowR = L;
        for (int i = M + 1; i <= R; i++) {
            long min = arr[i] - upper;
            long max = arr[i] - lower;
            while (windowR <= M && arr[windowR] <= max) {
                windowR++;
            }
            while (windowL <= M && arr[windowL] < min) {
                windowL++;
            }
            ans += windowR - windowL;
        }
        long[] help = new long[R - L + 1];
        int i = 0;
        int p1 = L;
        int p2 = M + 1;
        while (p1 <= M && p2 <= R) {
            help[i++] = arr[p1] <= arr[p2] ? arr[p1++] : arr[p2++];
        }
        while (p1 <= M) {
            help[i++] = arr[p1++];
        }
        while (p2 <= R) {
            help[i++] = arr[p2++];
        }
        for (i = 0; i < help.length; i++) {
            arr[L + i] = help[i];
        }
        return ans;
    }
}
```

### 总结

很多情况下,我们可能不会得到明显的提示,表明这个题目是关于归并排序的,我们实际上就是构造了一种满足归并排序的新结构,在这题中就是前缀和数组,原数组不满足类似“特殊规则的逆序对”这种形式时,我们可以想可不可以硬来,原结构不行就构造一个结构.这个题就是一个非常好的例子,可以使用归并排序的题目往往是“找逆序对个数”这种形式的,培养感觉.

还有这题虽然思路很长,但很多都是跟思路没关系的,实际上并不难理解.涉及到一种数据结构`窗口`,就是一种特殊的双指针,其中的左右指针是不回退的,后面还会单独讲.
