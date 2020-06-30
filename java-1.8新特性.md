[toc]

# 1. 函数式接口

+ Consumer

  Consumer< T >    消费者接口，接受一个参数并且无返回值的操作

+ Function

  Function< T，R >   函数型接口，接受一个参数并产生结果

+ Supplier

  Supplier< T >  结果供应商，供给型接口，无输入，有输出

+ Predicate

  Predicate< T >  断言接口

```java
public class InterfaceDemo {
    public static void main(String[] args) {
        consumerTest(10,x-> System.out.println(x));
        System.out.println(functionTest("mmxxyy",str->str.toUpperCase()));
        List<Integer> list = supperlierTest(2,()->(int)(Math.random()*100));
        list.forEach(System.out::println);
        List<String> pList = Arrays.asList("Larry","tom","mike");
        List<String> pResult = predicateTest(pList,s->s.contains("o"));
        pResult.forEach(System.out::println);
    }
    //消费型接口，有输入，没有返回值
    private static void consumerTest(int n, Consumer<Integer> c){
        c.accept(n);
    }
    //函数型接口，有输入，有输出
    private static String functionTest(String str, Function<String ,String> f){
        return f.apply(str);
    }
    //供给型接口，无输入，有输出
    private static List<Integer> supperlierTest(int num, Supplier<Integer> s){
        List<Integer> list = new ArrayList<>();
        for(int i=0;i<num;i++){
            list.add(s.get());
        }
        return list;
    }
    //断言型接口，有输出，
    private static List<String> predicateTest(List<String> list, Predicate<String> p){
        List<String> results = new ArrayList<>();
        for(String s:list){
            if(p.test(s)){
                results.add(s);
            }
        }
        return results;
    }

}
```

# 2.Stream

## 什么是 Stream？

Stream（流）是一个来自数据源的元素队列并支持聚合操作

- 元素是特定类型的对象，形成一个队列。 Java中的Stream并不会存储元素，而是按需计算。
- **数据源** 流的来源。 可以是集合，数组，I/O channel， 产生器generator 等。
- **聚合操作** 类似SQL语句一样的操作， 比如filter, map, reduce, find, match, sorted等。

和以前的Collection操作不同， Stream操作还有两个基础的特征：

- **Pipelining**: 中间操作都会返回流对象本身。 这样多个操作可以串联成一个管道， 如同流式风格（fluent style）。 这样做可以对操作进行优化， 比如延迟执行(laziness)和短路( short-circuiting)。
- **内部迭代**： 以前对集合遍历都是通过Iterator或者For-Each的方式, 显式的在集合外部进行迭代， 这叫做外部迭代。 Stream提供了内部迭代的方式， 通过访问者模式(Visitor)实现。

------

## 生成流

在 Java 8 中, 集合接口有两个方法来生成流：

- **stream()** − 为集合创建串行流。
- **parallelStream()** − 为集合创建并行流。

```java
List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl"); List<String> filtered = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.toList());
```

```java
public class StreamDemo {
    public static void main(String[] args) {
        Stream<String> stream = Stream.of("good","good","study","day","day","up");
        //输出
//        stream.forEach(System.out::println);
        //filter 过滤
//        stream.filter((s)->s.length()>3).forEach(System.out::println);
        //distinct 去重
//        stream.distinct().forEach(System.out::println);
        //map 映射
//        stream.map(s->s.toUpperCase()).forEach(System.out::println);
        //flatMap 组合多个集合
//        Stream<List<Integer>> ss = Stream.of(Arrays.asList(1,2,3),Arrays.asList(4,5));
//        ss.flatMap(list->list.stream()).forEach(System.out::println);

        //reduce
//        Optional<String> opt =  stream.reduce((s1, s2)->s1.length()>=s2.length()?s1:s2);
//        System.out.println(opt.get());
        //collect 根据流生成集合
        List<String> list = stream.collect(Collectors.toList());
        list.forEach(s-> System.out.println(s));
    }
}
```

