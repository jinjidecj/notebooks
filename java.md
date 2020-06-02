# 零碎的知识点

## 1.访问权限

三个显示关键字设置类中的访问权限：public private protected

如果没有使用上述三个关键字设置权限，默认为default访问权限，则称为**包访问**，只有同一个包下的其他类成员可以访问。

## 2.组合和聚合

组合：整体删除，部件一定删除

聚合：整体删除，部件不会删除

## 3.“是一个”与“像是一个”

**是一个----纯粹替代**：子类继承父类，不添加新方法，is-a

**像是一个----导出类添加新方法**：子类继承父类后增加新的方法，添加新的接口，is-like-a

14.25

​                    95649.28

97522.78     97522.78

## 4.前期绑定，后期绑定

前期绑定：编译器生成对特定函数名的调用，改调用会被解析为将执行的代码的绝对地址

后期绑定：程序运行到函数调用时才能确定代码的地址，编译器需要确保方法存在，并且对参数和返回值进行类型检查，但不知要执行的确切代码。（多态）

## 5.向上转型

子类当做其父类来处理的过程叫向上转型。

**向下转型**：强制类型转换（从父类到子类）

## 6.基本类型的存储

new 出来的对象保存在堆内存中，不用new的变量直接存储在栈内存中，更加高效。

## 7. finalize()

垃圾回收器只知道如何释放new创建的对象的内存，不知道如何回收不是new分配的内存。

而在 Java 中，对象并非总是被垃圾回收，或者换句话说：

1. 对象可能不被垃圾回收。
2. 垃圾回收不等同于析构。

当垃圾回收器准备回收对象的内存时，会先调用其finalize()方法。

## 8. 垃圾回收器如何工作

+ 引用计数：每个对象含有一个引用计数器，计数为0则清除对象，在出现循环引用时其计数都不为0，会出现无法回收的情况，因此这种工作方式未被使用

+ 自适应：

  + 停止-复制：这需要先暂停程序的运行（不属于后台回收模式），然后将所有存活的对象从当前堆复制到另一个堆，没有复制的就是需要被垃圾回收的。另外，当对象被复制到新堆时，它们是一个挨着一个紧凑排列，然后就可以按照前面描述的那样简单、直接地分配新空间了。

    这种所谓的"复制回收器"效率低下主要因为两个原因。其一：得有两个堆，然后在这两个分离的堆之间来回折腾，得维护比实际需要多一倍的空间。某些 Java 虚拟机对此问题的处理方式是，按需从堆中分配几块较大的内存，复制动作发生在这些大块内存之间。

    其二在于复制本身。一旦程序进入稳定状态之后，可能只会产生少量垃圾，甚至没有垃圾。尽管如此，复制回收器仍然会将所有内存从一处复制到另一处，这很浪费。为了避免这种状况，一些 Java 虚拟机会进行检查：要是没有新垃圾产生，就会转换到另一种模式（即"自适应"）。这种模式称为标记-清扫（mark-and-sweep），Sun 公司早期版本的 Java 虚拟机一直使用这种技术。对一般用途而言，"标记-清扫"方式速度相当慢，但是当你知道程序只会产生少量垃圾甚至不产生垃圾时，它的速度就很快了。

  + "标记-清扫"：从栈和静态存储区出发，遍历所有的引用，找出所有存活的对象。但是，每当找到一个存活对象，就给对象设一个标记，并不回收它。只有当标记过程完成后，清理动作才开始。在清理过程中，没有标记的对象将被释放，不会发生任何复制动作。"标记-清扫"后剩下的堆空间是不连续的，垃圾回收器要是希望得到连续空间的话，就需要重新整理剩下的对象。

## 9.可变参数列表

```java
public class NewVarArgs {
    static void printArray(Object... args) {
        for (Object obj: args) {
            System.out.print(obj + " ");
        }
        System.out.println();
    }

    public static void main(String[] args) {
        // Can take individual elements:
        printArray(47, (float) 3.14, 11.11);
        printArray(47, 3.14F, 11.11);
        printArray("one", "two", "three");
        printArray(new A(), new A(), new A());
        // Or an array:
        printArray((Object[]) new Integer[] {1, 2, 3, 4});
        printArray(); // Empty list is OK
    }
}
```

有了可变参数，你就再也不用显式地编写数组语法了，当你指定参数时，编译器实际上会为你填充数组。你获取的仍然是一个数组

## 10.final

类中所有的 **private** 方法都隐式地指定为 **final**。因为不能访问 **private** 方法，所以不能覆写它。

当说一个类是 **final** （**final** 关键字在类定义之前），就意味着它不能被继承。

Java 中除了 **static** 和 **final** 方法（**private** 方法也是隐式的 **final**）外，其他所有方法都是后期绑定。这意味着通常情况下，我们不需要判断后期绑定是否会发生——它自动发生。

## 11.类初始化和加载

每个类的编译代码都存在于它自己独立的文件中。该文件只有在使用程序代码时才会被加载。一般可以说“类的代码在首次使用时加载“。这通常是指创建类的第一个对象，或者是访问了类的 **static** 属性或方法。构造器也是一个 **static** 方法尽管它的 **static** 关键字是隐式的。因此，准确地说，一个类当它任意一个 **static** 成员被访问时，就会被加载。

首次使用时就是 **static** 初始化发生时。所有的 **static** 对象和 **static** 代码块在加载时按照文本的顺序（在类中定义的顺序）依次初始化。**static** 变量只被初始化一次。

**初始化过程：**

初始化一个类时，如果他有继承基类，不论是否创建了基类的对象，基类都会被加载，以此类推，当加载完成后，开始执行根基类的static，以此类推。

必要的类加载完成后，开始创建对象。对象中的所有基本类型变量都被置为默认值，对象引用被设为 **null** —— 这是通过将对象内存设为二进制零值一举生成的。接着会调用基类的构造器。

基类构造器和派生类构造器一样以相同的顺序经历相同的过程。当基类构造器完成后，实例变量按文本顺序初始化。最终，构造器的剩余部分被执行。

## 12.多态

只有普通的方法调用可以是多态的

如果一个方法是静态(**static**)的，它的行为就不具有多态性

## 13.接口

关键字 **default** 允许在接口中提供方法实现——在 Java 8 之前被禁止。

```java
// interfaces/InterfaceWithDefault.java
interface InterfaceWithDefault {
    void firstMethod();
    void secondMethod();

    default void newMethod() {
        System.out.println("newMethod");
    }
}
```

这样可以不用实现newMethod方法而使用它。

允许在接口中添加静态方法

| 特性                 | 接口                                                       | 抽象类                                   |
| -------------------- | ---------------------------------------------------------- | ---------------------------------------- |
| 组合                 | 新类可以组合多个接口                                       | 只能继承单一抽象类                       |
| 状态                 | 不能包含属性（除了静态属性，不支持对象状态）               | 可以包含属性，非抽象方法可能引用这些属性 |
| 默认方法 和 抽象方法 | 不需要在子类中实现默认方法。默认方法可以引用其他接口的方法 | 必须在子类中实现抽象方法                 |
| 构造器               | 没有构造器                                                 | 可以有构造器                             |
| 可见性               | 隐式 **public**                                            | 可以是 **protected** 或友元              |

接口中的字段都自动是 **static** 和 **final** 的

接口可以嵌套在类或其他接口中，像非嵌套接口一样，它们具有 **public** 或包访问权限的可见性。

## 14.内部类

当生成一个内部类的对象时，此对象与制造它的外围对象（enclosing object）之间就有了一种联系，所以它能访问其外围对象的所有成员，而不需要任何特殊条件。此外，内部类还拥有其外围类的所有元素的访问权。

**.new**  

```java
// innerclasses/DotNew.java
// Creating an inner class directly using .new syntax
public class DotNew {
    public class Inner {}
    public static void main(String[] args) {
        DotNew dn = new DotNew();
        DotNew.Inner dni = dn.new Inner();
    }
}
```

要想直接创建内部类的对象，你不能按照你想象的方式，去引用外部类的名字 **DotNew**，而是必须使用外部类的对象来创建该内部类对象

在拥有外部类对象之前是不可能创建内部类对象的。这是因为内部类对象会暗暗地连接到建它的外部类对象上。但是，如果你创建的是嵌套类（静态内部类），那么它就不需要对外部类对象的引用。

**匿名内部类**

```java
// innerclasses/Parcel7.java
// Returning an instance of an anonymous inner class
public class Parcel7 {
    public Contents contents() {
        return new Contents() { // Insert class definition
            private int i = 11;

            @Override
            public int value() { return i; }
        }; // Semicolon required
    }

    public static void main(String[] args) {
        Parcel7 p = new Parcel7();
        Contents c = p.contents();
    }
}
```

“创建一个继承自 **Contents** 的匿名类的对象。”通过 **new** 表达式返回的引用被自动向上转型为对 **Contents** 的引用。

如果你的基类需要一个有参数的构造器：

```java
// innerclasses/Parcel8.java
// Calling the base-class constructor
public class Parcel8 {
    public Wrapping wrapping(int x) {
        // Base constructor call:
        return new Wrapping(x) { // [1]
            @Override
            public int value() {
                return super.value() * 47;
            }
        }; // [2]
    }
    public static void main(String[] args) {
        Parcel8 p = new Parcel8();
        Wrapping w = p.wrapping(10);
    }
}
```

- [1] 将合适的参数传递给基类的构造器。
- [2] 在匿名内部类末尾的分号，并不是用来标记此内部类结束的。实际上，它标记的是表达式的结束，只不过这个表达式正巧包含了匿名内部类罢了。因此，这与别的地方使用的分号是一致的。

如果定义一个匿名内部类，并且希望它使用一个在其外部定义的对象，那么编译器会要求其参数引用是 **final** 的

## 15.嵌套类

如果不需要内部类对象与其外围类对象之间有联系，那么可以将内部类声明为 **static**，这通常称为嵌套类。想要理解 **static** 应用于内部类时的含义，就必须记住，普通的内部类对象隐式地保存了一个引用，指向创建它的外围类对象。然而，当内部类是 **static** 的时，就不是这样了。嵌套类意味着：

1. 要创建嵌套类的对象，并不需要其外围类的对象。
2. 不能从嵌套类的对象中访问非静态的外围类对象。

嵌套类与普通的内部类还有一个区别。普通内部类的字段与方法，只能放在类的外部层次上，所以普通的内部类不能有 **static** 数据和 **static** 字段，也不能包含嵌套类。但是嵌套类可以包含所有这些东西

嵌套类就没有这个特殊的 **this** 引用，这使得它类似于一个 **static** 方法。

**接口内部的类**

嵌套类可以作为接口的一部分。你放到接口中的任何类都自动地是 **public** 和 **static** 的。因为类是 **static** 的，只是将嵌套类置于接口的命名空间内，这并不违反接口的规则。你甚至可以在内部类中实现其外围接口，就像下面这样：

```java
// innerclasses/ClassInInterface.java
// {java ClassInInterface$Test}
public interface ClassInInterface {
    void howdy();
    class Test implements ClassInInterface {
        @Override
        public void howdy() {
            System.out.println("Howdy!");
        }
        public static void main(String[] args) {
            new Test().howdy();
        }
    }
}
```

如果你想要创建某些公共代码，使得它们可以被某个接口的所有不同实现所共用，那么使用接口内部的嵌套类会显得很方便。

一个内部类被嵌套多少层并不重要——它能透明地访问所有它所嵌入的外围类的所有成员

## 16.集合

**ArrayList** 和 **LinkedList** 都是 **List** 的类型，从输出中可以看出，它们都按插入顺序保存元素。两者之间的区别不仅在于执行某些类型的操作时的性能，而且 **LinkedList** 包含的操作多于 **ArrayList** 。本章后面将对这些内容进行更全面的探讨。

**HashSet** ， **TreeSet** 和 **LinkedHashSet** 是 **Set** 的类型。从输出中可以看到， **Set** 仅保存每个相同项中的一个，并且不同的 **Set** 实现存储元素的方式也不同。 **HashSet** 使用相当复杂的方法存储元素，这种技术是检索元素的最快方法，因此，存储顺序看上去没有什么意义（通常只关心某事物是否是 **Set** 的成员，而存储顺序并不重要）。如果存储顺序很重要，则可以使用 **TreeSet** ，它将按比较结果的升序保存对象）或 **LinkedHashSet** ，它按照被添加的先后顺序保存对象。

**Map** （也称为*关联数组*）使用*键*来查找对象，就像一个简单的数据库。所关联的对象称为*值*。 对于每个键， **Map** 只存储一次。

键和值保存在 **HashMap** 中的顺序不是插入顺序，因为 **HashMap** 实现使用了非常快速的算法来控制顺序。 **TreeMap** 通过比较结果的升序来保存键， **LinkedHashMap** 在保持 **HashMap** 查找速度的同时按键的插入顺序保存键。

**List:**

可以使用 `contains()` 方法确定对象是否在列表中。如果要删除一个对象，可以将该对象的引用传递给 `remove()` 方法。同样，如果有一个对象的引用，可以使用 `indexOf()` 在 **List** 中找到该对象所在位置的下标号

当确定元素是否是属于某个 **List** ，寻找某个元素的索引，以及通过引用从 **List** 中删除元素时，都会用到 `equals()` 方法（根类 **Object** 的一个方法）

对于 **LinkedList** ，在列表中间插入和删除都是廉价操作（在本例中，除了对列表中间进行的真正的随机访问），但对于 **ArrayList** ，这可是代价高昂的操作。

**迭代器**

*迭代器*（也是一种设计模式）的概念实现了这种抽象。迭代器是一个对象，它在一个序列中移动并选择该序列中的每个对象，而客户端程序员不知道或不关心该序列的底层结构。另外，迭代器通常被称为*轻量级对象*（lightweight object）：创建它的代价小。因此，经常可以看到一些对迭代器有些奇怪的约束。例如，Java 的 **Iterator** 只能单向移动。这个 **Iterator** 只能用来：

1. 使用 `iterator()` 方法要求集合返回一个 **Iterator**。 **Iterator** 将准备好返回序列中的第一个元素。
2. 使用 `next()` 方法获得序列中的下一个元素。
3. 使用 `hasNext()` 方法检查序列中是否还有元素。
4. 使用 `remove()` 方法将迭代器最近返回的那个元素删除。

**ListIterator** 是一个更强大的 **Iterator** 子类型，它只能由各种 **List** 类生成。 **Iterator** 只能向前移动，而 **ListIterator** 可以双向移动。它可以生成迭代器在列表中指向位置的后一个和前一个元素的索引，并且可以使用 `set()` 方法替换它访问过的最近一个元素。可以通过调用 `listIterator()` 方法来生成指向 **List** 开头处的 **ListIterator** ，还可以通过调用 `listIterator(n)` 创建一个一开始就指向列表索引号为 **n** 的元素处的 **ListIterator** 。

## 17.Lambda表达式

Lambda 表达式是使用**最小可能**语法编写的函数定义：

1. Lambda 表达式产生函数，而不是类。 在 JVM（Java Virtual Machine，Java 虚拟机）上，一切都是一个类，因此在幕后执行各种操作使 Lambda 看起来像函数 —— 但作为程序员，你可以高兴地假装它们“只是函数”。
2. Lambda 语法尽可能少，这正是为了使 Lambda 易于编写和使用。

Lambda 表达式的基本语法是：

1. 参数。

2. 接着 `->`，可视为“产出”。

3. `->` 之后的内容都是方法体。

   - **[1]** 当只用一个参数，可以不需要括号 `()`。 然而，这是一个特例。

   - **[2]** 正常情况使用括号 `()` 包裹参数。 为了保持一致性，也可以使用括号 `()` 包裹单个参数，虽然这种情况并不常见。

   - **[3]** 如果没有参数，则必须使用括号 `()` 表示空参数列表。

   - **[4]** 对于多个参数，将参数列表放在括号 `()` 中。

     到目前为止，所有 Lambda 表达式方法体都是单行。 该表达式的结果自动成为 Lambda 表达式的返回值，在此处使用 **return** 关键字是非法的。 这是 Lambda 表达式缩写用于描述功能的语法的另一种方式。

   + **[5]** 如果在 Lambda 表达式中确实需要多行，则必须将这些行放在花括号中。 在这种情况下，就需要使用 **return**。

Lambda 表达式通常比**匿名内部类**产生更易读的代码

```java
// functional/LambdaExpressions.java

interface Description {
  String brief();
}

interface Body {
  String detailed(String head);
}

interface Multi {
  String twoArg(String head, Double d);
}

public class LambdaExpressions {

  static Body bod = h -> h + " No Parens!"; // [1]

  static Body bod2 = (h) -> h + " More details"; // [2]

  static Description desc = () -> "Short info"; // [3]

  static Multi mult = (h, n) -> h + n; // [4]

  static Description moreLines = () -> { // [5]
    System.out.println("moreLines()");
    return "from moreLines()";
  };

  public static void main(String[] args) {
    System.out.println(bod.detailed("Oh!"));
    System.out.println(bod2.detailed("Hi!"));
    System.out.println(desc.brief());
    System.out.println(mult.twoArg("Pi! ", 3.14159));
    System.out.println(moreLines.brief());
  }
}
```

```java
interface IntCall {
  int call(int arg);
}
// functional/RecursiveFactorial.java
递归
public class RecursiveFactorial {
  static IntCall fact;
  public static void main(String[] args) {
    fact = n -> n == 0 ? 1 : n * fact.call(n - 1);
    for(int i = 0; i <= 10; i++)
      System.out.println(fact.call(i));
  }
}
```

方法引用：

方法引用组成：类名或对象名，后面跟 `::` ，然后跟方法名称。

## 18.异常

**多重捕获**

通过 Java 7 的多重捕获机制，你可以使用“或”将不同类型的异常组合起来，只需要一行 catch 语句：

```java
// exceptions/MultiCatch.java
public class MultiCatch {
    void x() throws Except1, Except2, Except3, Except4 {}
    void process() {}
    void f() {
        try {
            x();

        } catch(Except1 | Except2 | Except3 | Except4 e) {
            process();
        }
    }
}复制ErrorOK!
```

或者以其他的组合方式：

```java
// exceptions/MultiCatch2.java
public class MultiCatch2 {
    void x() throws Except1, Except2, Except3, Except4 {}
    void process1() {}
    void process2() {}
    void f() {
        try {
            x();
        } catch(Except1 | Except2 e) {
            process1();
        } catch(Except3 | Except4 e) {
            process2();
        }
    }
}
```

**在return中使用finally**

无论在try中的哪里使用return，finally代码块都会执行。