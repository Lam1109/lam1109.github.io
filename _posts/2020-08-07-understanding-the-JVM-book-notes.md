---
date: 2020-08-07 14:23:40
layout: post
title: Understanding the JVM
subtitle: Advanced Features and Best Practices, Third Edition.
description: The book notes of Understanding the JVM.
image: /assets/img/post_img/jvm1.png
optimized_image: /assets/img/post_img/jvm1.png
category: java
tags:
  - jvm
  - advanced features
author: Lam
---

# 第1章 走进Java
## 1.1 概述
- Java不仅仅是一门编程语言，它还是一个由一系列计算机软件和规范组成的技术体系。
- Java能获得广泛的认可，除了它拥有一门结构严谨、面向对象的编程语言之外，还有许多不可忽视的优点：

> 1. 它摆脱了硬件平台的舒服，实现了“一次编写，到处运行”的理想。
> 2. 它提供了一种相对安全的内存管理和访问机制，避免了绝大部分内存泄漏和指针越界问题。
> 3. 它实现了热点代码检测和运行时编译及优化，这使得Java应用能随着运行时间的增长而获得更高的性能。
> 4. 它有一套完善的应用程序接口。

## 1.2 Java技术体系
- JCP（Java Community Process）定义的Java技术体系包括以下几个组成部分：

> 1. Java程序设计语言
> 2. 各种硬件平台上的Java虚拟机实现
> 3. Class文件格式
> 4. Java类库API
> 5. 来自商业机构和开源社区的第三方Java类库

- 把Java程序设计语言、Java虚拟机、Java类库统称为JDK（Java Development Kit），JDK是用于支持整个Java程序开发的最小环境。
- 把Java类库API重的Java SE API子集和Java虚拟机统称为JRE（Java Runtime Environment），JRE是支持Java程序运行的标准环境。

## 1.3 Java发展史
- 略

## 1.4 Java虚拟机家族
### 1.4.1 虚拟机始祖：Sun Classic/Exact VM
- Sun Classic是世界上第一款商用的Java虚拟机。
- Exact VM因它使用准确式内存管理（Exact Memory Managemeent，也可以叫Non-Conservative/Accurate Memory Management）而得名。

### 1.4.2 武林盟主：HotSpot VM
- **HotSpot VM**是Sun/OracleJDK和OpenJDK中的默认Java虚拟机，也是目前使用范围最广的Java虚拟机。

---

# 第2章 Java内存区域与内存溢出异常
## 2.1 概述
- Java和C++之间有一堵由内存动态分配和垃圾收集技术所围成的高墙，墙外的人想进去，墙里面的人却想出来。

## 2.2 运行时区域
- Java虚拟机在执行Java程序的过程中会把它所管理的内存划分为若干个不同的数据区域，如图所示。

![image](/assets/img/post_img/RuntimeDataArea.png)

### 2.2.1 程序计数器
- **程序计数器**（Program Counter Register）是一块较小的内存空间，它可以看作是当前线程所执行的字节码的行号指示器。

> 为了切换线程后能恢复到正确的执行位置，每条线程都有一个独立的程序计数器，各条线程之间计数器互不影响，独立存储，这类内存区域被称为“线程私有”的内存。

> 如果线程正在执行的是有一个Java方法，这个计数器记录的是正在执行的虚拟机字节码指令的地址；如果正在执行的是本地（Native）方法，这个计数器值则应该为空（Undefined）。

### 2.2.2 Java虚拟机栈
- **Java虚拟机栈**（Java Virtual Machine Stack）也是线程私有的， 它的生命周期与线程相同。
- 虚拟机栈描述的是Java方法执行的线程内存模型：每个方法执行的时候，Java虚拟机都会同步创建一个栈帧（Stack Frame）用于存储**局部变量表**、**操作数栈**、**动态连接**、**方法出口**等信息。
- **局部变量表**存放了编译期可知的各种Java虚拟机**基本数据类型**（boolean、byte、char、short、int、float、long、double）、**对象引用**（reference类型，它并不等同于对象本身，可能是一个指向对象起始地址的引用指针，也可能是指向一个代表对象的句柄或者其他与此对象相关的位置）和**returnAddress类型**（指向了一条字节码指令的地址）。
- 以上数据类型在局部变量表中的存储空间以**局部变量槽**（Slot）来表示，其中64位长度的long和double类型的数据会占用两个变量槽，其余的数据类型只占用一个。
- 异常：

> 1. 如果线程请求的栈深度大于虚拟机所允许的深度，将抛出StackOverflowError异常。
> 2. 如果Java虚拟机栈容量可以动态扩展，当栈扩展时无法申请到足够的内存会抛出OutOfMemoryError异常。

### 2.2.3 本地方法栈
- **本地方法栈**（Native Method Stack）与虚拟机栈所发挥的作用是非常相似的，其区别只是虚拟机栈为虚拟机执行Java方法（也就是字节码）服务，而本地方法栈则是为虚拟机使用到的本地（Native）方法服务。
- 与虚拟机栈一样，本地方法栈也会在栈深度溢出或者栈扩展失败时分别抛出StackOutflowError和OutOfMemoryError异常。

### 2.2.4 Java堆
- **Java堆**（Java Heap）是虚拟机所管理的内存中最大的一块。
- Java堆是被所有线程共享的一块内存区域，在虚拟机启动时创建。
- 此内存区域的唯一目的就是存放对象实例，Java世界里“几乎”所有的对象实例都在这里分配内存。
- Java堆是垃圾收集器管理的内存区域，因此一些资料中它也被称作“GC堆”（Garbage Collected Heap）。
- 从内存分配的角度看，所有线程共享的Java堆中可以划分出多个线程私有的**分配缓冲区**（Thread Local Allocation Buffer，TLAB），以提升对象分配时的效率。
- Java堆既可以被实现成固定大小的，也可以是扩展的。当前主流的虚拟机都是按照可扩展来实现的（通过参数-Xmx和-Xms设定）。
- 异常：

> 如果在Java堆中没有内存完成实例分配，并且堆也无法再扩展时，Java虚拟机将会抛出OutOfMemoryError异常。

### 2.2.5 方法区
- **方法区**（Method Area）与Java堆一样，是各个线程共享的内存区域，它用于存储已被虚拟机加载的**类型信息**、**常量**、**静态变量**、即时编译器编译后的**代码缓存**等数据。
- 异常：

> 如果方法区无法满足新的内存分配需求时，将抛出OutOfMemoryError异常。

### 2.2.6 运行时常量池
- **运行时常量池**（Runtime Constant Pool）是方法区的一部分。Class文件中除了有类的**版本**、**字段**、**方法**、**接口**等描述信息外，还有一项信息是**常量池表**（Constant Pool Table），运用存放编译期生成的各种字面量与符号引用，这部分内容将类加载后存放到方法区的运行时常量池中。
- 运行时常量池是方法区的一部分，自然受到方法区内存的限制，当常量池无法再申请到内存时会抛出OutOfMemoryError异常。

### 2.2.7 直接内存
- **直接内存**（Direct Memory）并不是虚拟机运行时数据区的一部分，也不是《Java虚拟机规范》中定义内存区域。

> 在JDK 1.4中新加入了NIO（New Input/Output）类，引入了一种基于通道（Channel）与缓冲区（Buffer）的I/O方法，它可以使用Native函数库直接分配堆外内存，然后通过一个存储在Java堆里面的DirectByteBuffer对象作为这块内存的引用进行操作。这样能在一些场景中显著提高性能，因为避免了在Java堆和Native堆中来回复制数据。

## 2.3 HotSpot虚拟机对象探秘
### 2.3.1 对象的创建
- Java是一门面向对象的编程语言，Java程序运行过程中无时无刻都有对象被创建出来。

> 在语言层面上，创建对象通常仅仅是一个new关键字而已；而在虚拟机中，对象的创建又是怎样一个过程呢？

- 创建过程：

> 1. 当Java虚拟机遇到一条字节码new指令时，首先将去检查这个指令的参数是否能在常量池中定位到一个类的符号引用，并且检查这个符号引用代表的类是否已被加载、解析和初始化过。如果没有，那么必须先执行相应的类加载过程。
> 2. 在类加载检查通过后，接下来虚拟机将为新生对象分配内存。对象所需内存的大小在类加载完成后便可完全确定，为对象分配空间的任务实际上等同于把一块确定大小的内存块从Java堆中划分出来。
> 3. 接下来，java虚拟机还要对对象进行必要的设置，例如这个对象是哪个类的实例、如何才能找到类的元数据信息，对象的哈希码、对象的GC分代年龄等信息。这些信息存放在对象对象头（Object Header）中。

### 2.3.2 对象的内存布局
- 在HotSpot虚拟机里，对象在堆内存中的存储布局可以划分为三个部分：**对象头**（Header）、**实例数据**（Instance Data）和**对齐补充**（Padding）。
- HotSpot虚拟机对象的**对象头部分**包括两类信息：

> 1. 第一类是用于存储对象自身的运行时数据，如哈希码（HashCode）、GC分代年龄、锁状态标志、线程持有的锁、偏向线程ID、偏向时间戳等，这部分数据的长度在32位和64位的虚拟机（未开启压缩指针）中分别为32个比特和64个比特，官方称它为“Mark Word”。
> 2. 第二类是类型指针，即对象指向它的类型元数据的指针，Java虚拟机通过这个指针来确定该对象是哪个类的实例。如果对象是一个Java数组，那在对象头中还必须有一块用于记录数组长度的数据，因为虚拟机可以通过普通Java对象的元数据信息确定Java对象的大小。

- **实例数据部分**是对象真正存储的有效信息，即我们在程序代码里面所定义的各种类型的字段内容，无论是从父类继承下来的，还是在子类中定义的字段都必须记录起来。

> 这部分的存储顺序会受到虚拟机分配策略参数（-XX:FieldsAllocationStyle参数）和字段在Java源码中定义顺序的影响。HotSpot虚拟机默认的分配顺序为longs/doubles、ints、shorts/chars、bytes/booleans、oops（Ordinary Object Pointers, OOPs），从以上默认的分配策略中可以看到，相同宽度的字段总是被分配到一起存放，在满足这个前提条件的情况下，在父类中定义的变量会出现在子类之前。

- **对齐填充部分**不是必需的，也没有特别的含义，仅仅起着占位符的作用。

> HotSpot虚拟机的自动内存管理系统要求对象起始地址必须是8字节的整数倍，也即任何对象的大小都必须是8字节的整数倍。

### 2.3.3 对象的访问定位
- Java程序会通过栈上的reference数据来操作堆上的具体对象。
- 对象访问方式也是由虚拟机实现的，主流的访问方式主要有使用**句柄**和**直接指针**两种：

> 1. 如果使用句柄访问的话，Java堆中将可能会划分出一块内存来作为句柄池，reference中存储的就是对象的句柄地址，而句柄中包含了对象实例数据与类型数据各自具体的地址信息。
> 2. 如果使用直接指针凡哥维纳的话，Java堆中对象的内存布局就必须考虑如何放置访问类型数据的相关信息，reference中存储的直接就是对象地址，如果只是访问对象本身的话，就不需要多一次间接访问的开销。

![image](/assets/img/post_img/ObjectAccess.png)

- 使用句柄访问的最大好处就是reference中存储的是稳定句柄地址，在对象被移动时，只会改变句柄中的实例数据指针，而reference本身不需要被修改。
- 使用直接指针访问的最大好处就是速度更快，它节省了一次指针定位的时间开销。

## 2.4 实战：OutOfMemoryError异常
### 2.4.1 Java堆溢出

- Java堆内存的OutOfMemoryError异常是实际应用中最常见的内存溢出异常情况。

```java
import java.util.ArrayList;
import java.util.List;

/**
 * VM Args: -Xms20m -Xmx20m -XX:+HeapDumpOnOutOfMemoryError
 *           堆初始值  堆最大值
 */
public class HeapOOM {
    static class OOMObject {

    }

    public static void main(String[] args) {
        List<OOMObject> list = new ArrayList<OOMObject>();

        while (true) {
            list.add(new OOMObject());
        }
    }
}
```
- 运行结果

> java.lang.OutOfMemoryError: Java heap space  
Dumping heap to java_pid3500.hprof ...  
Heap dump file created [30039453 bytes in 0.120 secs]  

