---
date: 2020-06-25 12:26:40
layout: post
title: LeetCode Daily Code
subtitle: Daily Code
description: Daily programming questions from leetcode.
image: assets/img/post_img/LeetCode1.png
optimized_image: /assets/img/uploads/profile.png
category: LeetCode
tags:
  - programming
  - java
author: thiagorossener
paginate: true
---

## 2020.06.25
### Problem
- 给定一个非空字符串 s 和一个包含非空单词列表的字典 wordDict，判定s 是否可以被空格拆分为一个或多个在字典中出现的单词。

> 说明：
- 拆分时可以重复使用字典中的单词。
- 你可以假设字典中没有重复的单词。
- 链接：<a href="https://leetcode-cn.com/problems/word-break">https://leetcode-cn.com/problems/word-break</a>

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









