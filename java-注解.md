[toc]

# 1.什么是Annotation

Annotation 注解，是代码里做的特殊标记，可以在编译，类加载，运行时在不改变原有逻辑的情况下，被读取，并执行相应的处理，通过使用Annotation，程序员可以在源文件中嵌入一些补充的信息。代码分析工具，开发工具和部署工具可以通过这些补充信息进行验证或者进行部署。Annotation类似于修饰符一样被使用，可以用于包，类，构造方法，方法，成员变量，参数，局部变量的声明。

Annotation是一个接口

# 2.系统定义的Annotation

| 注解                 | 介绍                                                         |
| -------------------- | ------------------------------------------------------------ |
| @Override            | 表示当前方法定义将覆盖超类中的方法。如果你不小心拼写错误，或方法签名对不上被覆盖的方法，编译器就会发出错误提示 |
| @Deprecated          | 表示一个类或方法已经不再建议继续使用了，标记为已过时         |
| @SuppressWarnings    | 表示关闭不当的编译器警告信息                                 |
| @Retention           | 标识这个注解怎么保存，是只在代码中，还是编入class文件中，或者是在运行时可以通过反射访问。 |
| @Documented          | 标记这些注解是否包含在用户文档中。                           |
| @Target              | 标记这个注解应该是哪种 Java 成员。                           |
| @Inherited           | 标记这个注解是继承于哪个注解类(默认 注解并没有继承于任何子类) |
| @SafeVarargs         | Java 7 开始支持，忽略任何使用参数为泛型变量的方法或构造函数调用产生的警告。 |
| @FunctionalInterface | Java 8 开始支持，标识一个匿名函数或函数式接口。              |
| @Repeatable          | Java 8 开始支持，标识某注解可以在同一个声明上使用多次。      |

+ @SuppressWarnings("unchecked")   未检查的转化，如集合没有指定类型
+ @SuppressWarnings("unused")   未使用的变量
+ @SuppressWarnings("resource")   有泛型未指定类型
+ @SuppressWarnings("path")   在类路径，源文件路径中有不存在的路径
+ @SuppressWarnings("deprecation")   使用了某些不赞成使用的类和方法
+ @SuppressWarnings("fallthrough")   switch语句执行到底没有break关键字
+ @SuppressWarnings("serial")   某类实现serializable但是没有定义serialVersionUID这个需要但是不必须的字段
+ @SuppressWarnings("rawtypes")   没有传递带有泛型的参数
+ @SuppressWarnings("all")   全部类型的警告

# 3.自定义Annotation

三个步骤：

+ 编写注解
+ 在类上应用注解
+ 对应用了注解的类进行反射操作的类

语法：

访问控制权限  @interface Annotation 名称{}

如：

public @interface MyAnnotation{}

在Annotation中定义变量

```java
public @interface MyAnnotation{
	public String name();
    public String info();
}
```

定义变量后，调用此Annotation时必须设置变量值

```java
@MyAnnotation(name="vince",info="hello")
public class Demo{
    
}
```

通过default指定变量默认值，有了默认值，在使用Annotation时可以不设值

```java
public @interface MyAnnotation{
    public String name() default "vince";
}
```

定义一个变量的数组，接收一组参数

```java
public @interface MyAnnotation{
    public String[] name();
}
@MyAnnotation(name={"java","cc"})
public class Demo{}
```

使用枚举变量限制取值范围

```java
public enum Color{
    RED,GREEn,BLUE
}
public @interface MyAnnotation{
    public Color color();
}
```

# 4.Retention和RetentionPolicy

Annotation想要决定其作用范围，通过@Retention指定，而Retention指定的范围，由RetentionPolicy决定，RetentionPolicy指定了三种范围：

| 范围                                        | 描述                    |
| ------------------------------------------- | ----------------------- |
| public static final RetentionPolicy SOURCE  | 在java源程序中存在      |
| public static final RetentionPolicy CLASS   | 在java生成的class中存在 |
| public static final RetentionPolicy RUNTIME | 在java运行的时候存在    |

```java
@Retention(Value = RetentionPolicy.RUNTIME)
public @interface MyAnnotation{
    public String name();
}
```

# 5.反射与Annotation

一个Annotation真正起作用，必须结合反射机制，在反射中提供了以下的操作方法：

java.lang.reflect.AccessibleObject

| 方法                                                         | 描述                                            |
| ------------------------------------------------------------ | ----------------------------------------------- |
| public boolean isAnnotationPresent(Class<? extend Annotation > annotationClass) | 判断是否是指定的Annotation，仅限JVM虚拟机使用。 |
| public Annotation[] getAnnotations()                         | 得到全部的Annotation                            |

#6.@Documented注解

此注解表示的是文档化，可以在生成doc文档的时候添加注解

```java
@Documented
@Retention(value = RetentionPolicy.RUNTIME)
public @interface MyAnnotation{
    public String name();
    public String info();
}

可以增加一些DOC注解
/**
* xxxxdoc
*/
@MyAnnotation(name="vince",info="xx")
public String toString(){
    return "hello";
}
```

# 7.@Target注解

@Target注解表示的是一个Annotation的使用范围

| 范围                                            | 描述                       |
| ----------------------------------------------- | -------------------------- |
| public static final ElementType TYPE            | 只能在类或接口或枚举上使用 |
| public static final ElementType FIELD           | 在成员变量使用             |
| public static final ElementType METHOD          | 在方法中使用               |
| public static final ElementType PARAMETER       | 在参数上使用               |
| public static final ElementType CONSTRUCTOR     | 在构造中使用               |
| public static final ElementType LOCAL_VARIABLE  | 在局部变量上使用           |
| public static final ElementType ANNOTATION_TYPE | 只能在Annotation中使用     |
| public static final ElementType PACKAGE         | 只能在包中使用             |

# 8.@Inherited注解

@Inherited表示一个Annotation是否允许被其子类继承下来。

```java
@Inherited
@Target(value=ElementType.TYPE)
@Retention(value=RetentionPolicy.RUNTIME)
public @interface MyAnnotation{
    public String name():
}
```

使用时允许被其子类继承。

[菜鸟教程](#https://www.runoob.com/w3cnote/java-annotation.html)