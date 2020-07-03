---
date: 2020-06-25 12:26:40
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
