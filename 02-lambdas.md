Lambdas
-----

Java 8中最重要的功能之一就是引入Lambda表达式。它们使您的代码简洁，并允许您传递行为。一段时间以来，Java一直因为冗长，缺乏面向函数编程而受到批评。面向函数编程越来越流行，Java被迫拥抱面向函数编程。否则，Java将失去竞争力。

Java 8是世界上最流行的语言采用面向函数编程向前迈出的一大步。为了支持函数式编程风格，该语言必须支持作为变量的方法。在Java 8之前，如果不使用匿名内部类，则无法编写干净的函数式代码。随着Lambda表达式的引进，函数已经成为变量，它们可以像任何其他变量一样被传递。

Lambda表达式允许您定义一个未绑定到标识符的匿名函数。您可以像编程语言中的任何其他构造方式一样使用它们，如变量声明。如果编程语言需要支持更高阶的函数，则需要Lambda表达式。高阶函数是接受其他函数作为参数或作为结果返回函数的函数。

> 本章代码 [ch02 package](https://github.com/shekhargulati/java8-the-missing-tutorial/tree/master/code/src/main/java/com/shekhargulati/java8_tutorial/ch02).

现在，随着Java 8中Lambda表达式的引入，Java支持更高阶的函数。让我们看看Lambda表达式的规范示例 -  Java的`Collections`类中的`sort`函数。 `sort`函数有两个变量 - 一个接受`List`，另一个接受`List`和一个`Comparator`。第二个排序函数是一个高阶函数的例子，它接受一个lambda表达式，如下面的代码片段所示。

```java
List<String> names = Arrays.asList("shekhar", "rahul", "sameer");
Collections.sort(names, (first, second) -> first.length() - second.length());
```

上面显示的代码按名称的长度对名称进行排序。程序的输出如下所示。

```
[rahul, sameer, shekhar]
```

代码片段中的表达式： `(first, second) -> first.length() - second.length()`  

是一个`Comparator<String>` 的lambda表达式。

*  `(first, second)`是 `Comparator ` 中 `compare`方法的参数
* `first.length() - second.length()` 是方法体
* `->` 是lambda的操作符。

在我们深入研究Java 8 Lambdas之前，让我们看一下他们的历史，以了解它们存在的原因。

## Lambdas的历史

Lambda表达式基于数学中的[lambda演算](https://en.wikipedia.org/wiki/Lambda_calculus)得名，
lambda演算起源于Alonzo Church on的工作将用函数表示正式化的计算概念。lambda演算是图灵完备的，数学形式的表达方法
计算。图灵完备意味着你可以表达任何数学计算通过lambdas。

lambda演算成为了一个强大的基础，理论基础的函数式编程语言。许多流行的函数式编程语言
像Haskell和Lisp都是基于Lambda演算的。更高阶的概念函数，即接受其他函数的函数，来自于lambda演算。

lambda演算的主要概念是表达式。表达式可以是表示为:

```
<expression> := <variable> | <function>| <application>
```

* **variable)** -- 变量名可能是一个字符或字符串，它表示一个参数（形参）或者一个值（实参）。
e.g. z var
* **function)** -- 它是一个匿名函数定义，只接受一个变量生成另一个表达式. 例如, `λx.x*x` 就是计算x的平方.
* **application** -- 这是将函数应用于参数的行为.例如你想计算10的平法，所以在lambda演算，你将写一个计算平方
的方法，`λx.x*x` 接着带入 10。你不仅可以应用像10这样简单的值，你可以把一个函数应用到另一个函数来产生另一个函数。
例如`(λx.x*x) (λz.z+10)`，你就能计算一个数字加10后的平方了。

现在,你了解λ演算及其对函数式编程的影响语言。让我们了解一下它是如何在Java 8中实现的。

## 在Java 8之前传递行为

在Java 8之前，传递行为的唯一方法是使用匿名类。假设您希望在用户注册后在另一个线程中发送电子邮件。在Java 8之前，您将编写如下所示的代码:

```java
sendEmail(new Runnable() {
            @Override
            public void run() {
                System.out.println("Sending email...");
            }
        });
```

 `sendEmail` 将被声明成这样.

```java
public static void sendEmail(Runnable runnable)
```

上述代码的问题不仅在于我们必须封装我们的操作，即在对象中运行方法，而且更大的问题是它错过了程序员的意图，即将行为传递给`sendEmail`函数。如果您使用像Guava这样的库，那么您肯定会感受到编写匿名类的痛苦。下面显示了一个简单的例子，用标题中的**lambda**来过滤所有任务。

```java
Iterable<Task> lambdaTasks = Iterables.filter(tasks, new Predicate<Task>() {
            @Override
            public boolean apply(Task task) {
                return input.getTitle().contains("lambda");
            }
});
```

使用Java 8 Stream API，您可以编写上述代码，而无需使用像Guava这样的第三方库。我们将在[chapter3](./ 03-streams.md)中介绍流。所以，请继续关注！

## Java 8 Lambda 表达式

在Java 8中，我们将使用lambda表达式编写代码，如下所示。我们在上面的代码片段中提到了相同的示例。

```java
sendEmail(() -> System.out.println("Sending email..."));
```

上面显示的代码简洁明了，不会污染程序员通过行为的意图。 （）用于表示lambda没有函数参数，即Runnable接口run方法没有任何参数.->是lambda操作符，用于将参数与函数体分开，打印发送电子邮件...到标准输出。

让我们看看再看一下Collections.sort示例，以便我们可以理解howlambda表达式如何使用参数。为了按名称列出名称列表，我们将`Comparator`传递给sort函数。`Comparator`如下所示。

```java
Comparator<String> comparator = (first, second) -> first.length() - second.length();
```

我们写的lambda表达式对应于Comparator接口中的`compare`方法。`compare`函数的签名如下所示。

```java
int compare(T o1, T o2);
```

在lambda表达式中，我们不需要显式地提供类型——String。“javac”编译器从上下文推断类型信息。
Java编译器推断，在排序时，两个参数都应该是字符串字符串列表，' compare '方法只指定一种类型' T '。该法案
以这种方式从上下文中推断类型的方法称为**类型推断**。Java 8改进了Java中已经存在的类型推断系统，并使之成为可能
更健壮和强大的支持lambda表达式。“javac”查找接近lambda表达式的信息并使用它查找参数的正确类型的信息。

> In most cases, `javac` will infer the type from the context. In case it can't
> resolve type because of missing or incomplete context information then the
> code will not compile. For example, if we remove `String` type information
> from `Comparator` then code will fail to compile as shown below.

```java
Comparator comparator = (first, second) -> first.length() - second.length(); // compilation error - Cannot resolve method 'length()'
```

## How do Lambda expressions work in Java 8?

You may have noticed that the type of a lambda expression is some interface like
`Comparator` in the above example. You can't use any arbitrary interface with
lambda expressions. ***Only those interfaces which have only one non-object
abstract method can be used with lambda expressions***. These kinds of
interfaces are called **Functional interfaces**, and they can be annotated with
the `@FunctionalInterface` annotation. `Runnable` interface is an example of a
functional interface, as shown below.

```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

`@FunctionalInterface` annotation is not mandatory but, it can help tools know
that an interface is a functional interface and perform meaningful actions. For
example, if you try to compile an interface that annotates itself with the
`@FunctionalInterface` annotation, and has multiple abstract methods, then
compilation will fail with an error ***Multiple non-overriding abstract methods
found***. Similarly, if you add `@FunctionInterface` annotation to an interface
without any method, i.e. a marker interface, then you will get error message
***No target method found***.

Let's answer one of the most important questions that might be coming to your
mind. ***Are Java 8 lambda expressions just the syntactic sugar over anonymous
inner classes, or how do functional interfaces otherwise get translated to
bytecode?***

The short answer is **NO**. Java 8 does not use anonymous inner classes mainly
for two reasons:

1. **Performance impact**: If lambda expressions were implemented using
anonymous classes, then each lambda expression would result in a class file on
disk. If these classes were loaded by the JVM at startup, then the startup time
of the JVM would increase, as all the classes would need to be first loaded and
verified before use.

2. **Possibility to change in future**: If Java 8 designers would have used
anonymous classes from the start, then it would have limited the scope of future
lambda implementation changes.

### Using invokedynamic

Java 8 designers decided to use the `invokedynamic` instruction, added in Java
7, to defer the translation strategy at runtime. When `javac` compiles the code,
it captures the lambda expression and generates an `invokedynamic` call site
(called lambda factory). The `invokedynamic` call site, when invoked, returns an
instance of the functional interface to which the lambda is being converted. For
example, if we look at the byte code of our Collections.sort example, it will
look like as shown below.

```
public static void main(java.lang.String[]);
    Code:
       0: iconst_3
       1: anewarray     #2                  // class java/lang/String
       4: dup
       5: iconst_0
       6: ldc           #3                  // String shekhar
       8: aastore
       9: dup
      10: iconst_1
      11: ldc           #4                  // String rahul
      13: aastore
      14: dup
      15: iconst_2
      16: ldc           #5                  // String sameer
      18: aastore
      19: invokestatic  #6                  // Method java/util/Arrays.asList:([Ljava/lang/Object;)Ljava/util/List;
      22: astore_1
      23: invokedynamic #7,  0              // InvokeDynamic #0:compare:()Ljava/util/Comparator;
      28: astore_2
      29: aload_1
      30: aload_2
      31: invokestatic  #8                  // Method java/util/Collections.sort:(Ljava/util/List;Ljava/util/Comparator;)V
      34: getstatic     #9                  // Field java/lang/System.out:Ljava/io/PrintStream;
      37: aload_1
      38: invokevirtual #10                 // Method java/io/PrintStream.println:(Ljava/lang/Object;)V
      41: return
}
```

The interesting part of the byte code shown above is the line 23
`23: invokedynamic #7,  0              // InvokeDynamic #0:compare:()Ljava/util/Comparator;`
where a call to `invokedynamic` is made.

The second step is to convert the body of the lambda expression into a method
that will be invoked through the `invokedynamic` instruction. This is the step
where JVM implementers have the liberty to choose their own strategy.

I have only glossed over this topic. You can read about the internals at
http://cr.openjdk.java.net/~briangoetz/lambda/lambda-translation.html.

## Anonymous classes vs lambdas

Let's compare anonymous classes with lambdas to understand the differences
between them.

1. In anonymous classes, `this` refers to the anonymous class itself whereas in
lambda expressions, `this` refers to the class enclosing the lambda expression.

2. You can shadow variables in the enclosing class inside the anonymous class.
This gives a compile time error when done inside a lambda expression.

3. The type of the lambda expression is determined from the context, whereas the
type of the anonymous class is specified explicitly as you create the instance
of anonymous class.

## Do I need to write my own functional interfaces?

By default, Java 8 comes with many functional interfaces which you can use in
your code. They exist inside `java.util.function` package. Let's have a look at
few of them.

### java.util.function.Predicate<T>

This functional interface is used to define a check for some condition, i.e. a
predicate. Predicate interface has one method called `test` which takes a value
of type `T` and returns boolean. For example, from a list of `names` if we want
to filter out all the names which starts with **s** then we will use a predicate
as shown below.

```java
Predicate<String> namesStartingWithS = name -> name.startsWith("s");
```

### java.util.function.Consumer<T>

This functional interface is used for performing actions which do not produce
any output. The consumer interface has one method called `accept` which takes a
value of type `T` and returns nothing, i.e. it is void. For example, sending an
email with given message.

```java
Consumer<String> messageConsumer = message -> System.out.println(message);
```

### java.util.function.Function<T,R>

This functional interface takes one value and produces a result. For example, if
we want to uppercase all the names in our `names` list, we can write a Function
as shown below.

```java
Function<String, String> toUpperCase = name -> name.toUpperCase();
```

### java.util.function.Supplier<T>

This functional interface does not take any input but produces a value. This
could be used to generate unique identifiers as shown below.

```java
Supplier<String> uuidGenerator= () -> UUID.randomUUID().toString();
```

We will cover more functional interfaces throughout this tutorial.

## Method references

There would be times when you will be creating lambda expressions that only
calls a specific method like `Function<String, Integer> strToLength = str ->
str.length();`. The lambda only calls `length()` method on the `String` object.
This could be simplified using method references like `Function<String, Integer>
strToLength = String::length;`. They can be seen as shorthand notation for
lambda expression that only calls a single method. In the expression
`String::length`, `String` is the target reference, `::` is the delimiter, and
`length` is the function that will be called on the target reference. You can
use method references on both static and instance methods.

### Static method references

Suppose we have to find a maximum number from a list of numbers, then we can
write a method reference `Function<List<Integer>, Integer> maxFn =
Collections::max`. `max` is a static method in the `Collections` class that
takes one argument of type `List`. You can then call this like
`maxFn.apply(Arrays.asList(1, 10, 3, 5))`. The above lambda expression is
equivalent to a `Function<List<Integer>, Integer> maxFn = (numbers) ->
Collections.max(numbers);` lambda expression.

### Instance method references

This is used for method reference to an instance method, for example
`String::toUpperCase` calls `toUpperCase` method on a `String` reference. You
can also use method reference with parameters for example `BiFunction<String,
String, String> concatFn = String::concat`. The `concatFn` can be called as
`concatFn.apply("shekhar", "gulati")`. The `String` `concat` method is called on
a String object and passed a parameter like `"shekhar".concat("gulati")`.

## Exercise >> Lambdify me

Let's look at the code shown below and apply what we have learned so far.

```java
public class Exercise_Lambdas {

    public static void main(String[] args) {
        List<Task> tasks = getTasks();
        List<String> titles = taskTitles(tasks);
        for (String title : titles) {
            System.out.println(title);
        }
    }

    public static List<String> taskTitles(List<Task> tasks) {
        List<String> readingTitles = new ArrayList<>();
        for (Task task : tasks) {
            if (task.getType() == TaskType.READING) {
                readingTitles.add(task.getTitle());
            }
        }
        return readingTitles;
    }

}
```

The code shown above first fetches all the Tasks from a utility method
`getTasks`. We are not interested in the `getTasks` implementation. The
`getTasks` could fetch tasks by accessing a web-service or database or
in-memory. Once you have tasks, we filter all the reading tasks and extract the
title field from the task. We add extracted title to a list and then finally
return all the reading titles.

Let's start with the simplest refactor -- using `foreach` on a list with method
reference.

```java
public class Exercise_Lambdas {

    public static void main(String[] args) {
        List<Task> tasks = getTasks();
        List<String> titles = taskTitles(tasks);
        titles.forEach(System.out::println);
    }

    public static List<String> taskTitles(List<Task> tasks) {
        List<String> readingTitles = new ArrayList<>();
        for (Task task : tasks) {
            if (task.getType() == TaskType.READING) {
                readingTitles.add(task.getTitle());
            }
        }
        return readingTitles;
    }

}
```

Using `Predicate<T>` to filter out tasks.

```java
public class Exercise_Lambdas {

    public static void main(String[] args) {
        List<Task> tasks = getTasks();
        List<String> titles = taskTitles(tasks, task -> task.getType() == TaskType.READING);
        titles.forEach(System.out::println);
    }

    public static List<String> taskTitles(List<Task> tasks, Predicate<Task> filterTasks) {
        List<String> readingTitles = new ArrayList<>();
        for (Task task : tasks) {
            if (filterTasks.test(task)) {
                readingTitles.add(task.getTitle());
            }
        }
        return readingTitles;
    }

}
```

Using `Function<T,R>` for extracting out title from the Task.

```java
public class Exercise_Lambdas {

    public static void main(String[] args) {
        List<Task> tasks = getTasks();
        List<String> titles = taskTitles(tasks, task -> task.getType() == TaskType.READING, task -> task.getTitle());
        titles.forEach(System.out::println);
    }

    public static <R> List<R> taskTitles(List<Task> tasks, Predicate<Task> filterTasks, Function<Task, R> extractor) {
        List<R> readingTitles = new ArrayList<>();
        for (Task task : tasks) {
            if (filterTasks.test(task)) {
                readingTitles.add(extractor.apply(task));
            }
        }
        return readingTitles;
    }
}
```

Using method reference for extractor

```java
public class Exercise_Lambdas {

    public static void main(String[] args) {
        List<Task> tasks = getTasks();
        List<String> titles = filterAndExtract(tasks, task -> task.getType() == TaskType.READING, Task::getTitle);
        titles.forEach(System.out::println);
        List<LocalDate> createdOnDates = filterAndExtract(tasks, task -> task.getType() == TaskType.READING, Task::getCreatedOn);
        createdOnDates.forEach(System.out::println);
        List<Task> filteredTasks = filterAndExtract(tasks, task -> task.getType() == TaskType.READING, Function.identity());
        filteredTasks.forEach(System.out::println);
    }

    public static <R> List<R> filterAndExtract(List<Task> tasks, Predicate<Task> filterTasks, Function<Task, R> extractor) {
        List<R> readingTitles = new ArrayList<>();
        for (Task task : tasks) {
            if (filterTasks.test(task)) {
                readingTitles.add(extractor.apply(task));
            }
        }
        return readingTitles;
    }
}
```

We can also write our own **Functional Interface** that clearly describes the
intent of the developer. We can create an interface `TaskExtractor` that extends
`Function` interface. The input type of interface is fixed to `Task` and output
type depend on the implementing lambda. This way the developer will only have to
worry about the result type, as input type will always remain `Task`.

```java
public class Exercise_Lambdas {

    public static void main(String[] args) {
        List<Task> tasks = getTasks();
        List<Task> filteredTasks = filterAndExtract(tasks, task -> task.getType() == TaskType.READING, TaskExtractor.identityOp());
        filteredTasks.forEach(System.out::println);
    }

    public static <R> List<R> filterAndExtract(List<Task> tasks, Predicate<Task> filterTasks, TaskExtractor<R> extractor) {
        List<R> readingTitles = new ArrayList<>();
        for (Task task : tasks) {
            if (filterTasks.test(task)) {
                readingTitles.add(extractor.apply(task));
            }
        }
        return readingTitles;
    }

}


interface TaskExtractor<R> extends Function<Task, R> {

    static TaskExtractor<Task> identityOp() {
        return t -> t;
    }
}
```

[![Analytics](https://ga-beacon.appspot.com/UA-59411913-3/shekhargulati/java8-the-missing-tutorial/02-lambdas)](https://github.com/igrigorik/ga-beacon)
