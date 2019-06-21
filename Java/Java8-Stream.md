---
title: Java8 Stream
date: 2019-06-15 17:23:29
categories: java8
tags: 
- Java8
- Stream
---
## Java8 Stream

```
A sequence of elements supporting sequential and parallel aggregate operations.
```

从`Stream`的定义可以看出

- Stream是一组元素的集合
- Stream支持顺序和并行地对元素进行操作

### How it work？

先看示例：

```java
public void method1() {
    List<String> list = Stream.of("a", "b", "c", 1, 2, 3)
            .peek(System.out::print)
            .map(String::valueOf)
            .sorted()
            .collect(Collectors.toList());
    System.out.println("\n" + JSON.toJSONString(list));
}
```
上述程序输出结果：
```
abc123
["1","2","3","a","b","c"]
```
通过上述例子：
1. `Stream.of()`产生一个流；
2. `peek`将所有元素进行打印；
3. `map`将所有元素转化为String；
4. `sorted`将所有元素进行排需；
5. `collect`将所有元素聚合为一个List。

由此总结对流的操作可分为3个阶段
1. 创建流
2. 转化流（每次转化，原有的Stream不变，产生一个新的Stream对象，可以对流进行多次转化）
3. 消耗流（获取最终的结果）

```
+--------------------+   +------+   +-----+   +--------+   +-------+
| stream of elements |-> | peek |-> | map |-> | sorted |-> |collect|
+--------------------+   +------+   +-----+   +--------+   +-------+
```

`Stream`的操作就像一条流水线。我们可以对流进行不同地操作（如`peek`、`map`等），最终得到前面处理的结果。

### 操作类型

`Stream`的操作分为两大类*中间操作（intermediate）*和*结束操作（terminal）*。

#### 中间操作

中间操作可进一步区分为*无状态（stateless）*和*有状态（stateful）*

|       | 方法                                                         |
| ------ | ------------------------------------------------------------ |
| 无状态 | peek、filter、map、mapToInt、mapToLong、mapToDouble、flatMap、flatMapToInt、flatMapToLong、flatMapToDouble |
| 有状态 | sorted、distinct、limit、skip                                |

#### 结束操作

结束操作中有一部分操作可以视为*短路操作（short-circuiting）*

|       | 方法                                                         |
| ------ | ------------------------------------------------------------ |
| 非短路 | forEach、forEachOrdered、toArray、collect、min、max、reduce、count |
| 短路 | anyMatch、noneMatch、findFirst、findAny                      |


### 有什么优点？

一般来说，Java8之前的编程方式，对数组、集合中元素遍历是显示的在外部进行，我们称之为*外部迭代*，而`Stream`的方式叫做*内部迭代*，采用了*访问者模式（Visitor）*实现。`外部迭代`我们的关注点是怎么做，`内部迭代`我们的关注点是做什么。通过`Stream`对元素的*内部迭代*我们更容易清晰的看到一个方法对元素做了什么操作。

### 元素执行顺序

在`Stream`编程中，元素的执行顺序非常重要。

还是先看例子

```java
public void method3() {
    Stream.of("a", "b", "c")
            .peek(s -> System.out.println("peek -> " + s))
            .map(s -> {
                System.out.println("map -> " + s);
                return s.toUpperCase();
            })
            .forEach(s -> System.out.println("forEach -> " + s));
}
```

直接看这段程序的输出
```
peek -> a
map -> a
forEach -> A
peek -> b
map -> b
forEach -> B
peek -> c
map -> c
forEach -> C
```

是不是与想象中的不一样？

`Stream`对元素的操作是一个元素执行完所有的操作在执行下一个元素，而不是一个操作执行完所有的元素再进行下一个操作。这就更加证明了`Stream`对元素的操作是像流水线那样。

#### 为什么执行顺序如此重要

通过上文，还不足以说明执行顺序为什么非常重要。

再看一个例子

```java
public void method4() {
    Stream.of(2, 4, 5, 3, 1)
            .sorted((i1, i2)->{
                System.out.format("sorted -> %d %d \n", i1, i2);
                return i1.compareTo(i2);
            })
            .map(i -> {
                System.out.println("map -> " + i);
                return i * 2;
            })
            .filter(i -> {
                System.out.println("filter -> " + i);
                return i > 8;
            })
            .forEach(i -> System.out.println("forEach -> " + i));
}
```

输出如下：

```
sorted -> 4 2 
sorted -> 5 4 
sorted -> 3 5 
sorted -> 3 4 
sorted -> 3 2 
sorted -> 1 4 
sorted -> 1 3 
sorted -> 1 2 
map -> 1
filter -> 2
map -> 2
filter -> 4
map -> 3
filter -> 6
map -> 4
filter -> 8
map -> 5
filter -> 10
forEach -> 10
```

从上述输出可以看出，每个元素都进行了`sorted`、`map`操作，那么我们对上述程序做一个变形，如下

```java
public void method4() {
    Stream.of(2, 4, 5, 3, 1)
            .filter(i -> {
                System.out.println("filter -> " + i);
                return i * 2 > 8;
            })
            .map(i -> {
                System.out.println("map -> " + i);
                return i * 2;
            })
            .sorted((i1, i2)->{
                System.out.format("sorted -> %d %d \n", i1, i2);
                return i1.compareTo(i2);
            })
            .forEach(i -> System.out.println("forEach -> " + i));
}
```

变形之后该段程序输出为：

```
filter -> 2
filter -> 4
filter -> 5
map -> 5
filter -> 3
filter -> 1
forEach -> 10
```

可以看出，元素[1, 2, 3, 4]并未进行`map`操作，所有的元素都未进行`sorted`操作，因为`filter`操作将不满足条件的元素都过滤掉了，只剩下一个元素，故不会进行`sorted`操作。

所以在使用`Stream`对元素进行操作时，**合理的调整操作顺序，可以提升性能**。

### 延时性

`Stream`对元素的操作有一个非常重要的特性：延时性。为了更加直观的看到这个特性，我们先看一个例子

```java
public void method5() {
    Stream.of("a", "b", "c").peek(System.out::print);
}
```

按照我们的预期，上述程序应该输出
```
abc
```

可是在实际执行过程中却发现并没有任何输出，这是为什么？

这是因为**在对stream操作过程中，没有调用结束操作的时候，所有的中间操作都不会执行。**

### 高阶操作

#### 短路操作

在遇到*短路操作*时，一旦满足*短路操作*的条件时，`Stream`会马上终止。例如：

```java
public void method6() {
    Stream.of("x", "y", "z")
        .map(s -> {
            System.out.println("map -> " + s);
            return s.toUpperCase();
        })
        .anyMatch(s -> {
            System.out.println("anyMatch -> " + s);
            return s.startsWith("X");
        });
}
```

输出：

```
map -> x
anyMatch -> X
```

上述例子中，第一个元素即满足了`anyMatch`的条件，后续的元素便不再进行`Stream`上的各种操作，其余*短路操作*不再一一举例。

#### collect

`collect`操作是十分常用的*终止操作*，可以将流中的元素聚合成我们想要的集合类（如`List`、`Set`、`Map`等），也可以对元素做一些拼接、汇总等。下面的例子演示了`collect`一些常用的场景。

```java
public void method7() {
    System.out.println("---聚合为List---");
    List<String> list = Stream.of("a", "b", "c").collect(Collectors.toList());
    list.forEach(System.out::print);

    System.out.println("---拼接为字符串---");
    String str = Stream.of("x", "y", "z").collect(Collectors.joining(","));
    System.out.println(str);

    System.out.println("---聚合为汇总信息---");
    IntSummaryStatistics intSummaryStatistics = Stream.of(1, 2, 3).collect(Collectors.summarizingInt(value -> value));
    System.out.println(intSummaryStatistics);

    System.out.println("---求平均值---");
    Double average = Stream.of(1, 2, 3).collect(Collectors.averagingInt(value -> value));
    System.out.println(average);

    System.out.println("---分组---");
    Map<Integer, List<Integer>> map1 = Stream.of(1, 2, 3).collect(Collectors.groupingBy(value -> value));
    System.out.println(JSON.toJSONString(map1));

    System.out.println("---将元素聚合为Map--");
    Map<Integer, String> map2 = Stream.of("a", "b", "c").collect(Collectors.toMap(String::hashCode, Function.identity()));
    System.out.println(JSON.toJSONString(map2));
}
```

#### flatMap

通过上文，我们已经知道如何使用`map`，但是有的时候`map`使用起来十分受限，因为`map`只能将一个对象映射为另一个对象，但是当我们希望将一个对象转化对多个或零个对象的时候，就需要用到`flatMap`。

看例子：

```java
public void method8() {
    List<Foo> foos = new ArrayList<>();
    IntStream.range(1, 4).forEach(i -> foos.add(new Foo("Foo" + " -> " + i)));
    foos.forEach(foo -> IntStream.range(1, 4).forEach(i -> foo.bars.add(new Bar(foo.name + " -> " + "Bar" + " -> " + i))));
    List<Bar> bars = foos.stream()
            .flatMap(foo -> foo.bars.stream())
            .collect(Collectors.toList());
    bars.forEach(bar -> System.out.println(bar.name));
}

@Data
class Foo {
    String name;
    List<Bar> bars = new ArrayList<>();
    public Foo(String name) {
        this.name = name;
    }
}

@Data
class Bar {
    String name;
    public Bar(String name) {
        this.name = name;
    }
}
```

输出：

```
Foo -> 1 -> Bar -> 1
Foo -> 1 -> Bar -> 2
Foo -> 1 -> Bar -> 3
Foo -> 2 -> Bar -> 1
Foo -> 2 -> Bar -> 2
Foo -> 2 -> Bar -> 3
Foo -> 3 -> Bar -> 1
Foo -> 3 -> Bar -> 2
Foo -> 3 -> Bar -> 3
```

可以看出，上述例子通过`flatMap`操作，我们成功地将一个含有3个foo元素的流转化为了含有9个bar元素的流。

#### reduce

`reduce`操作是将所有流中的元素组合为单一结果。

下面例子演示了两种常见的规约操作。

```java
public void method9() {
    List<Foo> foos = new ArrayList<>();
    IntStream.range(1, 4).forEach(i -> foos.add(new Foo("Foo" + " -> " + i)));
    foos.forEach(foo -> IntStream.range(1, 4).forEach(i -> foo.bars.add(new Bar(foo.name + " -> " + "Bar" + " -> " + i))));
    System.out.println("---foos中的元素---");
    foos.forEach(o -> System.out.println(JSON.toJSONString(o)));
    System.out.println("---foos进行规约之后---");
    foos.stream()
            .reduce((foo1, foo2) -> foo1.name.hashCode() > foo2.name.hashCode() ? foo1 : foo2)
            .ifPresent(foo -> System.out.println(JSON.toJSONString(foo)));

    System.out.println("---foos将元素规约成一个新的对象---");
    Foo foo = foos.stream()
            .reduce(new Foo(""), (foo1, foo2) -> {
                foo1.name += foo2.name;
                return foo1;
            });
    System.out.println(JSON.toJSONString(foo));
}
```

### 并行流

从流的定义中我们得知流是支持并行地对元素进行操作地。在流中存在大量元素地情况下，使用并行流可以极大的提升运行时的性能。

并行流使用公共的`ForkJoinPool`，由`ForkJoinPool.commonPool()`方法提供。

```java
ForkJoinPool commonPool = ForkJoinPool.commonPool();
System.out.println(commonPool.getParallelism());
```

通过上述方法可以查看公共池的默认线程数，也可以通过JVM参数`-Djava.util.concurrent.ForkJoinPool.common.parallelism=5`来调整。

看下面的例子：

```java
public void method10() {
    List<String> list = Arrays.asList("a", "b", "c", "d")
            .parallelStream()
            .filter(s -> {
                System.out.format("filter: %s [%s]\n", s, Thread.currentThread().getName());
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                return true;
            })
            .map(s -> {
                System.out.format("map: %s [%s]\n", s, Thread.currentThread().getName());
                return s.toUpperCase();
            })
            .collect(Collectors.toList());
    list.forEach(System.out::print);
}
```

输出：

```
filter: c [main]
filter: b [ForkJoinPool.commonPool-worker-2]
filter: a [ForkJoinPool.commonPool-worker-3]
filter: d [ForkJoinPool.commonPool-worker-1]
map: c [main]
map: d [ForkJoinPool.commonPool-worker-1]
map: b [ForkJoinPool.commonPool-worker-2]
map: a [ForkJoinPool.commonPool-worker-3]
ABCD
```

上述例子中，为了使对元素的操作能在`ForkJoinPool.commonPool-worker-*`的线程上执行，我们针对`filter`操作进行了*sleep*，通过观察发现并行流使用的线程确实是由`ForkJoinPool.commonPool()`提供。

让我们对上述程序增加一个`sorted`操作，看会发生什么变化：

```java
public void method11() {
    System.out.println("\n-----");
    Arrays.asList("a", "b", "c", "d")
            .parallelStream()
            .filter(s -> {
                System.out.format("filter: %s [%s]\n", s, Thread.currentThread().getName());
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                return true;
            })
            .map(s -> {
                System.out.format("map: %s [%s]\n", s, Thread.currentThread().getName());
                return s.toUpperCase();
            })
            .sorted((s1, s2) -> {
                System.out.format("sort: %s <> %s [%s]\n", s1, s2, Thread.currentThread().getName());
                return s1.compareTo(s2);
            })
            .collect(Collectors.toList()).forEach(System.out::print);
    System.out.println("\n");
}
```

输出：

```
filter: d [ForkJoinPool.commonPool-worker-1]
filter: c [main]
filter: b [ForkJoinPool.commonPool-worker-3]
filter: a [ForkJoinPool.commonPool-worker-2]
map: c [main]
map: d [ForkJoinPool.commonPool-worker-1]
map: a [ForkJoinPool.commonPool-worker-2]
map: b [ForkJoinPool.commonPool-worker-3]
sort: B <> A [main]
sort: C <> B [main]
sort: D <> C [main]
ABCD
```

我们发现在经经过`sorted`操作时，全部元素都在`main`线程中执行了。实际上，并行流上的`sorted`在背后使用了Java8中新的方法`Arrays.parallelSort()`，这个方法会根据流中元素的长度来决定是以串行还是并行的方式来执行。