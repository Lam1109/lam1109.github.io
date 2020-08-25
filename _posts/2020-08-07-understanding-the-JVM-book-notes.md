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

- 解决思路

> 解决这个内存区域的异常，常规的处理方法是首先通过内存映像分析工对Dump出来的堆转储快照进行分析。第一步首先应确认内存中导致OOM的对象是否是必要的，也就是要先分清楚到底是出现了内存泄漏（Memory Leak）还是内存溢出（Memory Overflow）。

### 2.4.2 虚拟机栈和本地方法栈溢出
- 虚拟机和本地方法栈有两种异常：

> 1. 如果线程请求的栈深度大于虚拟机所允许的最大深度，将抛出StackOverflowError异常。  
> 2. 如果虚拟机的栈内存允许扩展，当栈容量无法申请到足够的内存时，将抛出OutOfMemoryError异常。

#### 2.4.2.1 StackOverflowError异常
**1）使用-Xss参数减少栈内存容量**

> 结果：抛出StackOverflowError异常，异常出现时输出的堆栈深度相应缩小。
 
```java
/**
 * VM Args: -Xss180k
 */
public class JavaVMStackSOF1 {
    private int stackLength = 1;

    public void stackLeak() {
        stackLength++;
        stackLeak();
    }

    public static void main(String[] args) throws Throwable {
        JavaVMStackSOF1 oom = new JavaVMStackSOF1();
        try {
            oom.stackLeak();
        } catch (Throwable e) {
            System.out.println("stack length:" + oom.stackLength);
            throw e;
        }
    }
}
```

> 对于不同版本的Java虚拟机和不同的操作系统，栈容量最小值可能会有所限制，这主要取决于操作系统内存分页大小。譬如上述方法中的参数-Xss180k可以正常用于64位Windows系统下的JDK 11，而在Linux下这个值可能是228K。

- 运行结果

> stack length:1538  
Exception in thread "main" java.lang.StackOverflowError  
	at oom.stack.JavaVMStackSOF1.stackLeak(JavaVMStackSOF1.java:10)  
	at oom.stack.JavaVMStackSOF1.stackLeak(JavaVMStackSOF1.java:11)  
	at oom.stack.JavaVMStackSOF1.stackLeak(JavaVMStackSOF1.java:11)  
	...
	
**2）定义大量的本地变量**
- 定义了大量的本地变量，增大此方法帧中本地变量表的长度。

> 结果：抛出StackOverflowError异常，异常出现时输出的堆栈深度相应缩小。

```java
public class JavaVMStackSOF2 {
    private static int stackLength = 0;

    public static void test() {
        long unused1, unused2, unused3, unused4, unused5,
             unused6, unused7, unused8, unused9, unused10,
             unused11, unused12, unused13, unused14, unused15,
             unused16, unused17, unused18, unused19, unused20,
             unused21, unused22, unused23, unused24, unused25,
             unused26, unused27, unused28, unused29, unused30,
             unused31, unused32, unused33, unused34, unused35,
             unused36, unused37, unused38, unused39, unused40,
             unused41, unused42, unused43, unused44, unused45,
             unused46, unused47, unused48, unused49, unused50,
             unused51, unused52, unused53, unused54, unused55,
             unused56, unused57, unused58, unused59, unused60,
             unused61, unused62, unused63, unused64, unused65,
             unused66, unused67, unused68, unused69, unused70,
             unused71, unused72, unused73, unused74, unused75,
             unused76, unused77, unused78, unused79, unused80,
             unused81, unused82, unused83, unused84, unused85,
             unused86, unused87, unused88, unused89, unused90,
             unused91, unused92, unused93, unused94, unused95,
             unused96, unused97, unused98, unused99, unused100;

        stackLength ++;

        test();

        unused1 = unused2 = unused3 = unused4 = unused5 =
        unused6 = unused7 = unused8 = unused9 = unused10 =
        unused11 = unused12 = unused13 = unused14 = unused15 =
        unused16 = unused17 = unused18 = unused19 = unused20 =
        unused21 = unused22 = unused23 = unused24 = unused25 =
        unused26 = unused27 = unused28 = unused29 = unused30 =
        unused31 = unused32 = unused33 = unused34 = unused35 =
        unused36 = unused37 = unused38 = unused39 = unused40 =
        unused41 = unused42 = unused43 = unused44 = unused45 =
        unused46 = unused47 = unused48 = unused49 = unused50 =
        unused51 = unused52 = unused53 = unused54 = unused55 =
        unused56 = unused57 = unused58 = unused59 = unused60 =
        unused61 = unused62 = unused63 = unused64 = unused65 =
        unused66 = unused67 = unused68 = unused69 = unused70 =
        unused71 = unused72 = unused73 = unused74 = unused75 =
        unused76 = unused77 = unused78 = unused79 = unused80 =
        unused81 = unused82 = unused83 = unused84 = unused85 =
        unused86 = unused87 = unused88 = unused89 = unused90 =
        unused91 = unused92 = unused93 = unused94 = unused95 =
        unused96 = unused97 = unused98 = unused99 = unused100 = 0;
    }

    public static void main(String[] args) {
        try {
            test();
        } catch (Error e) {
            System.out.println("stack length:" + stackLength);
            throw e;
        }
    }
}
```

- 运行结果

> stack length:592  
Exception in thread "main" java.lang.StackOverflowError  
	at oom.stack.JavaVMStackSOF2.test(JavaVMStackSOF2.java:28)  
	at oom.stack.JavaVMStackSOF2.test(JavaVMStackSOF2.java:30)  
	at oom.stack.JavaVMStackSOF2.test(JavaVMStackSOF2.java:30)  
	...
	
- 无论是由于栈帧太大还是虚拟机容量太小，当新的栈帧内存无法分配的时候，HotSpot虚拟机抛出的都是StackOverflowError异常。

#### 2.4.2.1 OutOfMemoryError异常

```java
package oom.stack;

/**
 * VM Args: -Xss2M （32位系统下运行）
 */
public class JavaVMStackOOM {
    private void dontStop() {
        while (true) {
        }
    }

    public void stackLeakByThread() {
        while (true) {
            Thread thread = new Thread(new Runnable() {
                @Override
                public void run() {
                    dontStop();
                }
            });
            thread.start();
        }
    }

    public static void main(String[] args) throws Throwable {
        JavaVMStackOOM oom = new JavaVMStackOOM();
        oom.stackLeakByThread();
    }
}
```

- 运行结果（32位操作系统）

> Exception in thread "main" java.lang.OutOfMemoryError: unable to create native thread

### 2.4.3 方法区和运行时常量池溢出

> String::intern()是一个本地方法，它的作用是如果字符串常量池中已经包含一个等于此String对象的字符串，则返回代表池中这个字符串的String对象的引用；否则，会将此String对象包含的字符串添加到常量池中，并且返回此String对象的引用。


```java
    String a = "this is a test";
    String b = new String("this is a test");
    System.out.println( b.intern() == a); // true
    System.out.println( b.intern() == b); // false
```

```java
public class RuntimeConstantPoolOOM {

    public static void main(String[] args) {
        String str1 = new StringBuilder("计算机").append("软件").toString();
        System.out.println(str1.intern() == str1);

        String str2 = new StringBuilder("ja").append("va").toString();
        System.out.println(str2.intern() == str2);
    }
}
```

> 上述代码在JDK 6中运行，会得到两个false，而在JDK 7及以上版本中运行，会得到一个true和false。产生差异的原因是因为字符串常量池移到了Java堆中。
>> 加载sun.misc.Version这个类的时候java字符串已经进入常量池了。详情参考知乎<a href="https://www.zhihu.com/question/51102308/answer/124441115">https://www.zhihu.com/question/51102308/answer/124441115</a>

### 2.4.4 本机直接内存溢出

```java
import sun.misc.Unsafe;
import java.lang.reflect.Field;

/**
 * VM Args: -Xmx20M -XX:MaxDirectMemorySize=10M
 *                   指定直接内存大小
 */
public class DirectMemoryOOM {
    private static final int _1MB = 1024 * 1024;

    public static void main(String[] args) throws Exception {
        Field unsafeField = Unsafe.class.getDeclaredFields()[0];
        unsafeField.setAccessible(true);
        Unsafe unsafe = (Unsafe) unsafeField.get(null);
        while (true) {
            unsafe.allocateMemory(_1MB);
        }
    }
}
```

- 运行结果

> Exception in thread "main" java.lang.OutOfMemoryError  
	at java.base/jdk.internal.misc.Unsafe.allocateMemory(Unsafe.java:619)  
	at jdk.unsupported/sun.misc.Unsafe.allocateMemory(Unsafe.java:461)  
	at oom.directmemory.DirectMemoryOOM.main(DirectMemoryOOM.java:19)  
	
# 第3章 垃圾收集器与分配策略
## 3.1 概述

- **垃圾收集器**（Garbage Collection, GC）需要完成三件事情：

> 1. 哪些内存需要回收？
> 2. 什么时候回收？
> 3. 如何回收？

- Java运行时区域的各个部分，其中**程序计数器**、**虚拟机栈**和**本地方法栈**3个区域随线程而生，随线程而灭，栈中的栈帧随着方法的进入和退出而有条不紊地执行着出栈和入栈操作。而**Java堆**和**方法区**这两个区域则有着很明显的不确定性：一个接口的多个实现类需要的内存可能不一样，一个方法所执行的不同条件分支所需要的内存也可能不一样，这部分内存的分配和回收是动态的。垃圾收集器就关注的这部分内存该如何管理。

## 3.2 对象已死？
### 3.2.1 引用计数法
- **原理**：在对象中添加一个引用计数器，每当有一个地方引用它时，计数器值就加一；当引用失效时，计数器就减一；任何时刻计数器为零的对象就是不可能再被使用的。
- **优点**：原理简单，效率高。
- **缺陷**：难以解决对象间相互引用的问题。

```java
// objA和objB相互引用，引用计数器值都不为0。
public class ReferenceCountingGC {
    public Object instance = null;
    private static final int _1MB = 1024 * 1024;

    private byte[] bigSize = new byte[2 * _1MB];

    public static void testGC() {
        ReferenceCountingGC objA = new ReferenceCountingGC();
        ReferenceCountingGC objB = new ReferenceCountingGC();
        objA.instance = objB;
        objB.instance = objA;
        
        objA = null;
        objA = null;
        
        System.gc();
    }
}
```

### 3.2.2 可达性分析法
- **原理**：通过一系列称为“GC Roots”的根对象作为起始节点集，从这些节点开始，根据引用关系向下搜索，搜索过程所走的路径称为“引用链”（Reference Chain），如果某个对象到GC Roots间没有任何引用链相连，或者用图论的话来说就是从GC Roots到这个对象不可达时，则证明此对象是不可能再被使用的。

![image](/assets/img/post_img/ReachabilityAnalysis.png)

- 在JAVA技术体系里面，固定可作为GC Roots的对象包括以下几种：

> 1. 在虚拟机栈（栈帧中的本地变量表）中引用的对象，譬如各个线程被调用的方法堆栈中使用到的参数、局部变量、临时变量等。
> 2. 在方法区中类静态属性引用的对象，譬如Java类的引用类型静态变量。
> 3. 在方法区中常量引用的对象，譬如字符串常量池（String Table）里的引用。
> 4. 在本地方法栈中JNI（即通常所说的Native方法）引用的对象。
> 5. Java虚拟机内部的引用，如基本数据类型对应的Class对象，一些常驻的异常对象等，还有系统类加载器。
> 6. 所有被同步锁（synchronized关键字）持有的对象。
> 7. 反映Java虚拟机内部情况的JMXBean、JVMTI中注册的回调、本地代码缓存等。

### 3.2.3 再谈引用
- 在JDK 1.2之前，Java中引用的定义是：如果reference类型的数据中存储的数值代表的是另外一块内存的起始地址，就称该reference数据是代表某块内存、某个对象的引用。
- 在JDK 1.2之后，Java将引用分为**强引用**（Strongly Reference）、**软引用**（Soft Reference）、**弱引用**（Weak Reference）和**虚引用**（Phantom Reference）4种，这4种引用强度依次减弱（强 > 软 > 弱 > 虚）。

> 1. 强引用是最传统的引用的定义，是指程序代码之中普遍存在的引用赋值，即类似“Object obj = new Object()”这种引用关系。无论任何情况下，只要强引用关系还存在，垃圾回收器就永远不会回收掉被引用的对象。
> 2. 软引用是用来描述一些还有用，但非必须的对象。只被软引用关联着的对象，在系统将要发生内存溢出异常前，会把这些对象列进回收范围之中进行第二次回收，如果这次回收还没有足够的内存，才回抛出内存溢出异常。JDK 1.2之后提供了SoftReference类来实现软引用。
> 3. 弱引用也是用来描述那些非必须对象，但是它的强度比软引用更弱一些，被弱引用关联的对象只能生存到下一次垃圾收集发生为止。当垃圾收集器开始工作，无论当前内存是否足够，都会回收掉只被弱引用关联的对象。JDK 1.2之后提供了WeakReference类来实现弱引用。
> 4. 虚引用也称为“幽灵引用”或者“幻影引用”，它是最弱的一种引用关系。一个对象是否有虚引用的存在，完全不会对其生存时间构成影响，也无法通过虚引用来取得一个对象实例。为一个对象设置虚引用关联的唯一目的只是为了能在这个对象被收集器回收时收到一个系统通知。JDK 1.2之后提供了PhantomReference类来实现虚引用。

### 3.2.4 回收方法区
- 方法区的垃圾收集主要回收两部分内容：**废弃的常量**和**不再使用的类型**。
- 判定一个常量是否被“废弃”相对简单，而判定一个类型是否属于“不再被使用的类”条件比较苛刻。需要同时满足以下三个条件：

> 1. 该类的所有实例都已经被回收，也就是Java堆中不存在该类及其任何派生子类的实例。
> 2. 加载该类的类加载器已经被回收，这个条件除非是经过精心设计的可替换类加载器的场景，如OSGi、JSP的重加载等，否则是很难达成的。
> 3. 该类对应的java.lang.Class对象没有在任何地方被引用，无法在任何地方通过反射访问该类的方法。

## 3.3 垃圾收集算法
- 从如何判定对象消亡的角度出发，垃圾收集算法可以划分为“引用计数式垃圾收集”（Reference Counting GC）和“追踪式垃圾收集”（Tracing GC）两大类，这两类也常被称作“直接垃圾收集”和“间接垃圾收集”。

### 3.3.1 分代收集理论
- 当前大多数垃圾收集器，都遵循了“分代收集”（Generational Collection）的理论进行设计，它建立在两个分代假说之上：

> 1. 弱分代假说：绝大多数对象都是朝生夕灭的。
> 2. 强分代假说：熬过越多次垃圾收集过程的对象就越难以消亡。

- 这两个假说共同奠定了多款常用的垃圾收集器的一致的设计原则：收集器应该将Java堆划分出不同的区域，然后将回收对象依据其年龄（即其熬过垃圾收集过程的次数）分配到不同的区域之中存储。
- 设计者一般至少将Java堆划分为**新生代**（Young Generation）和**老年代**（Old Generation）两个区域。
- 顾名思义，在新生代中，每次垃圾收集时都发现有大批对象死去，而每次回收后存活的少量对象，将会逐步晋升到老年代中存放。
- 由于“对象不是孤立的，对象之间会存在跨代引用”，需要对分代收集理论添加第三条经验法则：

> 3. 跨代引用假说：跨代引用相对于同代引用来说仅占极少数。

- 在新生代上建立一个全局的数据结构（称为“记忆集”，Remembered Set）。

> 这个结构把老年代划分成若干小块，标记出老年代的哪一块内存会存在跨代引用。这就使得不必为了少量的跨代引用去扫描整个老年代，也不必浪费空间专门记录每一个对象是否存在及存在哪些跨代引用。当发生Minor GC时，只有包含了跨代引用的小块内存里的对象才会被加入到GC Roots进行扫描。

- 垃圾收集名词：

> 1. 新生代收集（Minor GC/Young GC）：指目标只是新生代的垃圾收集。
> 2. 老年代收集（Major GC/Old GC）：指目标只是老年代的垃圾收集。目前只有CMS收集器会有单独收集老年代的行为。
> 3. 混合收集（Mixed GC）：指目标是收集整个新生代以及部分老年代的垃圾收集。目前只有G1收集器会有这种行为。
> 4. 整堆收集（Full GC）：收集整个Java堆和方法区的垃圾收集。

### 3.3.2 标记 - 清除算法
- 最早出现也是最基础的垃圾收集算法是“标记 - 清除”（Mark-Sweep）算法。分为“标记”和“清除”两个阶段：首先标记出所有需要回收的对象，在标记完成后，统一回收掉所有被标记的对象，也可以反过来，标记存活的对象，统一回收未被标记的对象。
- 缺陷：

> 1. 执行效率不稳定。标记和清除两个过程的执行效率随着对象数量的增长而降低。
> 2. 内存空间的碎片化问题。标记和清除之后会产生大量不连续的内存碎片，空间碎片太多不利于之后的内存分配。

![image](/assets/img/post_img/Mark-Sweep.png)

### 3.3.3 标记 - 复制算法
- 标记 - 复制算法常被简称为复制算法。它将可用内存按容量划分成大小相等的两块，每次只使用其中的一块。当这一块的内存用完了，就将还存活的对象复制到另外一块上面，然后再把已使用过的内存空间一次性清理掉。
- 缺陷：

> 1. 当有较多对象存活的时候，复制将产生大量的开销。
> 2. 将可用内存缩小为了原来的一半，空间浪费大。

- 这种算法被多种Java虚拟机用来回收新生代。
- 更优化的半区复制分代策略 —— Appel式回收。

> HotSpot虚拟机的Serial、ParNew等新生代收集器均采用了这种策略来设计新生代的内存布局。

> Appel式回收的具体做法是：把新生代分为一块较大的Eden空间和两块较小的Survivor空间（from和to），每次分配内存只使用Eden和其中一块Survivor。发生垃圾收集时，将Eden和Survivor中仍然存活的对象一次性复制到另外一块Survivor空间上，然后直接清理掉Eden和已用过的那块Survivor空间。当Survivor空间不足以容纳一次Minor GC之后存活的对象时，就需要依赖其他内存区域（大多数时候是老年代）进行分配担保（Handle Promotion）。

> HotSpot虚拟机默认Eden和一块Survivor的大小比例是8：1，也即每次新生代中可用内存空间为整个新生代容量的90%。只有一块Survivor空间（10%的新生代容量）是会被“浪费”的。

![image](/assets/img/post_img/Appel.png)

### 3.3.4 标记 - 整理算法
- 标记 - 整理算法的标记过程与前文一样，而后续的步骤不是直接对可回收对象进行清理，而是让所有存活的对象都向内存空间一端移动，然后直接清理掉边界以外的内存。
- 标记 - 清除算法与标记 - 整理算法的本质差异在于前者是一种非移动式的回收算法，而后者是移动式的。
- 移动存活对象并更新所有引用这些对象的地方是一种极为负重的操作，而且这种对象移动操作必须全程暂停用户应用程序才能进行，这种停顿被描述为“Stop The World”。
- 是否移动对象都存在各自利弊：移动则内存回收时会更复杂，但吞吐量更大。不移动则内存分配时会更复杂，但停顿时间短。

> 根据所需确定所要使用的算法，譬如HotSpot虚拟机里面关注吞吐量的Parallel Scavenge收集器是基于标记 - 整理算法的，而关注延迟的CMS收集器则是基于标记 - 清除算法的。

> 二者兼顾的方法：让虚拟机多数时间都采用标记 - 清除算法，暂时容忍内存碎片的存在，知道内存空间的碎片化程度已经大到影响对象分配时，再采用标记 - 整理算法收集一次，以获得规整的内存空间。基于标记 - 清除算法的CMS收集器面临空间碎片化过多时采用的就是这种处理方法。

## 3.4 HotSpot的算法细节实现*

## 3.5 经典垃圾收集器
- 收集算法是内存回收的方法论，垃圾收集器是内存回收的实践者。
- HotSpot虚拟机的垃圾收集器

> 连线代表可以搭配使用。

![image](/assets/img/post_img/HotSpotGC.png)

### 3.5.1 Serial收集器
- Serial收集器是最基础、历史最悠久的收集器，在JDK 1.3.1之前是HotSpot虚拟机新生代收集器的唯一选择。
- Serial收集器是一个**单线程**工作的收集器，但它的“单线程”的意义并不仅仅是说明它只会使用一个处理器或一条收集线程区完成垃圾收集工作，更重要的是强调在它进行垃圾收集器时，必须暂停其他所有工作线程，直到它收集结束。
- Serial/Serial Old收集器的运行过程：

![image](/assets/img/post_img/Serial_SerialOld.png)

- 虽然Serial收集器存在一系列的缺点，但是迄今为止它依然有着优于其他收集器的地方：

> 1. 简单高效（与其他收集器的单线程相比），对于内存资源受限的环境，它是所有收集器里额外内存消耗最小的。
> 2. 对于单核处理器或处理器核心较少的环境来说，Serial收集由于没有线程交互的开销，专心做垃圾收集自然可以获得最高的单线程收集效率。

### 3.5.2 ParNew收集器
- ParNew收集器实质上是Serial收集器的**多线程**并行版本，除了同时使用多条线程进行垃圾收集之外，其余的行为包括Serial收集器可用的所有控制参数、收集算法、Stop the World、对象分配原则、回收策略等都与Serial收集器完全一致，在实现上这两种收集器也共用了相当多的代码
- ParNew/Serial Old收集器的运行过程：

![image](/assets/img/post_img/ParNew_SerialOld.png)

- ParNew是JDK 7之前的遗留系统中首选的新生代收集器，其中一个与功能、性能无关的原因是：除了Serial收集器以外，目前只有它能与CMS收集器配合工作。

> 并行和并发都是并发编程中的专业名词，在谈论垃圾收集器的上下文语境中，它们可以理解为：
>> 并行（Parallel）：并行描述的是多条垃圾收集器线程之间的关系，说明同一时间有多条这样的线程在协同工作，通常默认此时用户线程是处于等待状态。  
>> 并发（Concurrent）：并发描述的是垃圾收集器线程与用户线程之间的关系，说明同一时间垃圾收集器线程与用户线程都在运行。

### 3.5.3 Parallel Scavenge收集器
- Parallel Scavenge收集器是一款**新生代**收集器，它同样是基于标记-复制算法实现的收集器，也是能够并行收集的**多线程**收集器。
- Parallel Scavenge收集器的特点是它的关注点与其他收集器不同，CMS等收集器的关注点是尽可能地缩短垃圾收集时用户线程的停顿时间，而Parallel Scavenge收集器的目标则是达到一个可控制的吞吐量（Throughput）。
- 吞吐量就是处理器用于运行用户代码的时间与处理器总消耗时间的比值，即：  
吞吐量 = 运行用户代码时间 / (运行用户代码时间 + 运行垃圾收集时间)
- 吞吐量与停顿时间：

> 停顿时间越短就越适合需要与用户交互或需要保证服务响应质量的程序，良好的响应速度能提升用户体验；  
> 高吞吐量则可以最高效率地利用处理器资源，尽快完成程序的运算任务，主要适合在后台运算而不需要太多交互的分析任务。

- Parallel Scavenge/Parallel Old收集器的运行过程：

![image](/assets/img/post_img/ParallelScavenge_ParallelOld.png)

### 3.5.4 Serial Old收集器
- Serial Old是Serial收集器的**老年代**版本，它同样是一个**单线程**收集器，使用标记-整理算法。
- Serial/Serial Old收集器的运行过程：

![image](/assets/img/post_img/Serial_SerialOld.png)

### 3.5.5 Parallel Old收集器
- Parallel Old是Parallel Scavenge收集器的**老年代**版本，支持**多线程**并发收集，基于标记-整理算法实现。
- Parallel Scavenge/Parallel Old收集器的运行过程：

![image](/assets/img/post_img/ParallelScavenge_ParallelOld.png)

### 3.5.6 CMS收集器
- CMS（Concurrent Mark Sweep）收集器是一种以获取最短回收停顿时间为目标的收集器。它基于标记-清除算法。
- CMS收集器的运行过程分为四个步骤：

> 1. 初始标记（CMS initial mark）
> 2. 并发标记（CMS concurrent mark）
> 3. 重新标记（CMS remark）
> 4. 并发清除（CMS concurrent sweep）

- 其中初始标记、重新标记这两个步骤仍然需要“Stop The World”。
- 各步骤的具体工作：

> 1. 初始标记仅仅只是标记一下GC Roots能直接关联到的对象，速度很快；
> 2. 并发标记就是从GC Roots的直接关联对象开始遍历整个对象图的过程，这个过程耗时较长但是不需要停顿用户线程，可以与垃圾收集线程一起并发运行；
> 3. 重新标记则是为了修正并发标记期间，因用户程序继续运作而导致标记产生变动的那一部分对象的标记记录，这个阶段的停顿时间通常会比初始标记阶段稍长一些，但也远比并发标记阶段的时间短。
> 4. 并发清除则是清理删除掉标记阶段判断的已经死亡的对象，由于不需要移动存活对象，所以这个阶段也是可以与用户线程同时并发的。

- 由于整个过程中耗时最长的并发标记喝并发清除井段中，垃圾收集器线程都可以与用户线程一起工作，所以从总体上来说，CMS收集器的内存回收过程是与用户线程一起并发执行的。
- CMS收集器的运行过程：

![image](/assets/img/post_img/CMS.png)

- CMS是一款优秀的收集器，主要优点即是：并发收集、低停顿，所以它也被称为“并发低停顿收集器”。
- CMS收集器的缺点：

> 1. CMS收集器对处理器资源非常敏感。
> 2. CMS收集器无法处理“浮动垃圾”（Floating Garbage），有可能出现“Concurrent Mode Failure”失败进而导致另一次完全“Stop The World”的Full GC的产生。
> 3. CMS收集器是基于标记-清除算法的，所以收集结束时可能会产生大量的空间碎片。

### 3.5.7 Garbage First（G1）收集器
- G1收集器是垃圾收集技术发展历史上的里程碑式的成果，它开创了收集器面向局部收集的设计思路和基于Region的内存布局形式。
- G1是一款主要面向服务端应用的垃圾收集器。

> 在G1收集器出现之前的所有其他收集器，包括CMS在内，垃圾收集的目标范围要么是整个新生代（Minor GC），要么就是整个老年代（Major GC）。而G1跳出了整个樊笼，它可以面向堆内存任何部分来组成回收集（Collection Set, 简称CSet）进行回收，衡量标准不再是它属于哪个分代，而是哪块内存中存放的垃圾数量最多，回收收益最大，这就是G1收集器的Mixed GC模式。

- G1开创的基于**Region**的堆内存布局是它能够实现这个目标的关键。虽然G1也是遵循分代收集理论设计的，但其堆内存的布局与其他收集器有非常明显的差异：

> G1不再坚持固定大小以及固定数量的分代区域划分，而是把连续的Java堆划分为多个大小相等的独立区域（Region），每个Region都可以根据需要，扮演新生代的Eden空间，Survivor空间，或者是老年代空间。

- Region中还有一类特殊的**Humongous**区域，专门用来存储大对象。G1认为只要大小超过了**一个Region容量的一半**的对象即可判定为大对象。

> 每个Region的大小可以通过参数 -XX:G1HeapRegionSize设定，取值范围为1MB~32MB，且应为2的N次幂。而对于超过了整个Region容量的超级大对象，将会被存放在N个连续的Humongous Region之中，G1的大多数行为都把Humongous Region作为老年代的一部分来进行对待。

- G1为每一个Region设计了两个名为TAMS（Top at Mark Start）的指针，把Region中的一部分空间划分出来用于并发回收过程中的新对象分配，并发回收时新分配的对象地址都必须要在这两个指针位置以上。 
- G1收集器的运行过程大致分为四个步骤：

> 1. 初始标记（Initial Marking）
> 2. 并发标记（Concurrent Marking）
> 3. 最终标记（Final Marking）
> 4. 筛选回收（Live Data Counting and Evacuation）

- 各步骤的具体工作：

> 1. 初始标记仅仅只是标记一下GC Roots能直接关联到的对象，并且修改TAMS指针的值，让下一阶段用户线程并发运行时，能正确地在可用的Region中分配新对象。这个阶段需要停顿线程，但耗时很短，而且是借用进行Minor GC的时候同步完成的，所以G1收集器在这个阶段实际并没有额外的停顿。 
> 2. 并发标记是从GC Roots开始对堆中对象进行可达性分析，递归扫描整个堆里的对象图，找出要回收的对象，这阶段耗时较长，但可与用户程序并发执行。当对象图扫描完成以后，还有重新处理SATB（原始快照）记录下的在并发时有引用变动的对象。
> 3. 最终标记是堆用户线程做另一个短暂的暂停，用于处理并发阶段结束后仍遗留下来的最后那少量的SATB记录。
> 4. 筛选回收扶着更新Region的统计数据，对各个Region的回收价值和成本进行排序，根据用户所期望的停顿时间来制定回收计划，可以自由选择任意多个Region构成回收集，然后把决定回收的那一部分Region的存活对象复制到空的Region中，再清理掉整个旧Region的全部空间。这里的操作涉及存活对象的移动，是必须暂停用户线程，由多条收集器线程并行完成的。

- G1收集器除了并发标记外，其余阶段也是要完全暂停用户线程的。
- G1收集器的运行过程：

![image](/assets/img/post_img/CMS.png)

- 从G1开始，最先进的垃圾收集器的设计导向都不约而同地变为追求能够应付应用的内存分配速率（Allocation Rate），而不追求一次把整个Java堆全部清理干净。
- G1从整体来看是基于“标记-整理”算法实现的，但从局部（两个Region之间）上看又是基于“标记-复制”算法实现的。
- 虽然G1最终是要取代CMS收集器的，但是目前在小内存应用上CMS的表现大概率仍然要会优于G1，而大内存应用上G1则大多能发挥其优势。这个优劣势的Java堆容量平衡点通常在6GB至8GB之间。

# 3.6 低延迟垃圾收集器
- 衡量垃圾收集器的三项最重要的指标是：**内存占用**（Footprint）、**吞吐量**（Throughput）和**延迟**（Latency），三者共同构成了一个“不可能三角”。在这三项指标中，**延迟**的重要性日益凸显，越发备受关注。
- 各款收集器的并发情况：

![image](/assets/img/post_img/GCConcurrent.png)

> Shenandoah和ZGC收集器，几乎整个工作过程全部都是并发的，只有初始标记、最终标记这些阶段有短暂的停顿，并且这部分的停顿时间基本上是固定的，与堆的容量、堆中对象的数量没有正比例关系。这两款收集器目前仍处于实验状态，被官方命名为“低延迟垃圾收集器”（Low-Latency Garbage Collector或Low-Pause-Time Garbage Collector）。

### 3.6.1 Shenandoah收集器
- Shenandoah是第一款不由Oracle公司的虚拟机团队所领导开发的HotSpot垃圾收集器。
- Shenandoah收集器更像是G1的下一代继承者，两者相似的堆内存布局，在初始标记、并发标记等许多阶段的处理思路都高度一致。
- Shenandoah相对于G1的不同之处：

> 1. 支持并发的整理算法，G1的回收阶段是可以多线程并行的，但却不能与用户线程并发。
> 2. Shenandoah是默认不使用分代收集的，不会有专门的新生代Region或者老年代Region的存在。
> 3. Shenandoah摒弃了在G1中耗费大量内存和计算资源去维护的记忆集，改用名为“连接矩阵”（Connection Matrix）的全局数据结果来记录夸Region的引用关系降低了跨代维护的消耗。

- Shenandoah收集器的工作过程大致可以划分为以下九个阶段：

> 1. 初始标记：与G1一样，首先标记与GC Roots直接关联的对象，这个阶段仍是“Stop The World”的，但停顿时间与堆大小无关，只与GC Roots的数量相关。
> 2. 并发标记：与G1一样，编辑对象图，标记出全部可达的对象，与用户线程一起并发，时间长短与堆中存活对象的数量以及对象图的结构复杂程度有关。
> 3. 最终标记：与G1一样，处理剩余的SATB扫描，并在这个阶段统计出回收价值最高的Region，将这些Region构成一组回收集。此阶段也会有一小段短暂的停顿。
> 4. 并发清理：这个阶段用于清理那些整个区域内连一个存活对象都没有找到的Region。
> 5. 并发回收：这个阶段是Shenandoah与之前HotSpot中其他收集器的核心差异。在这个阶段，Shenandoah要把回收集里面的存活对象先复制一份到其他未被使用的Region中。但是有个难点是在移动对象的同时，用户线程仍然可能不停的对被移动的对象进行读写访问，移动对象之后整个内存中所有指向该对象的引用都还是旧对象的地址，这是很难一瞬间全部改变过来的。对于这个难点，Shenandoah将会通过读屏障和被称为“Brooks Pointers”的转发指针来解决。并发回收阶段运行时间的长短取决于回收集的大小。
> 6. 初始引用更新：并发回收阶段复制对象结束后，还需要把堆中所有指向旧对象的引用修正蛋糕复制后的新地址，这个操作称为引用更新。这个阶段就是对这个操作进行初始化的，初始引用更新时间很短，会产生一个非常短暂的停顿。
> 7. 并发引用更新：真正开始进行引用更新操作，这个阶段是与用户线程一起并发的，时间长短取决于内存中涉及的引用数量的多少。
> 8. 最终引用更新：解决了堆中的引用更新后，还要修正存在于GC Roots 中的引用。这个阶段是Shenandoah的最后一次停顿，时间长短与GC Roots的数量有关。
> 9. 并发清理：经过并发回收和引用更新之后，整个回收集中所有的Region已再无存活对象，最后再调用一次并发清理过程来回收这些Region 的内存空间，供以后新对象分配使用。

- Shenandoah用以支持并行整理的核心概念——Brooks Pointer。

> 在原有对象布局结构的最前面统一增加一个新的引用字段，在正常不处于并发移动的情况下，该引用指向对象自己。

![image](/assets/img/post_img/BrooksPointers1.png)

> 转发指针加入后带来的收益自然是当对象拥有了一份新的副本时，只需要修改一处指针的值，即旧对象上转发指针的引用位置，使其指向新对象，便可以将所有对该对象的访问转发到新的副本上。

![image](/assets/img/post_img/BrooksPointers2.png)

### 3.6.2 ZGC收集器
- ZGC（“Z”并非专业名词的缩写，Z Garbage Collector）是一款在JDK 11中新加入的具有实验性质的低延迟垃圾收集器，由Oracle公司研发。
- ZGC和Shenandoah的目标是高度相似的，都希望在尽可能对吞吐量影响不大的前提下，实现在任意堆内存大小下都可以把垃圾收集的停顿时间限制在十毫秒以内的低延迟。
- ZGC的主要特征：ZGC收集器是一款**基于Region内存布局**的，（暂时）**不设分代**的，使用**读屏障**、**染色指针**和**内存多重映射**等技术来实现可并发的标记-整理算法的，以低延迟为首要目标的一款垃圾收集器。
- ZGC的Region（一些资料也称Page或者ZPage）具有动态性——动态的创建和销毁，以及动态的区域容量大小。
- 在**x64**硬件平台下，ZGC的Region可以具有大、中、小三类容量：

> 1. 小型Region（Small Region）：**容量固定为2MB**，用于放置小于256KB的小对象。
> 2. 中型Region（Medium Region）：**容量固定为32MB**，用于放置大于等于256KB但小于4MB的对象。
> 3. 大型Region（Large Region）：**容量不固定**，可以动态变化，但必须为2MB的整数倍，用于放置4MB或以上的大对象。每个大型Region中只会存放一个大对象，这也预示着虽然名字叫做“大型Region”，但它的实际容量完全有可能小于中型Region，最小容量可低至4MB。

![image](/assets/img/post_img/ZGCHeap.png)

- ZGC的运作过程：

![image](/assets/img/post_img/ZGC.png)

> 1. 并发标记（Concurrent Mark）
>> 遍历对象图做可达性分析的阶段, 前后也要经过类似于G1, Shenandoah 的初始标记, 最终标记的短暂停顿。
>> 与G1, Shenandoah不同的是, ZGC的标记是在指针上而不是在对象上进行的,   标记阶段会更新染色指针中的Marked0、Marked1标志位。
> 2. 并发预备重分配（Concurrent Prepare for Relocate）
>> 此阶段需要根据特定的查询条件统计出本次收集过程要清理哪些Region, 将这些Region组成重分配集(Relocation Set)。  
> 3. 并发重分配（Concurrent Relocate）
>> 是ZGC执行过程中的核心阶段, 此过程要把重分配集中的存活对象复制到新的Region上, 并为重分配集中的每个Region维护一个转发表(Forward Table), 记录从旧对象到新对象的转向关系。  
>> 由于染色指针的存在, ZGC能仅从引用上就明确得知一个对象是否处于重分配集之中。如果用户线程此时并发访问了位于重分配集中的对象, 这次访问将会被预置的内存屏障截获, 然后立即根据Region上的转发表记录将访问转发到新复制的对象上, 并同时修正该引用的值, 使其直接指向新对象, 此即为Self-Healing(自愈)[只有第一次访问旧对象会陷入转发]。
> 4. 并发重映射（Concurrent Remap）
>> 修正整个堆中指向重分配集中旧对象的所有引用。  
>> 重映射清理这些旧引用的主要目的是为了不变慢, 并不是很迫切。  
>> ZGC将并发重映射阶段要做的工作, 合并到了下一次垃圾收集循环中的并发标记阶段里去完成, 从而节省了一次遍历对象图的开销。

#### 3.6.2.1 染色指针技术

> 在64位系统中, 理论可以访问的内存高达16EB。实际上基于需求, 性能, 和成本考虑, 在AMD64架构中只支持到52位(4PB)的地址总线和48位(256TB)的虚拟地址空间, 目前64位的硬件实际能够支持的最大内存只有256TB。此外操作系统还有自己的约束, 64Linux系统分别支持47位(128TB)的进程虚拟地址和46位(64TB)的物理地址空间, 64位的Windows系统只支持44位(16TB)的物理地址空间。

> 虽然Linux下64位指针的高18位不能用来寻址, 剩余的46位指针所能支持的64TB内存在今天仍能够充分满足大型服务器需要。而ZGC则利用了剩下的46位指针的高4位提取出来用于存储四个标志信息。

![image](/assets/img/post_img/ColoredPointer.png)

- 染色指针的三大优势

> 1. 染色指针可以使得一旦某个Region的存活对象被移走之后, 此Region立即就能够被释放和重用掉, 而不必等待整个堆中所有指向该Region的引用都被修正后才能清理。
> 2. 染色指针可以大幅减少在垃圾收集过程中内存屏障的使用数量。
> 3. 染色指针可以作为一种可扩展的存储结构用来记录更多与对象标记、重定位过程相关的数据, 以便日后进一步提高性能。

#### 3.6.2.2 多重映射

> 处理器会使用分页管理机制把线性地址空间和物理地址空间分别划分为大小相同的块，这样的内存块被称为“页”（Page）。通过在线性虚拟空间的页与物理地址空间的页之间建立的映射表，分页管理机制会进行线性地址到物理地址空间的映射，完成线性地址到物理地址的转换。

- 在Linux/X86-64平台上的ZGC使用了多重映射(Multi-Mapping) 将多个不同的虚拟内存映射到同一个物理内存地址上。

> 任何的进程在进程自己看来自己的内存空间都是连续的, 但是计算机实际的物理内存并不是与该进程的内存是一一对应的。碎片化的物理内存可以映射成一个完整的虚拟内存, 同时应用可以申请比物理内存大的内存, 使得多个内存互不干扰, 使编译好的二进制文件的地址统一化......

## 3.7 选择合适的垃圾收集器
- 选择合适的垃圾收集器主要受三个因素的影响：

> 1. 应用程序的主要关注点是什么？譬如吞吐量或则是停顿时间等等。
> 2. 运行应用程序的基础设施如何？譬如硬件规格等等。
> 3. 使用JDK的发行商是扫什么？版本号是多少？

## 3.8 实战：内存分配与回收策略
- Java技术体系的自动内存管理，最根本的目标是自动化地解决两个问题：自动给对象分配以及自动回收分配给对象的内存。
- 在经典分代的设计下，新生对象通常会分配在新生代中，少数情况下（如对象大小超过一定阈值）也可能直接分配在老年代。

- 定义一个_1MB:

```java
private static final int _1MB = 1024 * 1024;
```

### 3.8.1 对象有限在Eden分配
- 大多数情况下，对象在新生代的Eden区中分配。当Eden区没有足够空间进行分配时，虚拟机将发起一次Minor GC。

```java
    /**
     * 对象优先在Eden分配
     * VM参数：-XX:+UseSerialGC -Xms20M -Xmx20M -Xmn10M -Xlog:gc* -XX:SurvivorRatio=8
     *         使用SerialGC     堆初始值  堆最大值  新生代   日志信息   新生代中Eden与一个Survivor区空间比例8:1
     */
    public static void testAllocation() {
        byte[] allocation1, allocation2, allocation3, allocation4;
        allocation1 = new byte[2 * _1MB];
        allocation2 = new byte[2 * _1MB];
        allocation3 = new byte[2 * _1MB];
        allocation4 = new byte[4 * _1MB]; // 出现一次Minor GC
    }
```

- 输出：

```
[0.067s][info][gc] Using Serial
[0.067s][info][gc,heap,coops] Heap address: 0x00000000fec00000, size: 20 MB, Compressed Oops mode: 32-bit
[0.474s][info][gc,start     ] GC(0) Pause Young (Allocation Failure)
[0.484s][info][gc,heap      ] GC(0) DefNew: 8167K->927K(9216K)
[0.484s][info][gc,heap      ] GC(0) Tenured: 0K->6144K(10240K)
[0.484s][info][gc,metaspace ] GC(0) Metaspace: 649K->649K(1056768K)
[0.484s][info][gc           ] GC(0) Pause Young (Allocation Failure) 7M->6M(19M) 9.935ms
[0.484s][info][gc,cpu       ] GC(0) User=0.00s Sys=0.02s Real=0.01s
[0.486s][info][gc,heap,exit ] Heap
[0.486s][info][gc,heap,exit ]  def new generation   total 9216K, used 5346K [0x00000000fec00000, 0x00000000ff600000, 0x00000000ff600000)
[0.486s][info][gc,heap,exit ]   eden space 8192K,  53% used [0x00000000fec00000, 0x00000000ff050cc0, 0x00000000ff400000)
[0.486s][info][gc,heap,exit ]   from space 1024K,  90% used [0x00000000ff500000, 0x00000000ff5e7d80, 0x00000000ff600000)
[0.486s][info][gc,heap,exit ]   to   space 1024K,   0% used [0x00000000ff400000, 0x00000000ff400000, 0x00000000ff500000)
[0.486s][info][gc,heap,exit ]  tenured generation   total 10240K, used 6144K [0x00000000ff600000, 0x0000000100000000, 0x0000000100000000)
[0.486s][info][gc,heap,exit ]    the space 10240K,  60% used [0x00000000ff600000, 0x00000000ffc00030, 0x00000000ffc00200, 0x0000000100000000)
[0.486s][info][gc,heap,exit ]  Metaspace       used 660K, capacity 4538K, committed 4864K, reserved 1056768K
[0.486s][info][gc,heap,exit ]   class space    used 60K, capacity 403K, committed 512K, reserved 1048576K
```

### 3.8.2 大对象直接进入老年代
- 大对象就是指需要大量连续内存空间的Java对象，最典型的大对象就是那种很长的字符串，或者元素数量很庞大的数组。

```java
    /**
     * 大对象直接进入老年代
     * VM参数：-XX:+UseSerialGC -Xms20M -Xmx20M -Xmn10M -Xlog:gc* -XX:SurvivorRatio=8
     * -XX:PretenureSizeThreshold=3145728
     *  超过3145728（3MB）被定义为大对象
     */
    public static void testPretenureSizeThreshold() {
        byte[] allocation;
        allocation = new byte[4 * _1MB]; // 直接分配在老年代中
    }
```

```
[0.018s][info][gc] Using Serial
[0.018s][info][gc,heap,coops] Heap address: 0x00000000fec00000, size: 20 MB, Compressed Oops mode: 32-bit
[0.176s][info][gc,heap,exit ] Heap
[0.176s][info][gc,heap,exit ]  def new generation   total 9216K, used 2187K [0x00000000fec00000, 0x00000000ff600000, 0x00000000ff600000)
[0.176s][info][gc,heap,exit ]   eden space 8192K,  26% used [0x00000000fec00000, 0x00000000fee22cc0, 0x00000000ff400000)
[0.176s][info][gc,heap,exit ]   from space 1024K,   0% used [0x00000000ff400000, 0x00000000ff400000, 0x00000000ff500000)
[0.176s][info][gc,heap,exit ]   to   space 1024K,   0% used [0x00000000ff500000, 0x00000000ff500000, 0x00000000ff600000)
[0.176s][info][gc,heap,exit ]  tenured generation   total 10240K, used 4096K [0x00000000ff600000, 0x0000000100000000, 0x0000000100000000)
[0.176s][info][gc,heap,exit ]    the space 10240K,  40% used [0x00000000ff600000, 0x00000000ffa00010, 0x00000000ffa00200, 0x0000000100000000)
[0.176s][info][gc,heap,exit ]  Metaspace       used 645K, capacity 4535K, committed 4864K, reserved 1056768K
[0.176s][info][gc,heap,exit ]   class space    used 59K, capacity 402K, committed 512K, reserved 1048576K
```

### 3.8.3 长期存活的对象将进入老年代
- 对象通常在Eden区里诞生，如果经过第一次Minor GC后仍然存活，并且能被Survivor容纳的话，该对象会被移动到Survivor空间中，并且将其对象年龄设为1岁。对象在Survivor区中每熬过一次MinorGC，年龄就增加一岁，当它的年龄到达一定程度（默认为15），就会被晋升到老年代中。

```java
    /**
     * 长期存活的对象将进入老年代
     * VM参数：-XX:+UseSerialGC -Xms20M -Xmx20M -Xmn10M -Xlog:gc* -XX:SurvivorRatio=8
     * -XX:MaxTenuringThreshold=1 -Xlog:gc+age=trace
     *  长期存活对象定义为年龄超过1
     */
    public static void testTenuringThreshold() {
        byte[] allocation1, allocation2, allocation3;
        allocation1 = new byte[_1MB / 4]; // 什么时候进入老年代决定于-XX:MaxTenuringThreshold的设置
        allocation2 = new byte[4 * _1MB];
        allocation3 = new byte[4 * _1MB];
        allocation3 = null;
        allocation3 = new byte[4 * _1MB];
    }
```

```
[0.051s][info][gc] Using Serial
[0.053s][info][gc,heap,coops] Heap address: 0x00000000fec00000, size: 20 MB, Compressed Oops mode: 32-bit
[0.329s][info][gc,start     ] GC(0) Pause Young (Allocation Failure)
[0.334s][debug][gc,age       ] GC(0) Desired survivor size 524288 bytes, new threshold 1 (max threshold 1)
[0.334s][trace][gc,age       ] GC(0) Age table with threshold 1 (max threshold 1)
[0.334s][trace][gc,age       ] GC(0) - age   1:    1048576 bytes,    1048576 total
[0.334s][info ][gc,heap      ] GC(0) DefNew: 6375K->1024K(9216K)
[0.334s][info ][gc,heap      ] GC(0) Tenured: 0K->4251K(10240K)
[0.334s][info ][gc,metaspace ] GC(0) Metaspace: 629K->629K(1056768K)
[0.334s][info ][gc           ] GC(0) Pause Young (Allocation Failure) 6M->5M(19M) 4.293ms
[0.334s][info ][gc,cpu       ] GC(0) User=0.00s Sys=0.00s Real=0.00s
[0.335s][info ][gc,start     ] GC(1) Pause Young (Allocation Failure)
[0.341s][debug][gc,age       ] GC(1) Desired survivor size 524288 bytes, new threshold 1 (max threshold 1)
[0.341s][trace][gc,age       ] GC(1) Age table with threshold 1 (max threshold 1)
[0.341s][trace][gc,age       ] GC(1) - age   1:        824 bytes,        824 total
[0.341s][info ][gc,heap      ] GC(1) DefNew: 5201K->0K(9216K)
[0.341s][info ][gc,heap      ] GC(1) Tenured: 4251K->5275K(10240K)
[0.341s][info ][gc,metaspace ] GC(1) Metaspace: 630K->630K(1056768K)
[0.341s][info ][gc           ] GC(1) Pause Young (Allocation Failure) 9M->5M(19M) 6.012ms
[0.341s][info ][gc,cpu       ] GC(1) User=0.02s Sys=0.00s Real=0.01s
[0.342s][info ][gc,heap,exit ] Heap
[0.342s][info ][gc,heap,exit ]  def new generation   total 9216K, used 4390K [0x00000000fec00000, 0x00000000ff600000, 0x00000000ff600000)
[0.342s][info ][gc,heap,exit ]   eden space 8192K,  53% used [0x00000000fec00000, 0x00000000ff0495e0, 0x00000000ff400000)
[0.342s][info ][gc,heap,exit ]   from space 1024K,   0% used [0x00000000ff400000, 0x00000000ff400338, 0x00000000ff500000)
[0.342s][info ][gc,heap,exit ]   to   space 1024K,   0% used [0x00000000ff500000, 0x00000000ff500000, 0x00000000ff600000)
[0.342s][info ][gc,heap,exit ]  tenured generation   total 10240K, used 5275K [0x00000000ff600000, 0x0000000100000000, 0x0000000100000000)
[0.342s][info ][gc,heap,exit ]    the space 10240K,  51% used [0x00000000ff600000, 0x00000000ffb26d40, 0x00000000ffb26e00, 0x0000000100000000)
[0.342s][info ][gc,heap,exit ]  Metaspace       used 650K, capacity 4535K, committed 4864K, reserved 1056768K
[0.342s][info ][gc,heap,exit ]   class space    used 59K, capacity 402K, committed 512K, reserved 1048576K
```

### 3.8.4 动态对象年龄判定
- HotSpot虚拟机不是永远要求对象的年龄必须达到-XX:MaxTenuringThreshold才能晋升老年代。如果在Survivor中相同年龄所有对象大小的综合大于Survivor空间的一半，年龄大于或等于该年龄的对象就可以直接进入老年代。

```java
    /**
     * 动态对象年龄判定
     * VM参数：-XX:+UseSerialGC -Xms20M -Xmx20M -Xmn10M -Xlog:gc* -XX:SurvivorRatio=8
     * -XX:MaxTenuringThreshold=15 -Xlog:gc+age=trace
     */
    @SuppressWarnings("unused")
    public static void testTenuringThreshold2() {
        byte[] allocation1, allocation2, allocation3, allocation4;
        allocation1 = new byte[_1MB / 4]; // allocation1+allocation2大于survivor空间一半
        allocation2 = new byte[_1MB / 4];
        allocation3 = new byte[4 * _1MB];
        allocation4 = new byte[4 * _1MB];
        allocation4 = null;
        allocation4 = new byte[4 * _1MB];
    }
```

```
[0.028s][info][gc] Using Serial
[0.028s][info][gc,heap,coops] Heap address: 0x00000000fec00000, size: 20 MB, Compressed Oops mode: 32-bit
[0.209s][info][gc,start     ] GC(0) Pause Young (Allocation Failure)
[0.215s][debug][gc,age       ] GC(0) Desired survivor size 524288 bytes, new threshold 1 (max threshold 15)
[0.215s][trace][gc,age       ] GC(0) Age table with threshold 1 (max threshold 15)
[0.215s][trace][gc,age       ] GC(0) - age   1:    1048576 bytes,    1048576 total
[0.216s][info ][gc,heap      ] GC(0) DefNew: 6630K->1024K(9216K)
[0.216s][info ][gc,heap      ] GC(0) Tenured: 0K->4507K(10240K)
[0.216s][info ][gc,metaspace ] GC(0) Metaspace: 631K->631K(1056768K)
[0.216s][info ][gc           ] GC(0) Pause Young (Allocation Failure) 6M->5M(19M) 6.732ms
[0.216s][info ][gc,cpu       ] GC(0) User=0.00s Sys=0.00s Real=0.01s
[0.216s][info ][gc,start     ] GC(1) Pause Young (Allocation Failure)
[0.218s][debug][gc,age       ] GC(1) Desired survivor size 524288 bytes, new threshold 15 (max threshold 15)
[0.218s][trace][gc,age       ] GC(1) Age table with threshold 15 (max threshold 15)
[0.218s][trace][gc,age       ] GC(1) - age   1:       2368 bytes,       2368 total
[0.218s][info ][gc,heap      ] GC(1) DefNew: 5201K->2K(9216K)
[0.218s][info ][gc,heap      ] GC(1) Tenured: 4507K->5531K(10240K)
[0.218s][info ][gc,metaspace ] GC(1) Metaspace: 631K->631K(1056768K)
[0.218s][info ][gc           ] GC(1) Pause Young (Allocation Failure) 9M->5M(19M) 2.129ms
[0.218s][info ][gc,cpu       ] GC(1) User=0.02s Sys=0.00s Real=0.00s
[0.221s][info ][gc,heap,exit ] Heap
[0.221s][info ][gc,heap,exit ]  def new generation   total 9216K, used 4392K [0x00000000fec00000, 0x00000000ff600000, 0x00000000ff600000)
[0.221s][info ][gc,heap,exit ]   eden space 8192K,  53% used [0x00000000fec00000, 0x00000000ff0496d8, 0x00000000ff400000)
[0.221s][info ][gc,heap,exit ]   from space 1024K,   0% used [0x00000000ff400000, 0x00000000ff400940, 0x00000000ff500000)
[0.221s][info ][gc,heap,exit ]   to   space 1024K,   0% used [0x00000000ff500000, 0x00000000ff500000, 0x00000000ff600000)
[0.221s][info ][gc,heap,exit ]  tenured generation   total 10240K, used 5531K [0x00000000ff600000, 0x0000000100000000, 0x0000000100000000)
[0.221s][info ][gc,heap,exit ]    the space 10240K,  54% used [0x00000000ff600000, 0x00000000ffb66d58, 0x00000000ffb66e00, 0x0000000100000000)
[0.222s][info ][gc,heap,exit ]  Metaspace       used 651K, capacity 4535K, committed 4864K, reserved 1056768K
[0.222s][info ][gc,heap,exit ]   class space    used 59K, capacity 402K, committed 512K, reserved 1048576K
```

### 3.8.5 空间分配担保
- JDK 6 Update 24之前在发生Minor GC之前，虚拟机必须先检查老年代最大可用的连续空间是否大于新生代所有对象总空间。

> 1. 如果这个条件成立，那这一次Minor GC可以确保是安全的。
> 2. 否则，虚拟机会查看-XX:-HandlePromotionFailure参数的设置值是否允许担保失败。
>> 如果允许，那会继续检查老年代最大可用连续空间是否大于历次晋升到老年代对象的平均大小。如果大于，那将尝试进行一次Minor GC，尽管这次Minor GC是有风险的；如果小于，那么就要进行一次Full GC。  
>> 如果不允许，那么就要进行一次Full GC。

- JDK 6 Update 24之后的规则变为：只要老年代的连续空间大于新生代对象总大小或者历次晋升的平均大小，就会进行Minor GC，否则将进行Full GC。

# 第4章 虚拟机性能监控、故障处理工具*

# 第5章 调优案例分析与实战*

# 第6章 类文件结构
## 6.1 概述
- 越来越多的程序语言选择了与操作系统和机器指令集无关的、平台中立的格式作为程序编译后的存储格式。

## 6.2 无关性的基石
- 各种不同平台的Java虚拟机，以及所有平台都统一支持的程序存储格式——**字节码**（Byte Code）是构成平台无关性的基石。
- 实现语言无关性的基础仍然是**虚拟机**和**字节码存储格式**。
- Class文件包含了Java虚拟机指令集、符号表以及若干其他辅助信息。Class文件的来源不仅仅只有Java语言。

![image](/assets/img/post_img/ClassFile.png)

## 6.3 Class类文件结构
- Java技术能够一直保持着非常良好的向后兼容性，Class文件结构的稳定功不可没。
- Class文件是一组以**8个字节**为基础单位的二进制流，各个数据项目严格按照顺序紧凑地排列在文件之中，中间没有添加任何分隔符。
- Class文件格式采用一种类似于C语言结构体的**伪结构**来存储数据，这种伪结构中只有两种数据类型：“无符号数”和“表”。

> 1. 无符号数属于基本的数据类型，以u1、u2、u4、u8来分别代表1个字节、2个字节、4个字节和8个字节的无符号数，无符号数可以用来描述数字、索引引用、数量值或者按照UTF-8编码构成字符串值。
> 2. 表是由多个无符号数或者其他表作为数据项构成的复合数据类型，为了便于区分，所有表的命名都习惯性地以“_info”结尾。表用于描述由层关系的符合结构的数据，整个Class文件本质上也可以视作是一张表。

### 6.3.1 魔数与Class文件版本
- 每个Class文件的头4个字节被称为**魔数**（Magic Number），它的唯一作用是确定这个文件是否为一个能被虚拟机接受的Class文件。
- Class文件的魔术取得很有“浪漫气息”，值为**0xCAFEBABE**。
- 紧接着魔数的4个字节存储的是Class文件的**版本号**：第5和第6个字节是**次版本号**（Minor Version），第7和第8个字节是**主版本号**（Major Version）。
- Java的版本号是从**45**开始的，JDK 1.1之后的每个JDK大版本发布主版本号向上加1，如JDK 13对应57.0。
- 具体例子

```java
// TestClass.java
package org.fenixsoft.clazz;

public class 
{

	private int m;
	
	public int inc() {
		return m + 5;
	}
}
```

> 编译上述TestClass.java文件生成TestClass.class，并使用十六进制编辑器WinHex打开Class文件。

![image](/assets/img/post_img/ClassWinHex1.png)

> 如图，开头4个字节的十六进制表示是0xCAFEBABE，代表次版本号的第5个和第6个字节值为0x0000，而主版本号的值为0x0039，也即是十进制的57，代表该JDK版本号是13。

### 6.3.2 常量池
- 紧接着主、次版本号之后的是**常量池入口**，常量池可以比喻为Class文件里的资源仓库，它是Class文件结构中与其他项目关联最多的数据，通常也是占用Class文件空间最大的数据项目之一，另外，它还是在Class文件中第一个出现的表类型数据项目。
- 由于常量池中常量的数量是不固定的，所以在常量池入口需要放置一项u2类型的数据，代表**常量池容量计数值**（constant_pool_count）。**这个容量计数是从1而不是从0开始的**。

![image](/assets/img/post_img/ClassWinHex2.png)

> 如图，常量池容量为0x0013，即十进制的19，这就代表常量池中有18项常量，索引值范围为1~18。

- 常量池中主要存放两大类常量：**字面量**（Literal）和**符号引用**（Symbolic References）。字面量比较接近于Java语言层面的常量概念，如文本字符串、被声明为final的常量值等。而符号引用则属于编译原理方面的概念，主要包括以下几类常量：

> 1. 被模块到处或者开放的包
> 2. 类和接口的全限定名
> 3. 字段的名称和描述符
> 4. 方法的名称和描述符
> 5. 方法句柄和方法类型
> 6. 动态调用点和动态常量

- Java代码在进行Javac编译的时候，并不像C和C++那样有“连接”这一步骤，而是在虚拟机加载Class文件的时候进行动态连接。也就是说，在Class文件中不会保存各个方法、字段最终在内存中的布局信息，这些字段、方法的符号引用不经过虚拟机在运行期转换的话是无法得到真正的内存入口地址，也就无法直接被虚拟机使用的。
- 常量池中每一项常量都有一个表，截至JDK 13，常量表中有有17中不同的常量。

<table>
  <thead>
    <tr>
      <th>类型</th>
      <th>标志</th>
      <th>描述</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>CONSTANT_Utf8_info</td>
      <td>1</td>
      <td>UTF-8编码的字符串</td>
    </tr>
    <tr>
      <td>CONSTANT_Integer_info</td>
      <td>3</td>
      <td>整型字面量</td>
    </tr>
    <tr>
      <td>CONSTANT_Float_info</td>
      <td>4</td>
      <td>浮点型字面量</td>
    </tr>
    <tr>
      <td>CONSTANT_Long_info</td>
      <td>5</td>
      <td>长整型字面量</td>
    </tr>
        <tr>
      <td>CONSTANT_Double_info</td>
      <td>6</td>
      <td>双精度浮点型字面量</td>
    </tr>
    <tr>
      <td>CONSTANT_Class_info</td>
      <td>7</td>
      <td>类或接口的符号引用</td>
    </tr>
    <tr>
      <td>CONSTANT_String_info</td>
      <td>8</td>
      <td>字符串类型字面量</td>
    </tr>
    <tr>
      <td>CONSTANT_Filedref_info</td>
      <td>9</td>
      <td>字段的符号引用</td>
    </tr>
    <tr>
      <td>CONSTANT_Methodref_info</td>
      <td>10</td>
      <td>类中方法的符号引用</td>
    </tr>
    <tr>
      <td>CONSTANT_InterfaceMethodref_info</td>
      <td>11</td>
      <td>接口中方法的符号引用</td>
    </tr>
    <tr>
      <td>CONSTANT_NameAndType_info</td>
      <td>12</td>
      <td>字段或方法的部分符号引用</td>
    </tr>
    <tr>
      <td>CONSTANT_MethodHandle_info</td>
      <td>15</td>
      <td>表示方法句柄</td>
    </tr>
    <tr>
      <td>CONSTANT_MethodType_info</td>
      <td>16</td>
      <td>表示方法类型</td>
    </tr>
    <tr>
      <td>CONSTANT_Dynamic_info</td>
      <td>17</td>
      <td>表示一个动态计算常量</td>
    </tr>
    <tr>
      <td>CONSTANT_InvokeDynamic_info</td>
      <td>18</td>
      <td>表示一个动态方法调用点</td>
    </tr>
    <tr>
      <td>CONSTANT_Module_info</td>
      <td>19</td>
      <td>表示一个模块</td>
    </tr>
    <tr>
      <td>CONSTANT_Package_info</td>
      <td>20</td>
      <td>表示一个模块中开放或者导出的包</td>
    </tr>
  </tbody>
</table>

- 使用javap命令输出常量表

```java
javap -verbose TestClass  
Compiled from "TestClass.java"  
public class org.fenixsoft.clazz.TestClass  
  minor version: 0  
  major version: 57  
  flags: (0x0021) ACC_PUBLIC, ACC_SUPER  
  this_class: #8                          // org/fenixsoft/clazz/TestClass  
  super_class: #2                         // java/lang/Object  
  interfaces: 0, fields: 1, methods: 2, attributes: 1  
Constant pool:  
   #1 = Methodref          #2.#3          // java/lang/Object."<init>":()V  
   #2 = Class              #4             // java/lang/Object  
   #3 = NameAndType        #5:#6          // "<init>":()V  
   #4 = Utf8               java/lang/Object  
   #5 = Utf8               <init>  
   #6 = Utf8               ()V  
   #7 = Fieldref           #8.#9          // org/fenixsoft/clazz/TestClass.m:I  
   #8 = Class              #10            // org/fenixsoft/clazz/TestClass  
   #9 = NameAndType        #11:#12        // m:I  
  #10 = Utf8               org/fenixsoft/clazz/TestClass  
  #11 = Utf8               m  
  #12 = Utf8               I  
  #13 = Utf8               Code  
  #14 = Utf8               LineNumberTable  
  #15 = Utf8               inc  
  #16 = Utf8               ()I  
  #17 = Utf8               SourceFile  
  #18 = Utf8               TestClass.java  
```

### 6.3.3 访问标志
- 在常量池结束之后，紧接着的2个字节代表**访问标志**（access_flags），这个标志用于识别一些类或者接口层次的访问信息，包括：这个Class是类还是接口；是否定义为public类型；是否定义为abstract类型；如果是类的话，是否被声明为final；等等。

<table>
  <thead>
    <tr>
      <th>标志名称</th>
      <th>标志值</th>
      <th>含义</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>ACC_PUBLIC</td>
      <td>0x0001</td>
      <td>是否为public类型</td>
    </tr>
    <tr>
      <td>ACC_FINAL</td>
      <td>0x0010</td>
      <td>是否被声明为fianl，只有类可设置</td>
    </tr>
    <tr>
      <td>ACC_SUPER</td>
      <td>0x0020</td>
      <td>是否允许使用invokespecial字节码指令的新语义，<br>JDK 1.0.2之后都为真</td>
    </tr>
    <tr>
      <td>ACC_INTERFACE</td>
      <td>0x0200</td>
      <td>标识这是一个接口</td>
    </tr>
        <tr>
      <td>ACC_ABSTRACT</td>
      <td>0x0400</td>
      <td>是否为abstract类型，<br>接口和抽象类为真，其他为假</td>
    </tr>
    <tr>
      <td>ACC_SYNTHETIC</td>
      <td>0x1000</td>
      <td>标识这个类并非由用户代码生成</td>
    </tr>
    <tr>
      <td>ACC_ANNOTATION</td>
      <td>0x2000</td>
      <td>标识这是一个注解</td>
    </tr>
    <tr>
      <td>ACC_ENUM</td>
      <td>0x4000</td>
      <td>标识这是一个枚举</td>
    </tr>
    <tr>
      <td>ACC_MODULE</td>
      <td>0x8000</td>
      <td>标识这是一个模块</td>
    </tr>
  </tbody>
</table>

- access_flags中一共有16个标志位可以使用，当前之定义了其中9个，没有使用到的标识位要求一律为0。

![image](/assets/img/post_img/ClassWinHex3.png)

> 如图，前例中，TestClass是一个普通的Java类，它的ACC_PUBLIC、ACC_SUPER标识位为真，而其他7个标识位为假，因此它的access_flags的值为：0x0001|0x020 = 0x0021。即图中0x0021。

### 6.3.4 类索引、父类索引与接口索引集合
- **类索引**（this_class）和**父类索引**（super_class）都是一个u2类型的数据，而**接口索引集合**（interfaces）是一组u2类型的数据的集合，Class文件由这三项数据来确定该类型的继承关系。
- 类索引用于确定这个类的全限定名，父类索引用于确定这个类的父类的全限定名。

> 除了java.lang.Object之外，所有的Java类都有父类，因此除了java.lang.Object外，所有Java类的父类索引都不为0。

- 接口索引集合就用来描述这个类实现了哪些接口，这些被实现的接口将按implements关键字（若该Class文件表示的是一个接口，则应当是extends关键字）后的接口顺序从左到右排列在接口索引集合中。
- 类索引、父类索引各用两个u2类型的索引值表示。而接口索引集合，入口的第一项u2数据类型为接口计数器（interfaces_count），表示索引表的容量。

![image](/assets/img/post_img/ClassWinHex4.png)

> 如图，0x0008、0x0002、0x0000分别表示类索引为8，父类索引为2，接口索引集合大小为0。对照前面javap命令计算出来的常量池，可以找到对应的类和父类的常量。

```java
  #2 = Class              #4             // java/lang/Object
  #8 = Class              #10            // org/fenixsoft/clazz/TestClass
```

### 6.3.5 字段表集合
- **字段表**（field_info）用于描述接口或者类中声明的变量。
- Java语言中的“字段”包括类级变量以及实例级变量，但不包括在方法内部声明的局部变量。
- 字段表结构：

<table>
  <thead>
    <tr>
      <th>类型</th>
      <th>名称</th>
      <th>数量</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>u2</td>
      <td>access_flags</td>
      <td>1</td>
    </tr>
    <tr>
      <td>u2</td>
      <td>name_index</td>
      <td>1</td>
    </tr>
    <tr>
      <td>u2</td>
      <td>descriptor_index</td>
      <td>1</td>
    </tr>
    <tr>
      <td>u2</td>
      <td>attributes_count</td>
      <td>1</td>
    </tr>
        <tr>
      <td>attribute_info</td>
      <td>attributes</td>
      <td>attributes_count</td>
    </tr>
  </tbody>
</table>

- 字段访问标识：

<table>
  <thead>
    <tr>
      <th>标志名称</th>
      <th>标志值</th>
      <th>含义</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>ACC_PUBLIC</td>
      <td>0x0001</td>
      <td>字段是否public</td>
    </tr>
    <tr>
      <td>ACC_PRIVATE</td>
      <td>0x0002</td>
      <td>字段是否private</td>
    </tr>
    <tr>
      <td>ACC_PROTECTED</td>
      <td>0x0004</td>
      <td>字段是否protected</td>
    </tr>
    <tr>
      <td>ACC_STATIC</td>
      <td>0x0008</td>
      <td>字段是否static</td>
    </tr>
        <tr>
      <td>ACC_FINAL</td>
      <td>0x0010</td>
      <td>字段是否final</td>
    </tr>
    <tr>
      <td>ACC_VOLATILE</td>
      <td>0x0040</td>
      <td>字段是否volatile</td>
    </tr>
    <tr>
      <td>ACC_TRANSIENT</td>
      <td>0x0080</td>
      <td>字段是否transient</td>
    </tr>
    <tr>
      <td>ACC_SYNTHETIC</td>
      <td>0x1000</td>
      <td>字段是否由编译器自动产生</td>
    </tr>
    <tr>
      <td>ACC_ENUM</td>
      <td>0x4000</td>
      <td>字段是否enum</td>
    </tr>
  </tbody>
</table>

- **全限定名**：“org/fenixsoft/clazz/TestClass”即是这个类的全限定名。
- **简单名称**：即是指没有类型和参数修饰的方法或者字段名称，这个类中的inc()方法和m字段的简单名称分别就是“inc”和“m”。
- **描述符**的作用是描述字段的数据类型、方法的参数列表（包括数量、类型以及顺序）和返回值。
- 基本数据类型以及代表无返回值的void类型都用一个大写字母来表示，而对象类型则用字符L加对象的全限定名来表示。

<table>
  <thead>
    <tr>
      <th>标识字符</th>
      <th>含义</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>B</td>
      <td>基本类型byte</td>
    </tr>
    <tr>
      <td>C</td>
      <td>基本类型char</td>
    </tr>
    <tr>
      <td>D</td>
      <td>基本类型double</td>
    </tr>
    <tr>
      <td>F</td>
      <td>基本类型float</td>
    </tr>
        <tr>
      <td>I</td>
      <td>基本类型int</td>
    </tr>
    <tr>
      <td>J</td>
      <td>基本类型long</td>
    </tr>
    <tr>
      <td>S</td>
      <td>基本类型short</td>
    </tr>
    <tr>
      <td>Z</td>
      <td>基本类型boolean</td>
    </tr>
    <tr>
      <td>V</td>
      <td>特殊类型void</td>
    </tr>
    <tr>
      <td>L</td>
      <td>对象类型，如Ljava/lang/Object;</td>
    </tr>
  </tbody>
</table>

- 对于数组类型，每一维度将使用一个前置的“[”字符来描述。

> 如一个定义为“java.lang.String[][]”类型的二维数组将被记录城“[[Ljava/lang/String;”，一个整型数组“int[]”将被记录成“[I”。

- 用描述符来描述方法时，按照先参数列表、后返回值的顺序描述，参数列表按照参数的严格顺序放在一组小括号“()”之内。

> 如方法void inc()的描述符为“()V”，  
方法java.lang.String toString()的描述符为“()Ljava/lang/String;”，  
方法int indexOf(char[] source, int sourceOffset, int sourceCount, char[] target, int targetOffset, int targetCount, int fromIndex)的描述符为“([CII[CII)I”。

![image](/assets/img/post_img/ClassWinHex5.png)

> 如图，第一个u2类型的数据为容量计数器fields_count，其值为0x0001，说明这个类只有一个字段表数据。接下来是access_flags标志，值为0x0002，代表private修饰符的ACC_PRIVATE标志位为真。接下来是代表字段名称的name_index，值为0x000B（十进制的11）对应m，接下来是代表字段描述的descriptor_index，值为0x000C（十进制的12）对应I。根据以上信息，可以推断出原代码定义的字段为“private int m”。

```java
  #11 = Utf8               m
  #12 = Utf8               I
```

### 6.3.6 方法表集合

- 方法表结构：

<table>
  <thead>
    <tr>
      <th>类型</th>
      <th>名称</th>
      <th>数量</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>u2</td>
      <td>access_flags</td>
      <td>1</td>
    </tr>
    <tr>
      <td>u2</td>
      <td>name_index</td>
      <td>1</td>
    </tr>
    <tr>
      <td>u2</td>
      <td>descriptor_index</td>
      <td>1</td>
    </tr>
    <tr>
      <td>u2</td>
      <td>attributes_count</td>
      <td>1</td>
    </tr>
        <tr>
      <td>attribute_info</td>
      <td>attributes</td>
      <td>attributes_count</td>
    </tr>
  </tbody>
</table>

- 方法访问标志：

<table>
  <thead>
    <tr>
      <th>标志名称</th>
      <th>标志值</th>
      <th>含义</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>ACC_PUBLIC</td>
      <td>0x0001</td>
      <td>方法是否为public</td>
    </tr>
    <tr>
      <td>ACC_PRIVATE</td>
      <td>0x0002</td>
      <td>方法是否为private</td>
    </tr>
    <tr>
      <td>ACC_PROTECTED</td>
      <td>0x0004</td>
      <td>方法是否为protected</td>
    </tr>
    <tr>
      <td>ACC_STATIC</td>
      <td>0x0008</td>
      <td>方法是否为static</td>
    </tr>
    <tr>
      <td>ACC_FINAL</td>
      <td>0x0010</td>
      <td>方法是否为final</td>
    </tr>
    <tr>
      <td>ACC_SYNCHRONIZED</td>
      <td>0x0020</td>
      <td>方法是否为synchronized</td>
    </tr>
    <tr>
      <td>ACC_BRIDGE</td>
      <td>0x0040</td>
      <td>方法是不是由编译器产生的桥接方法</td>
    </tr>
    <tr>
      <td>ACC_VARARGS</td>
      <td>0x0080</td>
      <td>方法是否接受不定参数</td>
    </tr>
    <tr>
      <td>ACC_NATIVE</td>
      <td>0x0100</td>
      <td>方法是否为native</td>
    </tr>
    <tr>
      <td>ACC_ABSTRACT</td>
      <td>0x0400</td>
      <td>方法是否为abstract</td>
    </tr>
    <tr>
      <td>ACC_STRICT</td>
      <td>0x0800</td>
      <td>方法是否为strictfp</td>
    </tr>
    <tr>
      <td>ACC_SYNTHETIC</td>
      <td>0x1000</td>
      <td>方法是否由编译器自动产生</td>
    </tr>
  </tbody>
</table>

- 方法里的Java代码，经过Javac编译器编译成字节码指令之后，存放在方法属性表集合中一个名为“Code”的属性里面，属性表作为Class文件格式中最具扩展性的一种数据项目。

![image](/assets/img/post_img/ClassWinHex6.png)

> 如图，第一个u2类型的数据（即计数器容量）的值为0x0002，代表集合中有两个方法，这两个方法为编译器添加的实例构造器<init>和源码中定义的方法inc()。第一个方法的访问标志值为0x0001，也就是只有ACC_PUBLIC标志为真，名称索引值为0x0005，对应常量池中“<init>”，描述索引值为0x0006，对应常量池中“()V”，属性表计数器attributes_count的值为0x0001，表示此方法的属性表集合有1项属性，属性名称的索引值为0x000D（十进制的13），对应常量池中“Code”，说明此属性是方法的字节码描述。
