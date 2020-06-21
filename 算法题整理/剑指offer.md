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

## [面试题55 - II. 平衡二叉树](https://leetcode-cn.com/problems/ping-heng-er-cha-shu-lcof/)

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
    public boolean isBalanced(TreeNode root) {
        if(root == null) return true;
        
        return Math.abs(depth(root.left) - depth(root.right)) <= 1 && isBalanced(root.left) && isBalanced(root.right);
    }

    public int depth(TreeNode root) {
        if(root == null) return 0;
        
        return Math.max(depth(root.left), depth(root.right)) + 1;
    }
}
```

## [面试题55 - I. 二叉树的深度](https://leetcode-cn.com/problems/er-cha-shu-de-shen-du-lcof/)

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
    public int maxDepth(TreeNode root) {
        if(root == null) return 0;
        return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
    }
}
```

## [面试题26. 树的子结构](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/)

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
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        return (A != null && B != null) && (select(A, B) || isSubStructure(A.left, B) || isSubStructure(A.right, B));
    }

    public boolean select(TreeNode A, TreeNode B) {
        if(B == null) return true;
        if(A == null || A.val != B.val) return false;

        return select(A.left, B.left) && select(A.right, B.right);
    }
}
```

## [面试题07. 重建二叉树](https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/)

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
   public  TreeNode buildTree(int[] preorder, int[] inorder) {
        Map<Integer, Integer> map = new HashMap<>(inorder.length * 2);
        for (int i = 0; i < inorder.length; i++) {
            map.put(inorder[i], i);
        }
        
        return curBuildTree(preorder, 0, preorder.length - 1, inorder, 0, inorder.length - 1, map);
    }

    public  TreeNode curBuildTree(int[] preorder, int fBegin, int fEnd, int[] inorder, int mBegin, int mEnd, Map<Integer, Integer> map) {
        if(fBegin > fEnd || mBegin > mEnd) return null;
        
        TreeNode treeNode = new TreeNode(preorder[fBegin]);

        if(fBegin == fEnd) return treeNode;

        else {
            int index = map.get(preorder[fBegin]);
            treeNode.left = curBuildTree(preorder, fBegin + 1, fBegin + index - mBegin, inorder, mBegin, index - 1, map);
            treeNode.right = curBuildTree(preorder, fBegin + index - mBegin + 1, fEnd, inorder, index + 1, mEnd, map);
        }

        return treeNode;
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

# 动态规划

## [面试题47. 礼物的最大价值](https://leetcode-cn.com/problems/li-wu-de-zui-da-jie-zhi-lcof/)

**代码简洁版**

```java
class Solution {
    public int maxValue(int[][] grid) {
        int rowLength = grid.length;
        int colLength = grid[0].length;

        int[][] arr = new int[rowLength + 1][colLength + 1];

        for (int i = 1; i < arr.length; i++) {
            for (int j = 1; j < arr[i].length; j++) {
                arr[i][j] += Integer.max(arr[i - 1][j], arr[i][j - 1]) + grid[i - 1][j - 1];
            }
        }

        return arr[rowLength][colLength];
    }
}
```

**普通**

```java
class Solution {
    public int maxValue(int[][] grid) {
        int rowLength = grid.length;
        int colLength = grid[0].length;

        for (int i = 0; i < rowLength; i++) {
            for (int j = 0; j < colLength; j++) {
                if( i == 0 && j == 0 ){
                    // 第一行第一列
                    continue;
                }

                if( i == 0) {
                    // 第一行
                    grid[i][j] += grid[i][j - 1];
                }else if(j == 0) {
                    // 第一列
                    grid[i][j] += grid[i - 1][j];
                } else {
                    // 其它位置
                    grid[i][j] = Integer.max(grid[i - 1][j], grid[i][j - 1]) + grid[i][j];
                }
            }
        }
        return grid[rowLength][colLength];
    }
}
```

**优化**

```java
class Solution {
    public int maxValue(int[][] grid) {
        int rowLength = grid.length;
        int colLength = grid[0].length;

        for (int i = 1; i < colLength; i++) {
            grid[0][i] += grid[0][i - 1];
        }

        for (int i = 1; i < rowLength; i++) {
            grid[i][0] += grid[i - 1][0];
        }


        for (int i = 1; i < rowLength; i++) {
            for (int j = 1; j < colLength; j++) {
                grid[i][j] = Integer.max(grid[i - 1][j], grid[i][j - 1]) + grid[i][j];

            }
        }
        return grid[rowLength - 1][colLength - 1];
    }
}
```

## [面试题49. 丑数](https://leetcode-cn.com/problems/chou-shu-lcof/)

```java
class Solution {
    public int nthUglyNumber(int n) {
        if(n == 1) return 1;

        int a = 0 ,b = 0, c = 0;
        int[] res = new int[n];
        res[0] = 1;

        for (int i = 1; i < n; i++) {
            int n1 = res[a] * 2;
            int n2 = res[b] * 3;
            int n3 = res[c] * 5;

            res[i] = Math.min(Math.min(n1, n2), n3);

            if(n1 == res[i]) a++;
            if(n2 == res[i]) b++;
            if(n3 == res[i]) c++;
        }

        return res[n -1];
    }
}
```

## [面试题50. 第一个只出现一次的字符](https://leetcode-cn.com/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/)

```java
class Solution {
    public char firstUniqChar(String s) {
        Map<Character, Boolean> map = new HashMap<>(s.length() * 2);

        for (int i = 0; i < s.length(); i++) {
            map.put(s.charAt(i), !map.containsKey(s.charAt(i)));
        }

        for (int i = 0; i < s.length(); i++) {
            if(map.get(s.charAt(i))) return s.charAt(i);
        }

        return ' ';
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

## [面试题61. 扑克牌中的顺子](https://leetcode-cn.com/problems/bu-ke-pai-zhong-de-shun-zi-lcof/)

```java
class Solution {
    public boolean isStraight(int[] nums) {
        Set<Integer> set = new HashSet<>();

        int max = -Integer.MAX_VALUE;
        int min = Integer.MAX_VALUE;

        for (int i = 0; i < nums.length; i++) {
            if(nums[i] == 0) {
                continue;
            }
            if(set.contains(nums[i])) return false;
            set.add(nums[i]);
            if(nums[i] > max) max = nums[i];
            if(nums[i] < min && nums[i] != 0) min = nums[i];
        }

        return max - min < 5;
    }
}
```

## [面试题29. 顺时针打印矩阵](https://leetcode-cn.com/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/)

```java
class Solution {
    public int[] spiralOrder(int[][] matrix) {
        if(matrix == null || matrix.length == 0 || matrix[0].length == 0) return new int[0];

        int rows = matrix.length;
        int cols = matrix[0].length;
        int total = rows * cols;

        int[] order = new int[total];
        boolean[][] visited = new boolean[rows][cols];

        int row = 0;
        int col = 0;

        int[][] dec = new int[][]{{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
        int index = 0;

        for (int i = 0; i < total; i++) {
            order[i] = matrix[row][col];
            visited[row][col] = true;

            int nextRow = row + dec[index][0];
            int nextCol = col + dec[index][1];

            if(nextRow < 0 || nextRow >= rows || nextCol < 0 || nextCol >= cols || visited[nextRow][nextCol])
                index = (index + 1) % 4;


            col += dec[index][1];
            row += dec[index][0];
        }

        return order;
    }
}
```

## [面试题03. 数组中重复的数字](https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)

```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        Map<Integer, Boolean> map = new HashMap<>(nums.length * 2);

        for (int i = 0; i < nums.length; i++) {
            map.put(nums[i], map.containsKey(nums[i]));
        }

        for (int i = 0; i < nums.length; i++) {
            if(map.get(nums[i])) return nums[i];
        }

        return 0;
    }
}
```

## [面试题04. 二维数组中的查找](https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)

```java
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        if(matrix == null || matrix.length == 0 || matrix[0].length == 0) return false;

        int rows = 0;
        int cols = matrix[0].length - 1;

        while(cols >=0 && rows <= matrix.length - 1) {
            if(target > matrix[rows][cols]) {
                rows += 1;
            } else if(target <matrix[rows][cols]) {
                cols -= 1;
            } else {
                return true;
            }
        }

        return false;
    }
}
```



