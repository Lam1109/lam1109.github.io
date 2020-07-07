---
date: 2020-06-20 12:27:40
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

# Java
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

## 2020.07.05
### Problem 1
![image](/assets/img/nowcoder_img/20200705_1.png)

### Analysis
- 假设两线程为A、B，设有3种情况：  

> 1. AB不并发：此时相当于两个方法顺序执行。A执行完后a=-1，B使用-1作为a的初值，B执行完后a=-2。  
> 2. AB完全并发：此时读写冲突，相当于只有一个线程对a的读写最终生效。相同于方法只执行了一次。此时a=-1。  
> 3. AB部分并发：假设A先进行第一次读写，得到a=1;之后A的读写被B覆盖了。B使用用1作为a的初值，B执行完后a=0。  

## 2020.07.07
### Problem 1
![image](/assets/img/nowcoder_img/20200707_1.png)

### Analysis
- 标识符是以**字母开头**的字母数字序列：数字是指0~9，字母指大小写英文字母、下划线（_)和美元符号（$），也可以是Unicode字符集中的字符，如汉字；字母、数字等字符的任意组合，不能包含+、- *等字符；不能使用关键字6；大小写敏感 。




***





# 计算机网络
## 2020.06.29
### Problem 1
![image](/assets/img/nowcoder_img/20200706_1.png)

### Analysis
- TCP协议原理：TCP每发送一个报文段，就启动一个定时器，如果在定时器超时之后还没有收到ACK确认，就重传该报文。 如图所示，数据包由A的缓冲区发往B，B在收到数据包以后，回发一个ACK确认包给A，之后A将该数据包从缓冲区释放。因此，该数据包会一直缓存在A的缓冲区，直到一个ACK确认为止。题目要求在100s内发送100GB数据，网络的传输速率至少是1G/s，某个数据包n在A中缓存的时间就是数据包n从A到B，再加上该数据包的ACK从B到A的时间：2\*1500m/(2\*108m/s)=1.5\*10-2s，该段时间A中缓存的数据量至少是1G/s\*1.5\*10-2s约为15M。

![image](/assets/img/nowcoder_img/20200706_2.png)
