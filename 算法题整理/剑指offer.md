# 二叉树类

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

## [面试题32 - I. 从上到下打印二叉树](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-lcof/)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int[] levelOrder(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        List<TreeNode> queue = new LinkedList<>();

        if (root == null) {
            return new int[0];
        } else {
            queue.add(root);
        }

        while (!queue.isEmpty()) {
            TreeNode remove = queue.remove(0);
            res.add(remove.val);

            if (remove.left != null) queue.add(remove.left);
            if (remove.right != null) queue.add(remove.right);
        }

        int[] arr = new int[res.size()];

        for (int i = 0; i < res.size(); i++) {
            arr[i] = res.get(i);
        }

        return arr;
    }
}
```

## [面试题32 - II. 从上到下打印二叉树 II](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        List<TreeNode> queue = new LinkedList<>();

        if (root == null) {
            return new ArrayList<List<Integer>>();
        } else {
            queue.add(root);
        }

        while (!queue.isEmpty()) {
            List<Integer> tmp = new ArrayList<>();
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode remove = queue.remove(0);
                tmp.add(remove.val);

                if (remove.left != null) queue.add(remove.left);
                if (remove.right != null) queue.add(remove.right);
            }
            res.add(tmp);
        }

        return res;
    }
}
```

## [面试题32 - III. 从上到下打印二叉树 III](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        List<TreeNode> queue = new LinkedList<>();
        // 初始位奇数层
        boolean flag = true;

        if (root == null) {
            return new ArrayList<List<Integer>>();
        } else {
            queue.add(root);
        }

        while (!queue.isEmpty()) {
            List<Integer> tmp = new ArrayList<>();
            int size = queue.size();
            if(flag) {
                for (int i = 0; i < size; i++) {
                    TreeNode remove = queue.remove(0);
                    tmp.add(remove.val);

                    if (remove.left != null) queue.add(remove.left);
                    if (remove.right != null) queue.add(remove.right);
                }
            } else {
                for (int i = queue.size() - 1; i >= 0; i--) {
                    TreeNode remove = queue.remove(queue.size() - 1);
                    tmp.add(remove.val);

                    if (remove.right != null) queue.add(0 ,remove.right);
                    if (remove.left != null) queue.add(0, remove.left);
                }

            }

            flag = !flag;
            res.add(tmp);
        }

        return res;
    }
}
```

# 字符串类

## [面试题48. 最长不含重复字符的子字符串](https://leetcode-cn.com/problems/zui-chang-bu-han-zhong-fu-zi-fu-de-zi-zi-fu-chuan-lcof/)

**滑动窗口**

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if(s == null || "".equals(s)) {
            return 0;
        }

        int maxLength = 0;
        List<String> tmp = new ArrayList<>();

        for (int i = 0; i < s.length(); i++) {
            String substring = s.substring(i, i + 1);
            if(tmp.contains(substring)) {
                maxLength = maxLength > tmp.size() ? maxLength : tmp.size();
                while(tmp.contains(substring)) {
                    tmp.remove(0);
                }
            }
            tmp.add(substring);
        }

        if(tmp.size() != 0) {
            maxLength = maxLength > tmp.size() ? maxLength : tmp.size();
        }

        return maxLength;
    }
}
```

**滑动窗口优化**

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int len = s.length();
        if (len <= 1) {
            return len;
        }

        int maxLength = 0;
        int l = 0, r = 0;
        Map<String, Integer> map = new HashMap<>();

        for (int i = 0; i < s.length(); i++) {
            String subString = s.substring(i, i + 1);

            if (map.containsKey(subString)) {
                int hashIndex = map.get(subString);
                if (hashIndex >= l) {
                    maxLength = Integer.max(maxLength, r - l);
                    l = hashIndex + 1;
                }
            }
            map.put(subString, r++);
        }

        return Integer.max(maxLength, r - l);
    }
}
```

# 其它

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
## [面试题59 - I. 滑动窗口的最大值](https://leetcode-cn.com/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/)

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int len = nums.length;

        if (len == 0 && k == 0) {
            return new int[]{};
        }


        List<Integer> ans = new ArrayList<>();

        for (int i = k - 1, j = 0; i < len; i++, j++) {
            int count = -Integer.MAX_VALUE;
            for (int l = 0; l < k; l++) {
                if(nums[j + l] > count) {
                    count = nums[j + l];
                }
            }

            ans.add(count);
        }

        int[] ints = new int[ans.size()];
        for (int i = 0; i < ans.size(); i++) {
            ints[i] = ans.get(i);
        }

        return ints;
    }
}
```

## [面试题43. 1～n整数中1出现的次数](https://leetcode-cn.com/problems/1nzheng-shu-zhong-1chu-xian-de-ci-shu-lcof/)

**说明**

f(n))函数的意思是1～n这n个整数的十进制表示中1出现的次数，将n拆分为两部分，最高一位的数字high和其他位的数字last，分别判断情况后将结果相加，看例子更加简单。

例子如n=1234，high=1, pow=1000, last=234

可以将数字范围分成两部分1~999和1000~1234

1~999这个范围1的个数是f(pow-1)
1000~1234这个范围1的个数需要分为两部分：
千分位是1的个数：千分位为1的个数刚好就是234+1(last+1)，注意，这儿只看千分位，不看其他位
其他位是1的个数：即是234中出现1的个数，为f(last)
所以全部加起来是f(pow-1) + last + 1 + f(last);

例子如3234，high=3, pow=1000, last=234

可以将数字范围分成两部分1~999，1000~1999，2000~2999和3000~3234

1~999这个范围1的个数是f(pow-1)
1000~1999这个范围1的个数需要分为两部分：
千分位是1的个数：千分位为1的个数刚好就是pow，注意，这儿只看千分位，不看其他位
其他位是1的个数：即是999中出现1的个数，为f(pow-1)
2000~2999这个范围1的个数是f(pow-1)
3000~3234这个范围1的个数是f(last)
所以全部加起来是pow + high*f(pow-1) + f(last);

```java
class Solution {
    public int countDigitOne(int n) {
        return f(n);
    }

    public int f(int n) {
        if(n <= 0) {
            return 0;
        }

        String s = String.valueOf(n);

        int high = s.charAt(0) - '0';
        int pow = (int) Math.pow(10, s.length() - 1);
        int low =  n - pow * high;

        if(high == 1) return (low + 1) + f(low) + f(pow - 1);
        else return pow + high * f(pow - 1) + f(low);
    }
}
```

### 暴力解法（会超时）

```java
class Solution {
    public int countDigitOne(int n) {
        int total = 0;

        for (int i = 1; i <= n; i++) {
            String s = String.valueOf(i);

            for (int j = 0; j < s.length(); j++) {
                String charTemp = s.substring(j, j + 1);

                if("1".equals(charTemp)) total++;
            }
        }

        return total;
    }
}
```

## [面试题40. 最小的k个数](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if(arr.length == 0) {
            return new int[0];
        }

        Arrays.sort(arr);

        int[] ans = new int[k];
        for (int i = 0; i < k; i++) {
            ans[i] = arr[i];
        }

        return ans;
    }
}
```

