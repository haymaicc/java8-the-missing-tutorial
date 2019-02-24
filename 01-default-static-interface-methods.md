给接口提供默认的静态方法
--------

我们都知道应该面向接口编程，接口给客户端一个方法定义而不需要提供实现的细节（例：实现类），于是使我们的程序变得更加松耦合。设计一个简洁的接口是API设计最重要的方面之一。SOLID(面向对象设计)原则，其中一项接口隔离就是说要设计一个小而独立的接口，而不是定义一个粗而通用的接口。接口设计是为你的应用开发高效整洁的API的关键因素。

> 本节的代码在这里 [ch01 package](https://github.com/shekhargulati/java8-the-missing-tutorial/tree/master/code/src/main/java/com/shekhargulati/java8_tutorial/ch01).

如果你定义了定义了一个API，那么随着时间的推移，你可能需要添加API的新方法。一旦发布API，就很难添加方法到接口而不破坏现有的实现。举个例子，假设你正在开发一个简单的计算器支持加，减，除，乘操作。我们可以写一个`Calculator`API，如下图所示。 ***为了简单起见，我们将使用int。***

```java
public interface Calculator {

    int add(int first, int second);

    int subtract(int first, int second);

    int divide(int number, int divisor);

    int multiply(int first, int second);
}
```

为了支持`Calculator`接口，你创建了一个`BasicCalculator`实现类，代码如下：

```java
public class BasicCalculator implements Calculator {

    @Override
    public int add(int first, int second) {
        return first + second;
    }

    @Override
    public int subtract(int first, int second) {
        return first - second;
    }

    @Override
    public int divide(int number, int divisor) {
        if (divisor == 0) {
            throw new IllegalArgumentException("divisor can't be zero.");
        }
        return number / divisor;
    }

    @Override
    public int multiply(int first, int second) {
        return first * second;
    }
}
```

## 静态工厂方法

假设`Calculator` API原来是非常有用且易于使用的。用户只需创建一个`BasicCalculator`的实例，然后他们就可以使用了。你开始看到如下所示的代码。

```java
Calculator calculator = new BasicCalculator();
int sum = calculator.add(1, 2);

BasicCalculator cal = new BasicCalculator();
int difference = cal.subtract(3, 2);
```

不幸的是，用户正在对实现类进行编码而不是面向接口编程。你的API没有强制用户必须面向接口编程，因为`BasicCalculator`的申明是public 的，如果你让`BasicCalculator`变成受包保护，那么你不得不提供一个工厂类来负责实现`Calculator` 的实例化，接下来让我们改进代码来解决上述的问题：

首先，我们将`BasicCalculator`  变成包私有，以便用户不能直接访问改类。

```java
class BasicCalculator implements Calculator {
  // rest remains same
}
```

接下来我们编写一个工厂类来提供`Calculator` 的实例化，代码如下。

```java
public abstract class CalculatorFactory {

    public static Calculator getInstance() {
        return new BasicCalculator();
    }
}
```

现在用户将被强制面向我们的`Calculator` 编程，而且我们不透露任何实现细节。

虽然我们已经实现了我们的目标，但我们不得不增加我们的复杂性，通过新建`CalculatorFactory`类。现在我们API的用户需要了解更多的类才能高效的使用我们的API。这是Java 8 之前的解决方案。

**Java 8  允许我们在接口中申明静态方法。**这允许我们API设计者在接口本身定义一个静态的工具方法，例如`getInstance` 。因此保持了接口的高效整洁。一个接口内的静态方法可以用来替换静态帮助类（`CalculatorFactory`），我们通常创建它来定义相关的帮助方法。例如，`Collections`类是一个帮助类，它定义了各种帮助方法来处理Collection及其关联的方法接口。 `Collections`类中定义的方法可以很容易地添加`Collection` 或其任何子接口

通过Java 8 我们可以将上面的代码改造为：

```java
public interface Calculator {

    static Calculator getInstance() {
        return new BasicCalculator();
    }

    int add(int first, int second);

    int subtract(int first, int second);

    int divide(int number, int divisor);

    int multiply(int first, int second);

}
```

## 不断拓展的API

 一些接口使用者决定通过添加方法来扩展`Calculator` API 例如`remainder`方法，或编写他们自己的`Calculator` 的实现 接口。在与您的用户交谈之后，您了解到他们中的大多数人都愿意 喜欢将一个`remainder`方法添加到`Calculator` 接口。它看起来 一个非常简单的API更改，因此您向API添加了一个的方法。 

```java
public interface Calculator {

    static Calculator getInstance() {
        return new BasicCalculator();
    }

    int add(int first, int second);

    int subtract(int first, int second);

    int divide(int number, int divisor);

    int multiply(int first, int second);

    int remainder(int number, int divisor); // new method added to API
}
```

向接口添加方法破坏了API兼容性。这个意味着正在使用`Calculator` 接口的用户将不得不添加`remainder` 方法的实现，否则他们的代码无法成功编译。对于API设计人员来说，这是一个很大的问题，因为它使API变得难以拓展。在Java 8之前，接口内部不能有方法实现。当API需要扩展一个方法时，这往往让人头疼。。为了支持API随着时间的推移而拓展，Java 8允许用户在接口中提供默认实现的方法。这些被称为默认或防御方法。实现接口的类不是需要提供这些方法的实现。如果一个实施类提供了实现，然后是实现类的方法实现将被使用 - 否则将使用默认实现。`List`接口定义了几个默认方法，如`replaceAll`，`sort`和`splitIterator`。

```java
default void replaceAll(UnaryOperator<E> operator) {
    Objects.requireNonNull(operator);
    final ListIterator<E> li = this.listIterator();
    while (li.hasNext()) {
        li.set(operator.apply(li.next()));
    }
}
```

我们可以通过定义一个默认方法来解决我们的API问题，如下所示。默认方法可以用已经存在的方法来定义  --

`remainder` 使用`subtract`，`multiply`和`divide` 方法来定义。

```java
default int remainder(int number, int divisor) {
    return subtract(number, multiply(divisor, divide(number, divisor)));
}
```

## 多重继承

一个类只能继承一个父类，但能实现多个接口。现在在Java中有接口的方法实现多重行为继承是可行的。 Java已经有了类型级别的多重继承，但现在它在行为级别上也有多重继承。有三个解决规则可以帮助决定选择哪种方法：

**Rule 1: 在类中声明的方法胜过在接口中定义的方法**

```java
interface A {
    default void doSth(){
        System.out.println("inside A");
    }
}
class App implements A{

    @Override
    public void doSth() {
        System.out.println("inside App");
    }

    public static void main(String[] args) {
        new App().doSth();
    }
}
```

This will print `inside App`, as methods declared in the implementing class have
precedence over methods declared in interfaces.

**Rule 2:否则，选择最具体的接口**

```java
interface A {
    default void doSth() {
        System.out.println("inside A");
    }
}
interface B {}
interface C extends A {
    default void doSth() {
        System.out.println("inside C");
    }
}
class App implements C, B, A {

    public static void main(String[] args) {
        new App().doSth();
    }
}
```

结果是 `inside C`.

**Rule 3: 否则，该类必须明确地调用所需的实现**

```java
interface A {
    default void doSth() {
        System.out.println("inside A");
    }
}
interface B {
    default void doSth() {
        System.out.println("inside B");
    }
}
class App implements B, A {

    @Override
    public void doSth() {
        B.super.doSth();
    }

    public static void main(String[] args) {
      new App().doSth();
    }
}
```
结果是 `inside B`.

[![Analytics](https://ga-beacon.appspot.com/UA-59411913-3/shekhargulati/java8-the-missing-tutorial/01-default-static-interface-methods)](https://github.com/igrigorik/ga-beacon)
