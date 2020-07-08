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
  - dvanced features
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
> 输入/输出流  
  内存映射文件  
  读写二进制数据  
  文件锁机制  
  对象输入/输出流与序列化  
  正则表达式  
  操作文件  

## 2.1 输入/输出流
- 在Java API中，可以从其中读入一个字节序列的对象称作输入流，而可以向其中写入一个字节序列的对象称作输出流。
- 抽象类**InputStream**和**OutputStream**构成了输入/输出（I/O）类层次结构的基础。

### 2.1.1 读写字节
- **InputStream**类有一个抽象方法：

```java
abstract int read()
/** 该方法将读入一个字节，
  * 并返回读入的字节，
  * 或者在遇到输入源结尾时返回-1。
```

```java
// 从Java 9开始，读取流中所有字节的方法
byte[] bytes = in.readAllBytes();
```

- OutputStream类有一个抽象方法：

```java
// 向某个输出位置写出一个字节
abstract void write(int b)
```

```java
// 一次性写出一个字节数组
byte[] values = ...;
out.write(values);

// transferTo方法可以将所有字节从一个输入流传递到输出流
in.transferTo(out);
```

- read和write方法在执行时都将**阻塞**，直至字节确实被读入或写出。
- 当完成对输入/输出流的读写时，应该通过调用**close**方法关闭它，这会释放掉十分有限的操作系统资源。**而且，如果不关闭文件，那么写出字节的最后一个包可能永远也得不到传递，从而导致写文件失败。**

### 2.1.2 完整的流家族
- **Java**拥有一个流家族，包含各种输入/输出流类型，其数量超过60个。

### 2.1.3 组合输入/输出流过滤器
- **FileInputStream**和**FileInputStream**可以提供附着在一个磁盘文件上的输入流和输出流，而只需要向其构造器提供文件的完整路径名。

```java
var fin = new FileInputStream("employee.dat");
// 这行代码可以查看用户目录下名为“employee.dat”的文件。
```

> 由于反斜杠字符在Java字符串中是转义字符，因此要确保在Windows风格的路径名中使用\\\。

- 某些输入流可以从文件和其他更外部的位置上获取字节，而其他输入流可以将字节组装到更有用的数据类型中。例如，为了从文件中读入数字，首先要创建一个**FileInputStream**，然后将其传递给**DataInputStream**的构造器：

```java
var fin = new FileInputStream("employee.dat");
var din = new DataInputStream(fin);
double x = din.readDouble();
```

### 2.1.4 文本读入与输出
- 在保存数据时，可以选择**二进制格式**或**文本格式**。例如，整数1234存储成二进制数时候，会被写为由字节 00 00 04 D2构成的序列（16进制表示法），而存储成文本格式时，则被存成了字符串“1234”。
- 文本格式：人类可阅读。  
- 二进制格式：效率高。

### 2.1.5 如何写出文本输出
- 文本输出，可以使用**PrintWriter**。

```java
var out = new PrintWriter("employee.txt", StandardCharsets.UTF_8);
String name = "Lam";
double salary = 75000;
out.print(name);
out.print(' ');
out.println(salary);
// employee.txt文件中有 Lam 75000.0
```

### 2.1.6 如何读入文本输入

```java
// 最简单 Scanner类
Scanner in = new Scanner(Path.of("inputfile.txt"),StandardCharsets.UTF_8);
String s1=in.nextLine();

// 将短小的文本文件读入到一个字符串中
var content = (Files.read String path, charset);

// 一行一行地读入
List<String> lines = Files.readAllLines(path, charset);

// 较大文件，将行惰性处理为一个Stream<String>对象
try (Stream<String> lines = Files.lines(path, charset)) {
    ...
}

// 使用扫描器来读入符号（token），即由分隔符分隔的字符串，默认的分隔符是空白字符
Scanner in = ...;
in.useDelimiter("\\PL+"); // 将分隔符修改为任意的正则表达式

// 调用next方法可以产生下一个符号
while (in.hasNext()) {
    String word = in.next();
    ...
}

// 获取一个包含所有符号的流
Stream<String> words = in.tokens();
```

- 在早期的Java版本中，处理文本输入的唯一方式就是通过**BufferedReader**类。它的**readLine**会产生一行文本，或者在无法获得更多的输入时返回null。

```java
InputStream inputStream = ...;
try (var in = new BufferedReader(new InputStreamReader(inputStream, charset))) {
    String line;
    while ((line = in.readLine()) != null) {
        do something with line
    }
}
```

### 2.1.7 以文本格式存储对象
- 输出
```java
public static void writeEmployee(PrintWriter out, Employee e) {
    out.println(e.getName() + "|" + e.getSalary + "|" + e.getHireDay());
}
//在调用该方法之后 切记使用out.close()关闭文件

// 调用该方法打印到文本文件中
    Harry Hacker|35500|1989-10-01
    Carl Cracker|75000|1987-12-15
    Tony Tester|38000|1990-03-15
```

- 输入

```java
/** 每次读入一行，
  * 然后分离所有字段。
  * 用扫描器来读入每一行，
  * 然后用String.split方法将这一行断开成一组标记。
  */
public static Employee readEmployee(Scanner in) {
    String line = in.nextLine();
    String[] tokens = line.split("\\|");
    String name = tokens[0];
    double salary = Double.parseDouble(tokens[1]);
    LocalDate hireDate = LocalDate.parse(tokens[2]);
    int year = hireDate.getYear();
    int month = hireDate.getMonthValue();
    int day = hireDate.getDayOfMonth();
    return new Employee(name, salary, year, month, day);
}
/** split方法的参数是一个描述分隔符的正则表达式，
  * 碰巧的是，
  * 竖线（|）在正则表达式中具有特殊的含义，
  * 因此需要用\字符来表示转义，
  * 而这个\又需要另一个\来转义，
  * 因此产生了“\\|”表达式。
  */
```

### 2.1.8 字符编码方式
- 最常见的编码方式是**UTF-8**，它将每个Unicode编码点编码为1到4个字节的序列。**UTF-8**的好处是传统的包含了英语中用到的所有字符的ASCII字符集中的每个字符都只会占用一个字节。
- **UTF-16**，它会将每个Unicode编码点编码为1个或2个16位值。
- **StandardCharsets**类具有类型为Charset的静态变量，用于表示每种Java虚拟机都必须支持的字符编码方式：

> StandardCharsets.UTF_8  
  StandardCharsets.UTF_16  
  StandardCharsets.UTF_16BE  
  StandardCharsets.UTF_16LE  
  StandardCharsets.ISO_8859_1  
  StandardCharsets.US_ASCII  

- 静态的**forName**方法可以获得另一种编码方式的Charset

```java
Charset shiftJIS = Charset.forName("Shift-JIS");
```

- 在读入或写出文本时，使用Charset对象。

```java
var str = new String(bytes, StandardCharsets.UTF_8);
// 将一个字节数组转换为字符串
```

## 2.2 读取二进制数据
### 2.2.1 DataInput和DataOutput接口
- **DataOutput**接口定义了下面用于以二进制格式写数组、字符、boolean值和字符串的方法：

```java
  writeInt    writeDouble
  writeShort  writeChar
  writeLong   writeBoolean
  writeFloat  writeUTF
  writeChars  writeByte  
  // 例如，writeInt总是将一个整数写出为4字节的二进制数量值，而不管它有多少位。
```

- 为了读回数据，可以使用在**DataInput**接口中定义的下列方法：

```java
  readInt    readDouble
  readShort  readChar
  readLong   readBoolean
  readFloat  readUTF
```
- example

```java
/** DataInputStream类实现了DataInput接口，
  * 为了从文件中读入二进制数据，
  * 可以将DataInputStream与某个字节源相组合，
  * 例如FileInputStream：
  */
var in = new DataInputStream(new FileInputStream("employee.dat"));

/** 类似地，
  * 要写出二进制数据，
  * 可以使用实现了DataOutput接口的DataOutputStream类：
  */
var out = new DataOutputStream(new FileOutputStream("employee.dat"));
```

### 2.2.2 随机访问文件
- **RandomAccessFile**类可以在文件中的任何位置查找或写入数据。

```java
var in = new RandomAccessFile("employee.dat", "r");
var inOut = new RandomAccessFile("employee.dat", "rw");
// r表示只读(read)，rw表示读写(read and write)
```

- 随机访问文件有一个表示下一个将被读入或写出的字节所处位置的文件指针，**seek**方法用来将这个指针设置到文件中的任意的字节位置，seek的参数是一个long类型的整数，它的值位于0到文件按照字节来度量的长度之间。
- **getFilePointer**方法将返回文件指针的当前位置。

### 2.2.3 ZIP文档
- ZIP文档（通常）以压缩格式存储了一个或多个文件，每个ZIP文档都有一个头，包含诸如每个文件名字和所使用的压缩方法等信息。
- 读入：

```java
var zin = new ZipInputStream(new FileInputStream(zipname));
ZipEntry entry;
while ((entry = zin.getNextEntry()) != null) {
    read the contents of zin
    zin.closeEntry();
}
zin.close();
```

- 写出：

```java
var fout = new FileOutputStream("test.zip");
var zout = new ZipOutputStream(fout);
for all files
{
    var ze = new ZipEntry(filename);
    zout.putNextEntry(ze);
    send data to zout
    zout.closeEntry();
}
zout.close();
```

## 2.3 对象输入/输出流与序列化
- Java语言支持一种称为**对象序列化**（object serialization）的非常通用的机制，它可以将任何对象写出到输出流中，并在之后将其返回。

### 2.3.1 保存和加载序列化对象
- 写出：

```java
// 创建一个ObjectOutputStream对象
var out = new ObjectOutputStream(new FileOutputStream("employee.dat");
// 使用writeObject方法保存对象
var harry = new Employee("Harry Hacker", 50000, 1989, 10, 1);
var boss = new Manager("Carl Cracker", 80000, 1987, 12, 15);

out.writeObject(harry);
out.writeObject(boss);
```

- 读入：

```java
// 创建一个ObjectInputStream对象
var in = new ObjectInputStream(new FileInputStream("employee.dat"));
// 使用readObject方法获得对象
var e1 = (Employee) in.readObject();
var e2 = (Employee) in.readObject();
```

- 每个对象都是用一个**序列号**（serial number）保存的。序列化对象算法：

> 1. 对于遇到的每一个对象引用都关联一个序列号。
> 2. 对于每个对象，当第一次遇到时，保存其对象数据到输出流中。
> 3. 若某个对象之前已经被保存过，那么只写出“与之前保存过的序列号为x的对象相同”。

- 在读回对象时，整个过程反过来：

> 1. 对于对象输入流中的对象，在第一次遇到其序列号时，构建它，并使用流中数据来初始化它，然后记录这个顺序号和新对象之间的关联。
> 2. 当遇到“与之前保存过的序列号为x的对象相同”这一标记时，获取与这个序列号相关联的对象引用。

## 2.4 操作文件
- Path和Files类封装了在用户机器上处理文件系统所需的所有功能。

### 2.4.1 Path
- 静态的Paths.get方法接受一个或多个字符串，并将它们用默认文件系统的路径分隔符（类UNIX文件系统是/，Windows是\）连接起来。

```java
Path absolute = Paths.get("/home", "harry");
Path relative = Paths.get("myprog", "conf", "user.properties");
```

- 相关方法：

```java
/** 1. resolve方法
  * 调用p.resolve(q)将按照下列规则返回一个路径：
  * 若q是绝对路径，则结果就是q。
  * 否则，根据文件系统的规则，将“p后面跟着q”作为结果。
  */
Path workRelative = Paths.get("work");
Path workPath = basePath.resolve(workRelative);
  // resolve方法有一种快捷方式，它接受一个字符串而不是路径。
Path workPath = basePath.resolve("work");

/** 2. resolveSibling方法
  * 若workPath是 /opt/myapp/wokr ，
  * 下面的调用，
  * 将创建 /opt/myapp/temp 。
  */
Path tempPath = workPath.resolveSibling("temp");

/** 3. relativize方法
  * 以“/home/harry”为目标对“/home/fred/input.txt”进行相对化操作，
  * 会产生“../fred/input.txt”，
  * 其中，假设..表示文件系统中的父目录。
  **/
  
/** 4. normalize方法
  * 该方法会移除所有冗余的.和.. ，
  * 规范化 /home/harry/../fred/./input.txt ，
  * 将产生 /home/fred/input.txt 。
  */
  
/** 5. toAbsolutePath方法
  * 将产生给定路径的绝对路径。
  */
```

### 2.4.2 读写文件
- Files类可以使得普通文件操作变得快捷。

```java
// 读取文件的所有内容
byte[] bytes = Files.readAllBytes(path);

// 从文本文件中读取内容
var content = Files.readString(path, charset);

// 将文件当作行序列读入
List<String> lines = Files.readAllLines(path, charset);

// 写出一个字符串到文件中
Files.writeString(path, content.charset);

// 向指定文件追加内容
Filse.write(path, content.getBytes(charset), StandardOpenOption.APPEND);

// 将一个行的集合写出到文件中
Files.write(path, lines, charset);

/** 以上简便方法适用于处理中等长度的文本文件，
  * 若要处理文件长度比较大，
  * 或者是二进制文件，
  * 那么还是应该使用输入/输出流或者读入器/写出器：
  */
InputStream in = Files.newInputStream(path);
OutputStream out = Files.newOutputStream(paht);

Reader in = Files.newBufferedReader(path, charset);
Writer out = Files.newBufferedWriter(path, charset);
```

### 2.4.3 创建文件和目录

```java
// 1. 创建新目录
/** createDirecoty方法
  * F:\\IDEAWORKSPACE\\TestDemo文件夹已经存在 \\newDir不存在
  ***************************************************************
  * String pathstr = "F:\\IDEAWORKSPACE\\TestDemo\\newDir";
  * Path path = Paths.get(pathstr);
  */
Files.createDirecoty(path);

/** createDirecoties方法
  * F:\\IDEAWORKSPACE\\TestDemo路径已经存在 \\midDir\\newDir不存在
  ***************************************************************
  * String pathstr = "F:\\IDEAWORKSPACE\\TestDemo\\midDir\\newDir";
  * Path path = Paths.get(pathstr);
  */
Files.createDirecoties(path);  

// 2. 创建文件
/** createFile方法
  * F:\\IDEAWORKSPACE\\TestDemo路径已经存在 myfile.txt不存在
  ***************************************************************
  * String pathstr = "F:\\IDEAWORKSPACE\\TestDemo\\myfile.txt";
  * Path path = Paths.get(pathstr);
  */
Files.createFile(path);
// 会在指定路径下创建myfile.txt空文件
```

- 有些便捷方法可以用来在给定个位置或者系统指定位置创建临时文件或者临时目录：

```java
Path newPath = Files.createTempFile(dir, prefix, suffix);
Path newPath = Files.createTempFile(prefix, suffix);
Path newPath = Files.createTempDirectory(dir, prefix);
Path newPath = Files.createTempDirectory(prefix);
/** dir是一个Path对象，prefix和suffix是可以为null的字符串。
  * 例如，调用Files.createTempFile(null, ".txt")
  * 可能会返回一个像 /temp/1234405522364837194.txt 这样的路径。
  */
```

### 2.4.4 复制、移动和删除文件

```java
// 1. 复制
Files.copy(fromPath, toPath);

// 2. 移动
Files.move(fromPath, toPath);

/** 若目标路径已经存在，
  * 则复制或移动将失败。
  * 若要覆盖已有的目标路径，
  * 可以使用REPLACE_EXISTING选项。
  * 若要复制所有的文件属性，
  * 可以使用COPY_ATTRIBUTES选项。
  */
Files.copy(fromPath, toPath, StandardCopyOption.REPLACE_EXISTING, StandardCopyOption.COPY_ATTRIBUTES);

/** 将操作定义为原子性的，
  * 以此保证要么移动操作成功完成，
  * 要么源文件继续保持在原来位置。
  * 使用ATOMIC_MOVE实现。
  */
Files.move(fromPath, toPath, StandardCopyOption.ATOMIC_MOVE);

/** 将一个输入流复制到Path中，
  * 表示想要将该输入流存储到硬盘上。
  * 类似地，可以将一个Path复制到输出流中。
  */
Files.copy(inputStream, toPath);
Files.copy(fromPath, outputStream);

// 3. 删除
Files.delete(path); // 文件不存在会报错
// 使用deleteIfExists方法
boolean deleted = Files.deleteIfExists(path);
```

- 用于文件操作的标准选项

<table>
  <thead>
    <tr>
      <th>选项</th>
      <th>描述</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>StandardOpenOption</td>
      <td>与newBufferedWriter、newInputStream、newOutputStream、write一起使用</td>
    </tr>
    <tr>
      <td>READ</td>
      <td>用于读取而打开</td>
    </tr>
    <tr>
      <td>WRITE</td>
      <td>用于写入而打开</td>
    </tr>
    <tr>
      <td>APPEND</td>
      <td>如果用于写入而打开，那么在文件末尾追加</td>
    </tr>
    <tr>
      <td>TRUNCATE_EXISTING</td>
      <td>如果用于写入而打开，那么移除已有内容</td>
    </tr>
    <tr>
      <td>CREATE_NEW</td>
      <td>创建新文件并且在文件已存在的情况下会创建失败</td>
    </tr>
    <tr>
      <td>CREAT</td>
      <td>自动在文件不存在的情况下创建新文件</td>
    </tr>
    <tr>
      <td>DELETE_ON_CLOSE</td>
      <td>当文件被关闭时，尽“可能”地删除该文件</td>
    </tr>
    <tr>
      <td>SPARSE</td>
      <td>给文件系统一个提示，表示该文件是稀疏的</td>
    </tr>
    <tr>
      <td>DSYNC或SYNC</td>
      <td>要求对文件数据|数据和元数据的每次更新都必须同步地写入到存储设备中</td>
    </tr>
    <tr>
      <td>StandardCopyOption</td>
      <td>与copy和move一起使用</td>
    </tr>
    <tr>
      <td>ATOMIC_MOVE</td>
      <td>原子性地移动文件</td>
    </tr>
    <tr>
      <td>COPY_ATTRIBUTES</td>
      <td>复制文件的属性</td>
    </tr>
    <tr>
      <td>REPLACE_EXISTING</td>
      <td>如果目标已存在，则替换它</td>
    </tr>
    <tr>
      <td>LinkOption</td>
      <td>与上面所有方法以及exists、isDirectory、isRegularFile等一起使用</td>
    </tr>
    <tr>
      <td>NOFOLLOW_LINKS</td>
      <td>不要跟踪符号链接</td>
    </tr>
    <tr>
      <td>FileVisitOption</td>
      <td>与find、walk、walkFileTree一起使用</td>
    </tr>
    <tr>
      <td>FOLLOW_LINKS</td>
      <td>跟踪符号链接</td>
    </tr>
  </tbody>
</table>

### 2.4.5 获取文件信息

```java
/** 1.
  * 以下静态方法都将返回一个boolean值，
  * 表示检查路径的某个属性的结果。
  */
exists
isHidden
isReadablle，isWritable，isExecutable
isRegularFile，isDirectory，isSymbolicLink

/** 2. size方法
  * 返回文件的字节数。
  */
long fileSize = Files.size(path);

/** 3. getOwner方法
  * 将文件拥有者作为java.nio.file.attribute.UserPrincipal的一个实例返回。
  */
```

- 基本文件属性包括：

> 1. 创建文件、最后一次访问以及最后一次修改文件的时间，这些时间都表示成java.nio.file.attribute.FileTime。
> 2. 文件是常规文件、目录还是符号链接，抑或这三者都不是。
> 3. 文件尺寸。
> 4. 文件主键，这是某种类的对象，具体所属类与文件系统相关，有可能是文件的唯一标识符，也可能不是。

```java
// 获取这些属性，可以调用
BasicFileAttributes attributes = Files.readAttributes(path, BasicFileAttributes.class);
// 若文件系统兼容POSIX，那么可以获得一个PosixFileAttributes实例
PosixFileAttributes attributes = Files.readAttributes(path, PosixFileAttributes.class);
```

### 2.4.6 访问目录中的项

```java
/** 静态的Files.list方法
  * 返回一个可以读取目录中各个项的Stream<Path>对象
  */
try (Stream<Path> entries = Files.list(pathToDirectory)) {
    ...
}

// list方法不会进入子目录。

/** Files.walk方法
  * 处理目录中的所有子目录
  */
try (Stream<Path> entries = Files.walk(pathToRoot)) {
    // Contains all descendants, visited in depth-first order
}

```

### 2.4.7 使用目录流
- 使用Files.newDirectoryStream对象，实现对遍历过程更加细粒度的控制。

```java
try (DirectoryStream<Path> entries = Files.newDirectorySystem(dir)) {
    for (Path entry : entries)
        Process entries
}

// 可以用glob模式来过滤文件
try (DirectoryStream<Path> entries = Files.newDirectorySystem(dir, "*.java"))
```

- 所有glob模式

<table>
  <thead>
    <tr>
      <th>模式</th>
      <th>描述</th>
      <th>示例</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>*</td>
      <td>匹配路径组成部分中0个或多个字符</td>
      <td>*.java匹配当前目录中所有Java文件</td>
    </tr>
    <tr>
      <td>**</td>
      <td>匹配跨目录边界的0个或多个字符</td>
      <td>**.java匹配在所有子目录中的Java文件</td>
    </tr>
    <tr>
      <td>?</td>
      <td>匹配一个字符</td>
      <td>????.java匹配所有四个字符的Java文件</td>
    </tr>
    <tr>
      <td>[...]</td>
      <td>匹配一个字符集合，可以使用连线符[0-9]和取反符[!0-9]</td>
      <td>Test[0-9A-F]匹配Testx.java，其中x是一个十六进制数字</td>
    </tr>
    <tr>
      <td>{...}</td>
      <td>匹配由都好隔开的多个可选项之一</td>
      <td>*.{java,class}匹配所有的Java文件和Class文件</td>
    </tr>
    <tr>
      <td>\</td>
      <td>转义上述任意模式中的字符以及\字符</td>
      <td>*\**匹配所有文件名中包含*的文件</td>
    </tr>
  </tbody>
</table>

### 2.4.8 ZIP文件系统

```java
FileSystem fs = FileSystems.newFileSystem(Paths.get(zipname), null);
/** 以上调用将建立一个文件系统，
  * 它包含ZIP文档中的所有文件。
  * 其中zipname是某个ZIP文件的名字。
  */
```

```java
// 已知文件名，从ZIP文档中复制出这个文件
Files.copy(fs.getPath(sourceName), targetPath);
// 其中的fs.getPath对于任意文件系统来说都与Paths.get类似
```

## 2.5 内存映射文件
- 大多数操作系统都可以利用**虚拟内存**来实现将一个文件或者文件的一部分“映射”到内存中。然后，该文件就可以被当作内存数组一样地访问，这比传统的文件操作要快得多。

### 2.5.1 内存映射文件的性能

```java
/** 首先，从文件中获得一个通道（channel），
  * 通道是用于磁盘文件的一种抽象，
  * 可以访问诸如内存映射、文件加锁机制以及文件间快速数据传递等操作系统特性。
  */
FileChannel channel = FileChannel.open(path, options);

/** 然后，通过调用FileChannel类的map方法从这个通道中获得一个ByteBuffer。
  * 可以指定映射的文件区域和映射模式，
  * 支持的模式有三种：
  * 1. FileChannel.MapMode.READ_ONLY：所产生的缓冲区是只读的，任何对该缓冲区写入的尝试都会导致ReadOnlyBufferException异常。
  * 2. FileChannel.MapMode.READ_WRITE：所产生的缓冲区是可写的，任何修改都会在某个时段写回到文件中。
  * 3. FileChannel.MapMode.PRIVATE：所产生的缓冲区是可写的，但是任何修改对这个缓冲区来说都是私有的，不会传播到文件中。
  * 一旦有了缓冲区，就可以使用ByteBuffer类和Buffer超类的方法读写数据了。
  */
```

- 缓冲区支持顺序和随机数据访问，它有一个可以通过get和put操作来移动的位置。

```java
// 顺序遍历缓冲区中的所有字节
while (buffer.hasRemaining()) {
    byte b = buffer.get();
    ...
}

// 随机访问
for (int i = 0; i < buffer.limit(); i++) {
    byte b = buffer.get(i);
    ...
}
```

### 2.5.2 缓冲区数据结构
- **缓冲区**是由具有相同类型的数值构成的数组，**Buffer**类是一个抽象类，它有众多的具体子类，包括**ByteBuffer**、**CharBuffer**、**DoubleBuffer**、**IntBuffer**、**LongBuffer**和**ShortBuffer**。
- 在实践中最常用的将是ByteBuffer和CharBuffer。如图所示，每个缓冲区都具有：

> 1. 一个容量，它永远不能改变。
> 2. 一个读写位置，下一个值将在此进行读写。
> 3. 一个界限，超过它进行读写是没有意义的。
> 4. 一个可选的标记，用于重复一个读入或写出操作。  
> 这些值满足：0 ≤ 标记 ≤ 读写位置 ≤ 界限 ≤ 容量

![image](/assets/img/post_img/buffer.png)

## 2.6 文件加锁机制

> 假设一个应用程序将用户的偏好存储在一个配置文件中，当用户调用这个应用的两个实例时，这两个实例就有可能会同时希望写配置文件。在这种情况下，第一个实例应该锁定文件，当第二个实例发现文件被锁定时，它必须决策是等待直至文件解锁，还是直接跳过这个写操作过程。

```java
/** 1. 锁定文件
  * 可以调用FileChannel类的lock或tryLock方法
  */
// A. 阻塞直至可获得锁
FileChannel = FileChannel.open(path);
FileLock lock = channel.lock();

// B. 立即返回  要么返回锁 要么在锁不可获得的情况下返回null
FileLock lock = channel.lock();

/** 2. 锁定文件的一部分
  */
FileLock lock(long start, long size, boolean shared);
// or 
FileLock trylock(long start, long size, boolean shared);
/** 如果shared标志为false，
  * 则锁定文件的目的是读写。
  * 而如果为true，
  * 则这是一个共享锁，
  * 允许多个进程从文件中读入，
  * 并阻止任何进程获得独占的锁。
  */
```

- 文件加锁机制是依赖于操作系统的，需要注意：

> 1. 在某些系统中，文件加锁仅仅是建议性的，如果一个应用未能得到锁，它仍旧可以向被另一个应用并发锁定的文件执行写操作。
> 2. 在某些系统中，不能在锁定一个文件的同时将其映射到内存中。
> 3. 文件锁是由整个Java虚拟机持有的。如果有两个程序是由同一个虚拟机启动的，那么它们不可能每一个都获得一个在同一个文件上的锁。当调用lock和trylock方法时，如果虚拟机已经在同一个文件上持有了另一个重叠的锁，那么这两个方法将抛出OverlappingFileLockException。
> 4. 在一些系统中，关闭一个通道会释放由Java虚拟机持有的底层文件上的所有锁。因此，在同一个锁定文件上应避免使用多个通道。
> 5. 在网络文件系统上锁定文件是高度依赖于系统的，因此应该尽量避免。

## 2.7 正则表达式
- **正则表达式**（regular expression）用于指定字符串的模式，可以在任何需要定位匹配某种特定模式的字符串的情况下使用正则表达式。

### 2.7.1 正则表达式语法

> 1. 字符类是一个括在括号中的可选择的字符集。例如，[Jj]、[0-9]、[A-Za-z]或[^0-9]。这里“-”表示是一个范围，而“^”表示补集。
> 2. 如果字符类中包含“-”，那么它必须是第一项或是最后一项；如果要包含“[”，那么它必须是第一项；如果要包含“^”，那么它可以是除开始位置之外的任何位置。其中，只需要转义“[”和“\”。
> 3. 有许多预定义的字符类，例如\d（数字）和\p{Sc}（Unicode货币符号）。
