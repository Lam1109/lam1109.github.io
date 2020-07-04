---
date: 2020-06-23 14:23:40
layout: post
title: Core Java™, Volume Ⅱ-Advanced Features
subtitle: (Eleventh Edition) Book Notes
description: The book notes of Core Java™, Volume Ⅱ-Advanced Features.
image: /assets/img/post_img/corejava2.png
optimized_image: /assets/img/post_img/java2.png
category: java
tags:
  - java
  - dvanced features.
author: Lam
---

# 第1章 Java 8的流库
> 从迭代到流的操作  
  流的创建  
  filter、map和flatMap方法  
  并行流  
  简单约简
  
- 通过使用流，可以说明想要完成什么任务，而不是说明如何去实现它。

## 1.1 从迭代到流的操作
- 流表面上看起来和集合很类似，都可以让我们转换和获取数据。但是，它们之间存在着显著的差异：

> 1. 流并不存储元素。这些元素可能存储在底层的集合中，或者按需生成。
> 2. 流的操作不会修改其数据源。
> 3. 流的操作是尽可能惰性执行的。这意味着直至需要其结果时，操作才会执行。

```java
// 计数：字符串列表words中所有长度大于12的单词

    long count = 0;

// 1. 常规方式
    for (String w : words) {
        if (w.length() > 12) 
            count++;
    }

// 2. 流
    count = words.stream().filter(w -> w.length() > 5).count();

// 3. 并行流
    count = words.parallelStream().filter(w -> w.length() > 5).count();
```

## 1.2 流的创建
- **Collection**接口的**stream**方法可以将任何集合转换为一个流。而数组则使用静态的**Stream.of**方法。

```java
Stream<String> words = Stream.of(contents.split("\\PL+")); // split("\\PL+") 以非字母（\\PL+）为分隔

// of 方法句有可变长参数
Stream<String> words = Stream.of("gently", "down", "the", "stream");
```

- **Array.stream(array,from,to)** 可以用数组中的一部分（form到to）来创建一个流。
- 静态的**Stream.empty**方法可以创建不包含任何元素的流。

```java
Stream<String> silence = Stream.empty();
// Generic type <String> is inferred; same as Stream.<String>empty()
```

- 一些流相关的方法

```java
// 获得一个常量值的流
Stream<String> echos = Stream.generate(() -> "Echo");

// 获取一个随机数的流
Stream<Double> randoms = Stream.generate(Math::random);

/** iterate方法 
  * 会接受一个“种子”值，
  * 以及一个函数，
  * 并且会反复地将该函数应用到之前的结果上。
  * 下例会生成一个 0 1 2 3...这样的序列。
  */
Stream<BigInteger> integers 
    = Stream.iterate(BigInteger.ZERO, n -> n.add(BigInteger.ONE)

/** 生成一个有限序列
  * 添加一个谓词描述迭代应该何时结束。
  */
var limit = new BigInteger("1000000");
Stream<BigInteger> integers 
    = Stream.iterate(BigInteger.ZERO, 
    n -> n.compareTo(limit) < 0, 
    n -> n.add(BigInteger.ONE)
    
/** Pattern类的splitAsStream方法
  * 按照某个正则表达式来分割一个CharSequence对象。
  */
Stream<String> words = Pattern.compile("\\PL+").splitAsStream(contents);

/** Scanner.tokens方法
  * 会产生一个扫描器的符号流。
  */
Stream<String> words = new Scanner(contents).tokens();

/** 静态Files.lines方法
  * 会返回一个包含了文件中所有行的Stream。
try (Stream<String> lines = Files.lines(path)) {
    Process lines
}

/** 若持有的Iterable对象不是集合，
  * 则可以通过下面的方法将其转换为一个流。
  */
StreamSupport.stream(iterable.spliterator(), false);

/** 若持有的是Iterator对象，
  * 得到一个由它结果构成的流。
  */
StreamSupport.stream(Spliterators.spliteratorUnknownSize(
    iterator, Spliterator.ORDERED), false);
```

## 1.3 filter、map和flatMap方法
- filter方法会产生一个新流，它的元素与某种条件相匹配。

```java
// 将一个字符串流转换为只包含长单词（长度大于12）的新流
List<String> words = ...;
Stream<String> longWords = words.stream().filter(w -> w.length() > 12);
```

- map方法传递执行转换函数。

```java
// 函数 将所有单词转换成小写
Stream<String> lowercaseWords = words.stream().map(String::toLowerCase);
// lambda表达式 新流中包含所有单词的首字母
Stream<String> lowercaseWords = words.stream().map(s -> s.substring(0,1));
```

- flatMap方法。

```java
public static Stream<String> codePoints(String s) {
    var result = new ArrayList<String>();
    int i= 0;
    while (i < s.length()) {
        int j = s.offsetByCodePoints(i, 1);
        result.add(s.substring(i, j));
        i = j;
    }
    return result.stream();
}
```

```java
/** codePoints("boat")的返回值是流["b", "o", "a", "t"]
  * 将codePoints方法映射到一个字符串流上
  */
Stream<Stream<String>> result = words.stream().map(w -> codePoints(w));
/** 上述代码会得到一个包含流的流
  * [...["y", "o", "u", "r"],["b", "o", "a", "t"],...]
  * 为了将其摊平为单个流
  * [..."y", "o", "u", "r", "b", "o", "a", "t",...]
  * 使用flatMap方法而不是map方法
  */
Stream<String> flatResult = words.stream().flatMap(w -> codePoints(w));

```

## 1.4 抽取子流和组合流
- 调用stream.limit(n)会返回一个新的流，它在n个元素之后结束（若原来的流比n短，则在该流结束时结束）。

```java
// 产生一个包含100个随机数的流
Stream<Double> randoms = Stream.generate(Math::random).limit(100);
```
- 调用stream.skip(n)正好相反，它会丢弃（跳过）前n个元素。

```java
// 跳过第一个元素
Stream<String> words = Stream.of(contents.split("\\PL+")).skip(1);
```

- stream.takeWhile(predicate)调用会在谓词为真时获取流中的所有元素，然后停止。

```java
/** 上文codePoints方法将字符串分割为字符，然后收集所有的数字元素。
  * takeWhile方法可以实现此目标。
  */
Stream<String> initialDigits = codePoints(str).takeWhile(s -> "0123456789".contains(s));
```

- dropWhile方法正好相反，它会在条件为真时丢弃元素，并产生一个由**第一个使该条件为假**的字符开始的所有元素构成的流。

```java
Stream<String> withoutInitialWhiteSpace 
    = codePoints(str).dropWhile(s -> s.trim().length() == 0);

- Stream类的静态concat方法可以将两个流连接起来
Stream<String> combined = Stream.concat(codePoints("Hello"), codePoints("World");
// Yields the stream ["H", "e", "l", "l", "o", "W", "o", "r", "l", "d"]
```

## 1.5 其他流的转换
- distinct方法会返回一个流，它的元素是从原有流中产生的，即原来的元素按照同样的顺序剔除重复元素后产生的。

```java
Stream<String> uniqueWords = Stream.of("merrily", "merrily", "merrily", "gently").distinct();
// only one "merrily" is retained
```
- 流的排序有多种sorted方法的变体可用。其中一种用于操作Comparable元素的流，而另一种可以接受一个Comparator。

```java
// 使最长的字符串排在最前面
Stream<String> longestFirst = words.stream().sorted(Comparator.comparing(String::length).reversed());
```
> 与所有流转换一样，sorted方法会产生一个新的流，它的元素是原有流中按照顺序排列的元素。

- peek方法会产生另一个流，它的元素与原来流中的元素相同，但是在每次获取一个元素时，都会调用一个函数。

```java
Object[] powers 
    = Stream.iterate(1.0, p -> p * 2).peek(e -> System.out.println("Fetching " + e)).limit(20).toArray();
```

## 1.6 简单约简
- **约简**是一种终结操作（terminal operation），它们会将流约简为可以在程序中使用的非流值。
- 简单约简：

```java
/** 1. count方法
  * 返回流中的元素的数量。
  */
Stream<String> example = Stream.of("merrily", "merrily", "merrily", "gently");
System.out.println(example.count()); // print 4

/** 2. max和min方法
  * 返回流中的元素的最大值和最小值。
  */
Optional<String> largest = words.max(String::compareToIgnoreCase);
System.out.println("largest: " + largest.orElse(""));

/** 3. findFirst方法
  * 返回非空集合中的第一个值。
  * 与filter组合使用时很有用。
  * 找到第一个以字母Q开头的单词。
  */
Optional<String> startsWithQ = words.filter(s -> startsWith("Q")).findFirst();

/** 4. findAny方法
  */
Optional<String> startsWithQ = words.filter(s -> startsWith("Q")).findAny();

/** 5. anyMatch方法
  * 检测是否存在匹配。
  * 接受一个断言引元，
  * 因此不需要使用filter。
  */
boolean aWordStartsWithQ = words.parallel().anyMatch(s -> startsWith("Q"));

/** 6. allMatch和noneMatch方法
  * 分别在所有元素
  * 和没有任何元素匹配谓词
  * 的情况下返回true。
  */
```

## 1.7 Optional类型
- Optional<T>对象是一种包装器对象，要么包装了类型T的对象，要么没有包装任何对象。

## 1.7.1 获取Optional值
- 有效地使用Optional的关键：它在值不存在的情况下会产生一个可替代物，而只有在值存在的情况下才会使用这个值。

```java
// 在没有任何匹配时，使用某种默认值，可能是空字符串。
String result = optionalString.orElse("");
    // The wrapped string, or "" if none

// 调用代码计算默认值
String result = optionalString.orElseGet(() -> System.getProperty("myapp.default"));
    // The function is only called when needed

// 或者在没有任何值时抛出异常
String result = optionalString.orElseThrow(IllegalStateException::new);
    // Supply a method that yield an exception object
```

## 1.7.2 消费Optional值
- ifPresent方法会接受一个函数

```java
/** 如果可选值存在，
  * 那么它会被传递给该函数。
  * 否则，不会发生任何事情。
  */
optionalValue.ifPresent(v -> Process v);
```

```java
/** 例如，如果在该值存在的情况下，
  * 要将其添加到某个集中，
  * 那么可以调用：
  */
optionalValue.ifPresent(v -> results.add(v)); // or  optionalValue.ifPresent(results::add);

/** ifPresentOrElse方法
  * 可选值存在时执行一种动作，
  * 可选值不存在时执行另一种动作。
  */
optionalValue.ifPresentOrElse(v -> System.out.println("Found " + v), () -> logger.warning("No match"));
```

## 1.7.3 管道化Optional值


