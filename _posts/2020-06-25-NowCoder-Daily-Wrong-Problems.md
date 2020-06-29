---
date: 2020-06-25 12:27:40
layout: post
title: NowCoder Daily Problems
subtitle: Daily Problems
description: Daily Problems from NowCoder.
image: /assets/img/post_img/NowCoder1.png
optimized_image: /assets/img/post_img/NowCoder2.png
category: NowCoder
tags:
  - problems
  - java
author: Lam
paginate: true
---

* [2020\.6\.26](#2020626)
  * [Problem 1](#problem-1)
  * [Analysis](#analysis)

## 2020.06.26

### Problem 1
![image](/assets/img/nowcoder_img/20200626_1.png)

### Analysis

<table>
  <thead>
    <tr>
      <th>作用域</th>
      <th>当前类</th>
      <th>同一package</th>
      <th>子孙类</th>
      <th>其他package</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>public</td>
      <td>√</td>
      <td>√</td>
      <td>√</td>
      <td>√</td>
    </tr>
    <tr>
      <td>protected</td>
      <td>√</td>
      <td>√</td>
      <td>√</td>
      <td>×</td>
    </tr>
    <tr>
      <td>friendly（不修饰）</td>
      <td>√</td>
      <td>√</td>
      <td>×</td>
      <td>×</td>
    </tr>
    <tr>
      <td>private</td>
      <td>√</td>
      <td>×</td>
      <td>×</td>
      <td>×</td>
    </tr>
  </tbody>
</table>

## 2020.06.29
### Problem 1
![image](/assets/img/nowcoder_img/20200629_1.png)

### Analysis
- Java中静态变量只能在类主体中定义，不能在方法中定义。静态变量属于类所有而不属于方法。
