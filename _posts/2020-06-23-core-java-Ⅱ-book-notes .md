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

### 1.7.1 获取Optional值
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

### 1.7.2 消费Optional值
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

### 1.7.3 管道化Optional值

```java
// 使用map方法来转换Optional内部的值。
Optional<String> transformed = optionalString.map(String::toUpperCase);
// 如果optionalString为空，则transformed也为空

// 将一个结果添加到列表中
optionalValue.map(results::add);
// 如果optionalValue为空，则什么也不会发生
```

### 1.7.4 不适合使用Optional值的方式
- Optional类型正确用法的提示：
> 1. Optional类型的变量永远都不应该为null。
> 2. 不要使用Optional类型的域。因为其代价是额外多出来一个对象。在类的内部，使用null表示缺失的域更易于操作。
> 3. 不要在集合中放置Optional对象，并且不要将它们用作map的键。应该直接收集其中的值。

### 1.7.5 创建Optional值
- 若想要编写方法来创建Optional对象，可以使用Optional.of(result)和Optional.empty()。

```java
public static Optional<Double> inverse(Double x) {
    return x == 0 ? Optional.empty() : Optional.of(1/x);
}
```
- ofNullable方法被用来作为可能出现的null值和可选值之间的桥梁。Optional.ofNullable(obj)会在obj不为null的情况下返回Optional.of(obj)，否则会返回Optional.empty()。

### 1.7.6 用flatMap构建Optional值的函数

```java
/** f方法可以产生Optional<T>对象，
  * 目标类型T具有一个可以产生Optional<U>对象的方法g。
  * 则可通过s.f().g()将它们组合起来。
  * 但是这种组合无法工作，
  * 因为s.f()的类型为Optional<T>，而不是T。
  * 故需要使用flatMap方法。
  */
Optional<T> result = s.f().flatMap(T::g);
// 若s.f()的值存在，那么g就可以应用到它上面。否则，就会返回一个空Optional<U>。
```

### 1.7.7 将Optional转换为流
- stream方法会将一个Optional<T>对象转换为一个具有0个或1个元素的Stream<T>对象。

```java
/** 有一个用户ID流和
  * 方法Optional<User> lookup(String id)
  */
  
Stream<String> ids = ...;
Stream<User> users = ids.map(Users::lookup).filter(Optional::isPresent).map(Optional::get);

/** 过滤掉无效ID，
  * 然后将get方法应用于剩余的ID。
  * 但是要慎用isPresent和get方法，
  * 故修改代码。
  */

Stream<User> users = ids.map(Users::lookup).flatMap(Optional::stream);
```

## 1.8 收集结果
- forEach方法，将函数运用于每个元素。

```java
stream.forEach(System.out::println);
```
> 在并行流上，forEach方法会以任意顺序遍历各个元素。forEachOrdered方法可以按照流中的顺序来处理，但该方法会丧失并行处理的部分甚至全部优势。

- 表达式stream.toArray()会返回一个Object[]数组。将其传递到数组构造器中，能够让数组拥有正确的类型。

```java
String[] result = stream.toArray(String[]::new);
    // stream.toArray() has type Object[]
```

- 收集器是一种收集众多元素并产生单一结果的对象，Collectors类提供了大量用于生成常见收集器的工厂方法。

```java
// 将流的元素收集到一个列表中
List<String> result = stream.collect(Collectors.toList());

// 将流的元素收集到一个集中
Set<String> result = stream.collect(Collectors.toSet());

// 控制获得的集的种类
TreeSet<String> result = stream.collect(Collectors.toCollection(TreeSet::new));

// 通过连接操作来收集流中所有字符串
String result = stream.collect(Collectors.joining());

// 元素之间增加分隔符
String result = stream.collect(Collectors.joining(", "));

// 将流中非字符串对象转换为字符串
String result = stream.map(Object::toString).collect(Collectors.joining(", "));
```

## 1.9 收集到映射表中
- Collectors.toMap方法有两个函数引元，它们用来产生映射表的键和值。

```java
/** 将一个流Stream<Person>的元素收集到映射表中，
  * 以便后续通过ID来查找人员。
  */
Map<Integer, String> idToName = people.collect(Collectors.toMap(Person::getId, Person::getName));

// 通常情况下，值应该是实际的元素，因此第二个函数可以使用Function.identity()。
Map<Integer, String> idToPerson = people.collect(Collectors.toMap(Person::getId, Function.identity()));
```

## 1.10 群组和分区
- 将具有相同特性的值群聚成组是非常常见的，并且groupingBy方法直接就支持它。

```java
Map<String, List<Locale>> countryToLocales = locales.collect(Collectors.groupingBy(Locale::getCountry));

/** 函数Locale::getCountry是群组的分类函数，
  * 可以查找给定国家代码对应的所有地点。
  */
List<Locale> swissLocales = countryToLocales.get("CH");
    // Yields locales de_CH, fr_CH, it_CH and maybe more
```

- 当分类函数是断言函数（即返回boolean值的函数）时，流的袁术可以分为两个列表：该函数返回true的元素和其他的元素。这种情况下，使用partitioningBy比使用groupingBy更高效。

```java
// 将所有locale分成了使用英语和使用其他英语的两类
Map<Boolean, List<Locale>> englishAndOtherLocales 
    = locales.collect(Collectors.partitioningBy(l -> l.getLanguage.equals("en")));
List<Locale> englishLocales = englishAndOtherLocales.get(true);
```

## 1.11 下游收集器
- groupingBy方法会产生一个映射表，它的每个值都是一个列表。“下游处理器”能够以某种方式来处理这些列表。

```java
Map<String, Set<Locale>> countryToLocaleSet = locales.collect(groupingBy(Locale::getCountry, toSet()));
```

- Java提供了多种可以将收集到的元素约简为数字的收集器。

```java
/** 1. counting
  *会产生收集到的元素的个数。
  */
Map<String, Long> countryToLocaleCounts = locales.collect(groupingBy(Locale::getCountry, counting()));

/** 2. summing(Int|Long|Double)
  * 会接受一个函数作为引元，
  * 将该函数应用到下游元素中，
  * 并产生它们的和。
  */
Map<String, Integer> stateToCityPopulation 
    = cities.collect(groupingBy(City::getState, summingInt(City::getPopulation)));
    
/** 3. maxBy和minBy
  * 会接受一个比较器，
  * 并分别产生下游元素中的最大值和最小值。
  */
Map<String, Optional<City>> stateToLargestCity 
    = cities.collect(groupingBy(City::getState, maxBy(Comparator.comparing(City::getPopulation))));
    // 可以产生每个州中最大的城市。
```

## 1.12 约简操作
- **reduce**方法是一种用于从流中计算某个值的通用机制，其简单的形式将接受一个二元函数，并从前两个元素开始持续应用它。

```java
List<Integer> values = ...;
Optional<Integer> sum = values.stream().reduce((x, y) -> x + y);
    /** reduce方法会计算v0+v1+v2+...，其中vi是流中的元素。
      * 若流为空，则该方法会返回一个Optional。
      */
```

## 1.13 基本类型流

- 流库中具有专门的类型**IntStream**、**LongStream**和**DoubleStream**，用来直接存储基本类型值，而无须使用包装器。
- **IntStream**存储short、char、byte和boolean。
- **DoubleStream**存储float。
- 创建IntStream，需要调用**IntStream.of**和**Arrays.stream**方法：

```java
IntStream stream = IntStream.of(1, 1, 2, 3, 5);
stream = Arrays.stream(values, from, to); // values is an int[] array
```

- 基本类型流上的方法与对象流上的方法类似。下面是主要的差异：

> 1. toArray方法会返回基本类型数组。
> 2. 产生可选结果的方法会返回一个OptionalInt、OptionalLong或OptionalDouble。这些类与Optional类类似，但是具有getAsInt、getAsLong和getAsDouble方法，而不是get方法。
> 3. 具有分别返回总和、平均值、最大值和最小值的sum、average、max和min方法。对象流没有定义这些方法。
> 4. summaryStatistics方法会产生一个类型为IntSummaryStatistics、LongSummaryStatistics或DoubleSummaryStatistics对象，它们可以同时报告流的总和、数量、平均值、最大值和最小值。

## 1.14 并行流
- Collection.parallelStream()方法从任何集合中获取一个并行流：

```java
Stream<String> parallelWords = words.parallerStream();
```

- parallel方法可以将任意的顺序流转换为并行流：

```java
Stream<String> parallelWords = Stream.of(wordArray).parallel();
```

- 不要指望通过将所有的流都转换为并行流就能加速操作，要牢记：

> 1. 并行化会导致大量的开销，只有面对非常大的数据集才划算。
> 2. 只有在底层的数据源可以被有效地分割为多个部分时，将流并行化才有意义。
> 3. 并行流使用的线程池可能会因诸如文件I/O或网络访问这样的操作被阻塞而饿死。只有面对海量的内存数据和运算密集处理，并行流才会工作最佳。

***

# 第2章 输入与输出
