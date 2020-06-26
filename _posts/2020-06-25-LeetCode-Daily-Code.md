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

