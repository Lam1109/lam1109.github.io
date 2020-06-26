---
date: 2020-06-23 14:22:40
layout: post
title: Core Java™, Volume I–Fundamentals
subtitle: (Eleventh Edition) Book Notes
description: The book notes of Core Java™, Volume I–Fundamentals.
image: /assets/img/post_img/corejava1.png
optimized_image: /assets/img/post_img/java2.png
category: java
tags:
  - java
  - fundamentals
author: Lam
---

# 第1章 Java程序设计概述

> Java程序设计平台&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Java发展简史  
Java“白皮书”的关键术语&nbsp;&nbsp;&nbsp;&nbsp;Java applet与Internet  

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
> 1. 运行时堆栈溢出，这是蠕虫和病毒常用的攻击手段。  
> 2. 破坏自己的进程空间之外的内存。  
> 3. 未经授权读写文件。  

### 1.2.6 体系结构中立
- Java编译器通过生成与特定计算机体系结构无关的字节码指令来实现这一特性。精心设计的字节码不仅可以很容易地在任何机器上解释执行，而且还可以动态地转换成本地机器代码。

### 1.2.7 可移植性
- 在Java中，数值类型有固定的字节数，这为代码移植提供了便利。而C/C++中，数值类型的字节数跟硬件有关。

### 1.2.8 解释型

### 1.2.9 高性能

### 1.2.10 多线程
- Java是**第一个**支持并发程序设计的主流语言。（多线程出现的原因：摩尔定律即将走到尽头。）
> 摩尔定律：当价格不变时，集成电路上可容纳的元器件数目，每隔18-24个月增加一倍。

### 1.2.11 动态性
- Java能够适应不断发展的环境。

## 1.3 Java applet 与 Internet
- 在网页中运行的Java程序称为 applet。

## 1.4 Java发展简史
- Sun公式的“Green”项目。
- 由“Oak”改名为“Java”。

# 第2章 Java程序设计环境


<table>
  <thead>
    <tr>
      <th>术语名</th>
      <th>缩写</th>
      <th>解释</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>Java Development Kit（Java开发工具包）</td>
      <td>JDK</td>
      <td>编写Java程序的程序员使用的软件</td>
    </tr>
    <tr>
      <td>Java Runtime Environment（Java运行时环境）</td>
      <td>JRE</td>
      <td>运行Java程序的用户使用的软件</td>
    </tr>
    <tr>
      <td>Server JRE（服务器JRE）</td>
      <td>——</td>
      <td>在服务器上运行Java程序的软件</td>
    </tr>
    <tr>
      <td>Standard Edition（标准版）</td>
      <td>SE</td>
      <td>用于桌面或简单服务器应用的Java平台</td>
    </tr>
    <tr>
      <td>Enterprise Edition（企业版）</td>
      <td>EE</td>
      <td>用于复杂服务器应用的Java平台</td>
    </tr>
  </tbody>
</table>

# 第3章 Java的基本程序设计结构
> 数据类型&nbsp;&nbsp;&nbsp;&nbsp;变量与常量  
  运算符&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;字符串  
  输入输出&nbsp;&nbsp;&nbsp;&nbsp;控制流  
  大数&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;数组  

## 3.1 数据类型

### 3.1.1 整型

<table>
  <thead>
    <tr>
      <th>类型</th>
      <th>存储需求</th>
      <th>取值范围</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>int</td>
      <td>4字节</td>
      <td>-2 147 483 648 ~ 2 147 483 （2<sup>4*8</sup>）</td>
    </tr>
    <tr>
      <td>short</td>
      <td>2字节</td>
      <td>-32 768 ~ 32767 （2<sup>2*8</sup>）</td>
    </tr>
    <tr>
      <td>long</td>
      <td>8字节</td>
      <td>-9 223 372 036 854 775 808 ~ 9 223 372 036 854 775 807 （2<sup>8*8</sup>）</td>
    </tr>
    <tr>
      <td>byte</td>
      <td>1字节</td>
      <td> -128 ~ 127 （2<sup>1*8</sup>）</td>
    </tr>
  </tbody>
</table>

### 3.1.2 浮点型
<table>
  <thead>
    <tr>
      <th>类型</th>
      <th>存储需求</th>
      <th>取值范围</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>float</td>
      <td>4字节</td>
      <td>大约±3.402 823 47E+38F（有效位数为6~7位）</td>
    </tr>
    <tr>
      <td>double</td>
      <td>8字节</td>
      <td>大约±1.797 693 134 862 315 70E+308（有效位数为15位）</td>
    </tr>
  </tbody>
</table>

- 三个特殊的浮点数值：
> 正无穷大  
  负无穷大  
  NaN（不是一个数字）（计算0/0或者负数的平方根结果为NaN。）

### 3.1.3 char类型
- 由于一些Unicode字符需要两个char值描述，所以使用char类型可能导致乱码。

### 3.1.4 boolean类型
- True 
- False

## 3.2 变量与常量

### 3.2.1 变量声明与初始化
- 如果可以从变量的初始值推断出它的类型，就可以使用关键字var而无须指定类型。

```java
var n = 12; //number is an int
var s = "Lam"; //s is a String
```
### 3.2.2 常量
- 在Java中，利用关键字final指示常量，表示这个变量只能被赋值一次。习惯上，常量名使用全大写。  

```java
final double PI = 3.14;
```
### 3.2.3 枚举类型
```java
enum Size { SMALL, MEDIUM, LARGE, EXTRA_LARGE };
//现在可以声明这种类型的变量
Size s = Size.MEDIUM;
```
## 3.3 运算符

### 3.3.1 算术运算符
### 3.3.2 数学函数与常量
```java
double y = Math.pow(x,a); //将y的值设置为x的a次幂
double x = Math.PI； //将x的值设置为π
```
### 3.3.3 强制类型转换
```java
double x = 9.997;
int nx = (int) x; //nx = 9，截取整数部分
int nx = (int) Math.round(x); //nx =10，四舍五入
```
### 3.3.4 位运算符
- “&” （与）
- “&#124;” （或）
- “^” （异或）
- “~” （非）
- “>>” （右移）
- “<<” （左移）
> 移位运算符的右操作数要完成模**32**的运算（左操作数为long类型时，模64）。  

```java
1 << 35 = 1 << 3;
```
## 3.4 字符串
- Java没有**内置**的字符串类型，而是在标准Java类库中提供了一个预定义类**String**。

### 3.4.1 子串
```java
String greeting = "Hello";
String s = greeting.substring(0,3); // s = "Hel"
```
- substring方法的第二个参数是**不想复制的第一个位置**。

### 3.4.2 拼接
```java
//join方法
String all = String.join("/","S","M","L","XL"); // all is "S/M/L/XL";
//repeat方法
String repeated = "LAM".repeat(3);// repeated is "LAMLAMLAM";
```
- 提示: Java字符串不是字符数组。（C程序员初学Java误区。）

### 3.4.3 检测字符串是否相等
- 不能使用 == 运算符检测两个字符串是否相等，而要使用**equals**方法。  

```java
s.equals(t); //返回值为 true 或者 false
```

### 3.4.4 空串与Null串
- 空串不等同于Null串。
- 空串是一个Java对象，有自己的串长度（0）和内容（空）。
- 检查一个字符串既不是null也不是空串

```java
if (str != null && str.length() != 0) //先检查是否null，因为对null串调用length()方法会报错
```

### 3.4.5 构建字符串
```java
StringBuilder sb = new StringBuilder();
sb.append(ch);// appends a single character
sb.append(str);// appends a string
String completedString = sb.toString(); 
```

## 3.5 输入与输出

### 3.5.1 读取输入
```java
Scanner in = new Scanner(System.in);
String name = in.nextLine();
```

> nextLine() 读一行  
  next() 读一次（空格结束）  
  nextInt() 读一整数

### 3.5.2 格式化输出
```java
System.out.println("LAM"); // print LAM
```

### 3.5.3 文件输入与输出

```java
//文件输入

//方法一
Scanner in = new Scanner(Path.of("inputfile.txt"),StandardCharsets.UTF_8);
String s1=in.nextLine();

//方法二（性能高） 
BufferedReader bf = new BufferedReader(new FileReader("inputfile.txt"));
String s2=bf.readLine(); //BufferedReader的read()方法返回int类型。

//...

//文件输出
PrintWriter pw = new PrintWriter("outputfile.txt",StandardCharsets.UTF_8);
pw.print("hello");
pw.close();// 必要
```

### 3.5.4 中断控制流程的语句

```java
break; // 终止循环
continue； // 终止本次循环，跳到循环首部(进行下一次循环)。
```

## 3.6 大数
- 如果基本的整数和浮点数精度不够，可以使用**java.math**包中的**BigInteger**和**BigDecimal**;

## 3.7 数组

### 3.7.1 声明数组

```java
int[] a = new int[100]; // or var a = new int[100];
```

### 3.7.2 for each 循环

```java
for (int e : a){
    System.out.println(e); // 打印数组a中每一个元素
}
```

### 3.7.3 数组拷贝
- 在Java中，允许将一个数组变量拷贝到另一个数组变量。

```java
int[] lunckyNumbers = smallPrimes;
luckyNumbers[5] = 12; // now smallPrimes[5] is also 12
//即不会新建数组，本质上是同一个数组。
```

- 要新建一个数组，可以使用**Arrays**类中的**copyOf**方法。

```java
inr[] copy = Arrays.copyOf(luckyNumbers, luckyNumbers.length); // copy是一个新的数组
```

- **length用于获取数组长度，而length()用于获取字符串长度。**

### 3.7.4 数组排序
- 使用**Arrays**类中的**sort**方法。

```java
// int[] a = {3,1,2};
Arrays.sort(a); //  升序，优化的快速排序（QuickSort）算法
// now a = {1,2,3}
```

### 3.7.5 多维数组
- Java实际上没有多维数组，多维数组被解释为“数组的数组”。

# 第4章 对象与类
> 面对对象程序设计概述  
  对象构造  
  使用预定义类  
  包  
  用户自定义类  
  静态字段与静态方法  
  文档注释
  方法参数  
  类设计技巧

## 4.1 面对对象程序设计概述
- 面对对象程序设计（object-oriented programming，**OOP**）是当今主流的程序设计范型。
- Algorithms + Data Structures = Programs

### 4.1.1 类
- **类**（class）是构造对象的模板或者蓝图。

### 4.1.2 对象
- 对象的三个主要特性：
> 对象的行为——可以对对象完成哪些操作，或者可以对对象应用哪些方法？  
  对象的状态——当调用方法时，对象会如何响应？  
  对象的标识——如何区分具有相同行为与状态的不同对象？  

### 4.1.3 识别类
- 识别类的一个简单经验是在分析问题的过程中寻找名词，而方法对应着动词。

### 4.1.4 类之间的关系
- **依赖**（use-a）
- **聚合**（has-a）
- **继承**（is-a）

## 4.2 使用预定义类
### 4.2.1 对象与对象变量
- 构造器的名字应该与类名相同。

## 4.3 静态字段与静态方法
### 4.3.1 静态字段
- 如果将一个字段定一为**static**，每个类只有一个这样的字段。
- 静态字段被称为**类字段**。术语“静态”至少沿用了C++的叫法，并无实际意义。

```java
class Employee{
    private static int nextId = 1;
    private int id;
    ...
}
```
> 每一个Employee对象都有一个自己的id字段，但这个类的所有实例将共享一个nextId字段。

### 4.3.2 静态常量

```java
public class Math{
    ...
    public static final double PI = 3.14159265358979323846;
    ...
}
```
> 在程序中我们就可以使用Math.PI来访问这个常量。

### 4.3.3 静态方法
- 以下两种情况可以使用静态方法：
> 方法不需要访问对象状态，因为它需要的所有参数都通过显示参数提供（例如：Math.pow）。  
  方法只需要访问类的静态字段。

## 4.4 方法参数
- 在Java中，对方法参数能做什么和不能做什么：
> 方法不能修改基本数据类型的参数（即数值型和布尔型）。  
  方法可以改变对象参数的状态。
  方法不能让一个对象参数引用一个新的对象。

## 4.5 对象构造
### 4.5.1 重载
- 有些类有多个构造器。这种功能叫做**重载**（overloading）。

### 4.5.2 默认字段初始化
- 数值为0
- 布尔值为false
- 对象引用为null

### 4.5.3 无参数的构造器
- 由无参数构造器创建对象时，对象的状态会设置为设当的默认值。如果写一个类时没有编写构造器，就会为你提供一个无参数构造器。

### 4.5.4 参数名

```java
public Employee(String name, double salary){
    this.name = name;
    this.salary = salary;
}
```

## 4.6 包
- C/C&#43;&#43;程序员经常将import与#include弄混。实际上，两者完全不同。C/C&#43;&#43;编译器无法查看任何其他文件的内部，必须使用#include来加载外部特性的声明。Java编译器则不同，可以查看其他文件的内部，只需要告诉它到哪里去查看就可以了。

## 4.7 文档注释
### 4.7.1 类注释
- 类注释必须放在import语句之后，类定义之前。

### 4.7.2 方法注释
```java
/**
* @param variable description 参数
* @return description 返回值
* @throws class description 可能的异常
*/
```

### 4.7.3 通用注释

```java
/**
* @author name 作者
* @version text 版本
*/
```

## 4.8 类设计技巧
> 1. 一定要保证数据私有。  
> 2. 一定要对数据进行初始化。
> 3. 不要在类中使用过多的基本类型。
> 4. 不是所有的字段都需要单独的字段访问器和字段更改器。
> 5. 分解有过多职责的类。
> 6. 类名和方法名要能够体现它们的职责。
> 7. 优先使用不可变的类。








