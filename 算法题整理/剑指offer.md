## [面试题33. 二叉搜索树的后序遍历序列](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/)

```java
class Solution {
    public boolean verifyPostorder(int[] postorder) {
        return resolve(postorder, 0, postorder.length - 1);
    }

    public boolean resolve(int[] postorder, int start, int end) {
        // 一个或者两个节点必须为二叉搜索树
        if (end - start <= 1) {
            return true;
        }

        // 寻找大于根节点的第一个节点，全为左子树则不存在
        // 此处必须为最后一个节点
        int greaterIndex = end;

        for (int i = start; i < end; i++) {
            if (postorder[i] > postorder[end]) {
                greaterIndex = i;
                break;
            }
        }

        // 判断全部左子节点是否小于根节点
        for (int i = start; i < greaterIndex; i++) {
            if (postorder[i] > postorder[end]) {
                return false;
            }
        }

        // 全部右子节点是否都小于根节点
        for (int i = greaterIndex; i < end; i++) {
            if (postorder[i] < postorder[end]) {
                return false;
            }
        }

        return resolve(postorder, start, greaterIndex - 1) &&
                resolve(postorder, greaterIndex, end - 1);
    }
}
```

## [面试题10- I. 斐波那契数列](https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/)

**非记忆性迭代**

```java
class Solution {
    public int fib(int n) {
        return resolve(n);
    }

    public static int resolve(int n) {
        if(n <= 1) {
            return n == 0? 0 : 1;
        }
        
        long[] arr = new long[n + 1];

        arr[0] = 0;
        arr[1] = 1;

        for (int i = 2; i < arr.length; i++) {
            // 此处取模是个坑，否则容易内存溢出
            arr[i] = arr[i - 1] % 1000000007 + arr[i - 2] % 1000000007;
        }

        return (int)(arr[arr.length - 1]  % 1000000007);
    }
}
```

**递归（会超时）**

```java
class Solution {
    public int fib(int n) {
        return resolve(n);
    }

    public int resolve(int n) {
        if(n <= 1) {
            return n == 0? 0 : 1;
        }

        return fib(n - 1) + fib(n - 2);
    }
}
```

**记忆性迭代**

```java
class Solution {
    public int fib(int n) {
        return resolve(n);
    }

    public int resolve(int n) {
        if (n <= 1) {
            return n == 0 ? 0 : 1;
        }

        int first = 0;
        int second = 1;

        int result = 0;

        for (int i = 2; i <= n; i++) {
            result = first % 1000000007 + second % 1000000007;

            if(i % 2 == 0) {
                first = result;
            } else {
                second = result;
            }
        }

        return (int) (result % 1000000007);
    }
}
```

