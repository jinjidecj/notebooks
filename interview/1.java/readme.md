# java基础知识
[toc]
# 1. String、StringBuffer与StringBuilder之间区别

| String                                                       | StringBuffer                                                 | StringBuilder    |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------- |
| String的值是不可变的，这就导致每次对String的操作都会生成新的String对象，不仅效率低下，而且浪费大量优先的内存空间 | StringBuffer是可变类，和线程安全的字符串操作类，任何对它指向的字符串的操作都不会产生新的对象。每个StringBuffer对象都有一定的缓冲区容量，当字符串大小没有超过容量时，不会分配新的容量，当字符串大小超过容量时，会自动增加容量 | 可变类，速度更快 |
| 不可变                                                       | 可变                                                         | 可变             |
|                                                              | 线程安全                                                     | 线程不安全       |
|                                                              | 多线程操作字符串                                             | 单线程操作字符串 |


# 2.为什么会产生内存泄漏？
当一个对象已经不需要再使用本该被回收时，另外一个正在使用的对象持有它的引用从而导致它不能被回收，这导致本该被回收的对象不能被回收而停留在堆内存中，这就产生了内存泄漏。

# 数据结构的转化
### int[] 转Set<Integer>
```java
Int[] intArray = new Int[]{1,2};
Set<Integer> set = Arrays.stream(intArray).boxed().collect(Collectors.toSet());
```
### Map 转 List
```java
Map<Integer,Integer> map = new HashMap<>();
map.put(1,3);
map.put(2,4);
List<Integer> integers = new ArrayList<>(map.values());
```
### String ----Char[]
```java
String s = "abc";
char[] charArray = s.toCharArray();

String newS = String.valueOf(charArray);
String newS = String.valueOf('c');
```
### List<Integer> ---- int[]
```java
//int[] 转 List<Integer>
List<Integer> list1 = Arrays.stream(data).boxed().collect(Collectors.toList());
//List<Integer> 转 int[]
int[] arr1 = list1.stream().mapToInt(Integer::valueOf).toArray();
```
# 排序
```java
Collections.sort(list);

Collections.sort(list, new Comparator<User>() {
　　　　@Override
　　　　public int compare(User u1, User u2) {
　　　　　　int diff = u1.getAge() - u2.getAge();
　　　　　　if (diff > 0) {
　　　　　　　　return 1;
　　　　　　}else if (diff < 0) {
　　　　　　　　return -1;
　　　　　　}
　　　　　　return 0; //相等为0
　　　　}
　　}); // 按年龄排序

```