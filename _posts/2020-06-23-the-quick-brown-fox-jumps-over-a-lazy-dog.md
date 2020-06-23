---
date: 2020-06-23 14:22:40
layout: post
title: Core Java™, Volume I–Fundamentals
subtitle: (Eleventh Edition) Book Notes
description: This revised edition of the classic Core Java™, Volume I–Fundamentals , is the definitive guide to Java for serious programmers who want to put Java to work on real projects.
image: assets/img/post_img/java1.png
optimized_image: assets/img/post_img/java2.png
category: java
tags:
  - java
author: Lam
---

# 第1章

> Java程序设计平台  
  Java发展简史  
  Java“白皮书”的关键术语  
  关于Java的常见误解
  Java applet与Internet  

## 1.1 程序设计平台
- Java并不只是一种语言，更是一个完整的平台。

## 1.2 Java“白皮书”的关键术语
### 1.2.1 简单性
### 1.2.2 面向对象

- 简单来讲，面向对象设计是程序是一种程序设计技术。它将重点放在数据（即对象）和对象的接口上。
- 面向对象和面向过程的区别 
> 用木匠打一个比方：一个“面向对象的”木匠始终首先关注的是所制作的椅子，其次才是所使用的工具；而一个“面向过程的”木匠主要考虑的是所使用的工具。
- 面向过程
> 优点：**性能比面向对象高**，因为类调用时现需要实例化，开销比较大，比较**消耗资源**；比如单片机、嵌入式开发、Linux/Unix等一般采用面向过程开发，性能是最重要的因素。  
  缺点：**不易维护、不易复用、不易扩展**。
- 面向对象
> 优点：**易维护、易复用、易拓展**，由于面向对象有**封装、继承、多态性**的特性，可以设计出**低耦合**的系统，使系统更加灵活、更加易于维护。
  缺点：**性能**比面向过程**低**。

### 1.2.3 分布式
- Java有一个丰富的例程库，用于处理TCP/IP等协议，通过URL打开和访问网络上的对象，其便捷程度就好像访问本第文件一样。

### 1.2.4 健壮性
- Java编译器能够检测出许多在其他语言中仅在运行时才能够检测出来的问题。

### 1.2.5 安全性
- Java一开始便设计成能够防范各种攻击，其中包括：  
> 运行时堆栈溢出，这是蠕虫和病毒常用的攻击手段。  
  破坏自己的进程空间之外的内存。  
  未经授权读写文件。  





## Inline HTML elements

HTML defines a long list of available inline tags, a complete list of which can be found on the [Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/HTML/Element).

- **To bold text**, use `<strong>`.
- *To italicize text*, use `<em>`.
- Abbreviations, like <abbr title="HyperText Markup Langage">HTML</abbr> should use `<abbr>`, with an optional `title` attribute for the full phrase.
- Citations, like <cite>&mdash; Thiago Rossener</cite>, should use `<cite>`.
- <del>Deleted</del> text should use `<del>` and <ins>inserted</ins> text should use `<ins>`.
- Superscript <sup>text</sup> uses `<sup>` and subscript <sub>text</sub> uses `<sub>`.

Most of these elements are styled by browsers with few modifications on our part.

# Heading 1

## Heading 2

### Heading 3

#### Heading 4

Vivamus sagittis lacus vel augue rutrum faucibus dolor auctor. Duis mollis, est non commodo luctus, nisi erat porttitor ligula, eget lacinia odio sem nec elit. Morbi leo risus, porta ac consectetur ac, vestibulum at eros.

## Code

Cum sociis natoque penatibus et magnis dis `code element` montes, nascetur ridiculus mus.

```js
// Example can be run directly in your JavaScript console

// Create a function that takes two arguments and returns the sum of those arguments
var adder = new Function("a", "b", "return a + b");

// Call the function
adder(2, 6);
// > 8
```

Aenean lacinia bibendum nulla sed consectetur. Etiam porta sem malesuada magna mollis euismod. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa.

## Lists

Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus. Aenean lacinia bibendum nulla sed consectetur. Etiam porta sem malesuada magna mollis euismod. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa justo sit amet risus.

* Praesent commodo cursus magna, vel scelerisque nisl consectetur et.
* Donec id elit non mi porta gravida at eget metus.
* Nulla vitae elit libero, a pharetra augue.

Donec ullamcorper nulla non metus auctor fringilla. Nulla vitae elit libero, a pharetra augue.

1. Vestibulum id ligula porta felis euismod semper.
2. Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus.
3. Maecenas sed diam eget risus varius blandit sit amet non magna.

Cras mattis consectetur purus sit amet fermentum. Sed posuere consectetur est at lobortis.

Integer posuere erat a ante venenatis dapibus posuere velit aliquet. Morbi leo risus, porta ac consectetur ac, vestibulum at eros. Nullam quis risus eget urna mollis ornare vel eu leo.

## Images

Quisque consequat sapien eget quam rhoncus, sit amet laoreet diam tempus. Aliquam aliquam metus erat, a pulvinar turpis suscipit at.

![placeholder](https://placehold.it/800x400 "Large example image")
![placeholder](https://placehold.it/400x200 "Medium example image")
![placeholder](https://placehold.it/200x200 "Small example image")

## Tables

Aenean lacinia bibendum nulla sed consectetur. Lorem ipsum dolor sit amet, consectetur adipiscing elit.

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Upvotes</th>
      <th>Downvotes</th>
    </tr>
  </thead>
  <tfoot>
    <tr>
      <td>Totals</td>
      <td>21</td>
      <td>23</td>
    </tr>
  </tfoot>
  <tbody>
    <tr>
      <td>Alice</td>
      <td>10</td>
      <td>11</td>
    </tr>
    <tr>
      <td>Bob</td>
      <td>4</td>
      <td>3</td>
    </tr>
    <tr>
      <td>Charlie</td>
      <td>7</td>
      <td>9</td>
    </tr>
  </tbody>
</table>

Nullam id dolor id nibh ultricies vehicula ut id elit. Sed posuere consectetur est at lobortis. Nullam quis risus eget urna mollis ornare vel eu leo.










