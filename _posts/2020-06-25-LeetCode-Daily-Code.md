---
date: 2020-06-21 12:26:40
layout: post
title: LeetCode Daily Code
subtitle: Daily Code
description: Daily programming questions from leetcode.
image: /assets/img/post_img/LeetCode1.png
optimized_image: /assets/img/post_img/LeetCode2.png
category: LeetCode
tags:
  - programming
  - java
author: Lam
paginate: true
---

* [2020\.06\.25](#20200625)
  * [Problem](#problem)
  * [Solution](#solution)
  * [Get](#get)
* [2020\.06\.26](#20200626)
  * [Problem](#problem-1)
  * [Solution](#solution-1)
  * [Get](#get-1)
 
## 2020.06.25
### Problem
- 给定一个非空字符串 s 和一个包含非空单词列表的字典 wordDict，判定s 是否可以被空格拆分为一个或多个在字典中出现的单词。

> 说明：

- 拆分时可以重复使用字典中的单词。
- 你可以假设字典中没有重复的单词。
- 链接：<a href="https://leetcode-cn.com/problems/word-break">https://leetcode-cn.com/problems/word-break</a>
- 示例：

```java
输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。

输入: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
输出: false
```

### Solution

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        int n = s.length();
        // hold[i] 表示 s 中以 i - 1 结尾的字符串是否可被wordDict拆分
        boolean[] hold = new boolean[n + 1];
        hold[0] = true;
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j < i; j++) {
                if (hold[j] && wordDict.contains(s.substring(j, i))) {
                    hold[i] = true;
                    break;
                }
            }
        }
        return hold[n];
    }
}
```
### Get

- **contains**方法 返回布尔值

```java
/* String s;
   List<String> list = new ArrayList<String>(); */
   s.contains("hello"); //检验s是否包含字串"hello"
   list.contains("hello"); //检验list（集合）是否包含元素"hello"
```

## 2020.06.26
### Problem
- 编写代码，移除未排序链表中的重复节点。保留最开始出现的节点。

> 说明：

- 链接：<a href="https://leetcode-cn.com/problems/remove-duplicate-node-lcci">https://leetcode-cn.com/problems/remove-duplicate-node-lcci</a>
- 示例：

```java
输入：[1, 2, 3, 3, 2, 1]
输出：[1, 2, 3]

输入：[1, 1, 1, 1, 2]
输出：[1, 2]
```

### Solution
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode removeDuplicateNodes(ListNode head) {
        List<Integer> list = new ArrayList<>();

        ListNode dummyHead = new ListNode(-1);
        dummyHead.next = head;

        ListNode hold = dummyHead;
        if(head==null){
            return head;
        }
        while(hold.next != null){
            if(!list.contains(hold.next.val)){
                list.add(hold.next.val);
                hold = hold.next;
            }
            else{
                hold.next = hold.next.next;
            }
        }
        return dummyHead.next;
    }
}
```

### Get
- Java中的**集合**  

<table>
  <thead>
    <tr>
      <th>集合名</th>
      <th>线程安全</th>
      <th>线程同步</th>
      <th>元素重复</th>
      <th>允许null</th>
      <th>存储有序</th>
      <th>添加方法</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>ArrayList</td>
      <td>×</td>
      <td>×</td>
      <td>√</td>
      <td>√</td>
      <td>√</td>
      <td>add()</td>
    </tr>
    <tr>
      <td>LinkedList</td>
      <td>×</td>
      <td>×</td>
      <td>√</td>
      <td>√</td>
      <td>√</td>
      <td>add()</td>
    </tr>
    <tr>
      <td>HashSet</td>
      <td>×</td>
      <td>×</td>
      <td>×</td>
      <td>×</td>
      <td>×</td>
      <td>add()</td>
    </tr>
    <tr>
      <td>HashMap</td>
      <td>×</td>
      <td>×</td>
      <td>×</td>
      <td>×</td>
      <td>×</td>
      <td>put()</td>
    </tr>
    <tr>
      <td>Hashtable</td>
      <td>√</td>
      <td>√</td>
      <td>×</td>
      <td>×</td>
      <td>×</td>
      <td>add()</td>
    </tr>
  </tbody>
</table>

## 2020.06.27
### Problem
- 给你一个未排序的整数数组，请你找出其中没有出现的最小的正整数。

> 说明：

- 链接：<a href="https://leetcode-cn.com/problems/first-missing-positive">https://leetcode-cn.com/problems/first-missing-positive</a>
- 示例：

```java
输入: [1,2,0]
输出: 3

输入: [7,8,9,11,12]
输出: 1
```

### Solution

```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        if(nums.length == 0){
            return 1;
        }
        var list = new ArrayList<Integer>();
        int ans = 0;
        for(int i = 0;i < nums.length;i++){
            list.add(nums[i]);
        }
        //list.size()+2 可应对[1],[0,1,2...]等特殊数组
        for(int i = 1;i<list.size() + 2;i++){
            if(!list.contains(i)){
                ans = i;
                break;
            } 
        }
        return ans;
    }
}
```

## 2020.06.29
### Problem
- 在未排序的数组中找到第 k 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

> 说明：

- 链接：<a href="https://leetcode-cn.com/problems/kth-largest-element-in-an-array/">https://leetcode-cn.com/problems/kth-largest-element-in-an-array</a>
- 示例：

```java
输入: [3,2,1,5,6,4] 和 k = 2
输出: 5

输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
输出: 4
```

### Solution

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        //调用库函数
        if(nums.length == 0) return 0;
        Arrays.sort(nums);
        return nums[nums.length-k];
        
        /* 手写某一种排序算法
        var len = nums.length;
        for (var i = 0; i < len - 1; i++) {
            for (var j = 0; j < len - 1 - i; j++) {
                if (nums[j] > nums[j+1]) {        // 相邻元素两两对比
                    var temp = nums[j+1];        // 元素交换
                    nums[j+1] = nums[j];
                    nums[j] = temp;
                }
            }
        }
        return nums[len-k];
        */
    }
}
```

### Get
- 排序算法

<table>
  <thead>
    <tr>
      <th>排序方法</th>
      <th>时间复杂度（平均）</th>
      <th>时间复杂度（最坏）</th>
      <th>时间复杂度（最好）</th>
      <th>空间复杂度</th>
      <th>稳定性</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>插入排序</td>
      <td>O(n<sup>2</sup>)</td>
      <td>O(n<sup>2</sup>)</td>
      <td>O(n)</td>
      <td>O(1)</td>
      <td>稳定</td>
    </tr>
    <tr>
      <td>希尔排序</td>
      <td>O(n<sup>1.3</sup>)</td>
      <td>O(n<sup>2</sup>)</td>
      <td>O(n)</td>
      <td>O(1)</td>
      <td>不稳定</td>
    </tr>
    <tr>
      <td>选择排序</td>
      <td>O(n<sup>2</sup>)</td>
      <td>O(n<sup>2</sup>)</td>
      <td>O(n<sup>2</sup>)</td>
      <td>O(1)</td>
      <td>不稳定</td>
    </tr>
    <tr>
      <td>堆排序</td>
      <td>O(nlog<sub>2</sub>n)</td>
      <td>O(nlog<sub>2</sub>n)</td>
      <td>O(nlog<sub>2</sub>n)</td>
      <td>O(1)</td>
      <td>不稳定</td>
    </tr>
    <tr>
      <td>冒泡排序</td>
      <td>O(n<sup>2</sup>)</td>
      <td>O(n<sup>2</sup>)</td>
      <td>O(n)</td>
      <td>O(1)</td>
      <td>稳定</td>
    </tr>
    <tr>
      <td>快速排序</td>
      <td>O(nlog<sub>2</sub>n)</td>
      <td>O(n<sup>2</sup>)</td>
      <td>O(nlog<sub>2</sub>n)</td>
      <td>O(nlog<sub>2</sub>n)</td>
      <td>不稳定</td>
    </tr>
        <tr>
      <td>归并排序</td>
      <td>O(nlog<sub>2</sub>n)</td>
      <td>O(nlog<sub>2</sub>n)</td>
      <td>O(nlog<sub>2</sub>n)</td>
      <td>O(n)</td>
      <td>稳定</td>
    </tr>
    <tr>
      <td>计数排序</td>
      <td>O(n+k)</td>
      <td>O(n+k)</td>
      <td>O(n+k)</td>
      <td>O(n+k)</td>
      <td>稳定</td>
    </tr>
    <tr>
      <td>桶排序</td>
      <td>O(n+k)</td>
      <td>O(n<sup>2</sup>)</td>
      <td>O(n)</td>
      <td>O(n+k)</td>
      <td>稳定</td>
    </tr>
    <tr>
      <td>基数排序</td>
      <td>O(n*k)</td>
      <td>O(n*k)</td>
      <td>O(n*k)</td>
      <td>O(n+k)</td>
      <td>稳定</td>
    </tr>
  </tbody>
</table>

## 2020.07.03
### Problem
- 将一个按照升序排列的有序数组，转换为一棵高度平衡二叉搜索树。本题中，一个高度平衡二叉树是指一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1。

> 说明：

- 链接：<a href="https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/">https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree</a>
- 示例：

```java
给定有序数组: [-10,-3,0,5,9],
一个可能的答案是：[0,-3,9,-10,null,5]，它可以表示下面这个高度平衡二叉搜索树：
      0
     / \
   -3   9
   /   /
 -10  5
```

### Solution

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
    public TreeNode sortedArrayToBST(int[] nums) {
        // 左右等分建立左右子树，中间节点作为子树根节点，递归该过程
        return nums == null ? null : buildTree(nums, 0, nums.length - 1);
    }

    private TreeNode buildTree(int[] nums, int left, int right) {
        if (left > right) {
            return null;
        }
        int m = left + (right - left) / 2;
        TreeNode root = new TreeNode(nums[m]);
        root.left = buildTree(nums, left, m - 1);
        root.right = buildTree(nums, m + 1, right);
        return root;
    }
}
```

## 2020.07.04
### Problem
- 给定一个只包含 '(' 和 ')' 的字符串，找出最长的包含有效括号的子串的长度。

> 说明：

- 链接：<a href="https://leetcode-cn.com/problems/longest-valid-parentheses">https://leetcode-cn.com/problems/longest-valid-parentheses</a>
- 示例：

```java
输入: "(()"
输出: 2
解释: 最长有效括号子串为 "()"

输入: ")()())"
输出: 4
解释: 最长有效括号子串为 "()()"
```

### Solution

```java
class Solution {
public int longestValidParentheses(String s) {
	char[] chars = s.toCharArray();
	return Math.max(calc(chars, 0, 1, chars.length, '('), calc(chars, chars.length -1, -1, -1, ')'));
}
private static int calc(char[] chars , int i ,  int flag,int end, char cTem){
	int max = 0, sum = 0, currLen = 0,validLen = 0;
	for (;i != end; i += flag) {
		sum += (chars[i] == cTem ? 1 : -1);
        currLen ++;
		if(sum < 0){
			max = max > validLen ? max : validLen;
			sum = 0;
			currLen = 0;
            validLen = 0;
		}else if(sum == 0){
            validLen = currLen;
        }
	}
	return max > validLen ? max : validLen;
}
}
```

## 2020.07.05
### Problem
- 给定一个字符串 (s) 和一个字符模式 (p) ，实现一个支持 '?' 和 '*' 的通配符匹配。
- '?' 可以匹配任何单个字符。'*' 可以匹配任意字符串（包括空字符串）。

> 说明：

- s 可能为空，且只包含从 a-z 的小写字母。
- p 可能为空，且只包含从 a-z 的小写字母，以及字符 ? 和 *。
- 链接：<a href="https://leetcode-cn.com/problems/wildcard-matching">https://leetcode-cn.com/problems/wildcard-matching</a>
- 示例：

```java
输入:
s = "aa"
p = "a"
输出: false
解释: "a" 无法匹配 "aa" 整个字符串。

输入:
s = "aa"
p = "*"
输出: true
解释: '*' 可以匹配任意字符串。

输入:
s = "cb"
p = "?a"
输出: false
解释: '?' 可以匹配 'c', 但第二个 'a' 无法匹配 'b'。
```

### Solution

```java
class Solution {
    public static boolean isMatch(String s, String p) {
        int sn = s.length();
        int pn = p.length();
        boolean[][] dp = new boolean[sn + 1][pn + 1];
        char[] sArray = s.toCharArray();
        char[] pArray = p.toCharArray();
        dp[0][0] = true;
        for (int i = 1; i <= pn; ++i) {
            if (pArray[i-1] == '*') {
                dp[0][i] = true;
            } else {
                break;
            }
        }
        for (int i = 1; i <= sn; ++i) {
            for (int j = 1; j <= pn; ++j) {
                if (pArray[j-1] == '*') {
                    dp[i][j] = dp[i][j - 1] || dp[i - 1][j];
                } else if (pArray[j-1] == '?' || sArray[i-1] == pArray[j-1]) {
                    dp[i][j] = dp[i - 1][j - 1];
                }
            }
        }
        return dp[sn][pn];
    }
}
```

### Get
- 动态规划
- 动态规划问题，大致可以通过以下四部进行解决。
> 1. 划分状态，即划分子问题。
> 2. 状态表示，即如何让计算机理解子问题。
> 3. 状态转移，即父问题是如何由子问题推导出来的。
> 4. 确定边界，确定初始状态是什么？最小的子问题？最终状态又是什么？

## 2020.07.06
### Problem
- 一个机器人位于一个m x n  网格的左上角。机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下。
现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？

> 说明：

- 链接：<a href="https://leetcode-cn.com/problems/unique-paths-ii">https://leetcode-cn.com/problems/unique-paths-ii</a>
- m 和 n 的值均不超过 100。
- 网格中的障碍物和空位置分别用 1 和 0 来表示。
- 示例：

```java
输入:
[
 [0,0,0],
 [0,1,0],
 [0,0,0]
]
输出: 2
解释:
3x3 网格的正中间有一个障碍物。
从左上角到右下角一共有 2 条不同的路径：
1. 向右 -> 向右 -> 向下 -> 向下
2. 向下 -> 向下 -> 向右 -> 向右
```

### Solution

```java
class Solution {
       public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if (obstacleGrid[0][0] == 1) return 0;
        int row = obstacleGrid.length;
        int col = obstacleGrid[0].length;

        int[][] dp = new int[row][col];
        dp[0][0] = 1;
        
        // base case
        for (int i = 1; i < row; i ++) {
            if (dp[i - 1][0] == 1 && obstacleGrid[i][0] != 1) dp[i][0] = 1;
            else break;
        }
        for (int i = 1; i < col; i ++) {
            if (dp[0][i - 1] == 1 && obstacleGrid[0][i] != 1) dp[0][i] = 1;
            else break;
        }

        for (int i = 1; i < row; i ++) {
            for (int j = 1; j < col; j ++) {
                if (obstacleGrid[i][j] == 1) continue;

                dp[i][j] = (obstacleGrid[i - 1][j] == 1 ? 0 : dp[i - 1][j])
                        + (obstacleGrid[i][j - 1] == 1 ? 0 : dp[i][j - 1]);
            }
        }
        return dp[row - 1][col - 1];
    }
}
```

## 2020.07.07
### Problem
- 给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。



> 说明：

- 链接：<a href="https://leetcode-cn.com/problems/path-sum">https://leetcode-cn.com/problems/path-sum</a>
- 叶子节点是指没有子节点的节点。
- 示例：

```java
给定如下二叉树，以及目标和 sum = 22
              5
             / \
            4   8
           /   / \
          11  13  4
         /  \      \
        7    2      1
返回 true, 因为存在目标和为 22 的根节点到叶子节点的路径 5->4->11->2。
```

### Solution

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
    public boolean hasPathSum(TreeNode root, int sum) {
        if(root == null) {
            return false;
        }
        if(root.left == null && root.right == null) {
            return root.val == sum;
        }
        return hasPathSum(root.left, sum - root.val) 
            || hasPathSum(root.right, sum - root.val);
    }
}
```

## 2020.07.08
### Problem
- 你正在使用一堆木板建造跳水板。有两种类型的木板，其中长度较短的木板长度为shorter，长度较长的木板长度为longer。你必须正好使用k块木板。编写一个方法，生成跳水板所有可能的长度。


> 说明：

- 链接：<a href="https://leetcode-cn.com/problems/diving-board-lcci">https://leetcode-cn.com/problems/diving-board-lcci</a>
- 返回的长度需要从小到大排列。
- 示例：

```java
输入：
shorter = 1
longer = 2
k = 3
输出： {3,4,5,6}
```

### Solution

```java
class Solution {
    public int[] divingBoard(int shorter, int longer, int k) {
        if(k == 0) {
            return new int[]{}; // 表示返回集合 {} 空集
    }
        if (longer == shorter) {
            return new int[]{k * longer}; // 表示返回集合 {value} value为k*longer的值
    }
        int n = longer - shorter;
        int[] ans = new int[k + 1];
        for(int i = 0;i <= k;i++) {
            ans[i] = shorter * k + i * n;
        }
        return ans;
    }
}
```

### Get
- return new int[]{}; // 表示返回集合 {} ，空集。
- return new int[]{k * longer}; // 表示返回集合 {value} ，value为k * longer的值。
