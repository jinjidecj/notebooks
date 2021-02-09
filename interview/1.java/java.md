# java基础知识
[toc]
# 1. String、StringBuffer与StringBuilder之间区别

| String                                                       | StringBuffer                                                 | StringBuilder    |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------- |
| String的值是不可变的，这就导致每次对String的操作都会生成新的String对象，不仅效率低下，而且浪费大量优先的内存空间 | StringBuffer是可变类，和线程安全的字符串操作类，任何对它指向的字符串的操作都不会产生新的对象。每个StringBuffer对象都有一定的缓冲区容量，当字符串大小没有超过容量时，不会分配新的容量，当字符串大小超过容量时，会自动增加容量 | 可变类，速度更快 |
| 不可变                                                       | 可变                                                         | 可变             |
|                                                              | 线程安全                                                     | 线程不安全       |
|                                                              | 多线程操作字符串                                             | 单线程操作字符串 |
`String` 类中使用 final 关键字修饰字符数组来保存字符串，`private final char value[]`，所以`String` 对象是不可变的。在 Java 9 之后，String 类的实现改用 byte 数组存储字符串 `private final byte[] value`;
而 `StringBuilder` 与 `StringBuffer` 都继承自 `AbstractStringBuilder` 类，在 `AbstractStringBuilder` 中也是使用字符数组保存字符串`char[]value` 但是没有用 `final` 关键字修饰，所以这两种对象都是可变的。
**线程安全性**

`String` 中的对象是不可变的，也就可以理解为常量，线程安全。`AbstractStringBuilder` 是 `StringBuilder` 与 `StringBuffer` 的公共父类，定义了一些字符串的基本操作，如 `expandCapacity`、`append`、`insert`、`indexOf` 等公共方法。`StringBuffer` 对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的。`StringBuilder` 并没有对方法进行加同步锁，所以是非线程安全的。

**性能**

每次对 `String` 类型进行改变的时候，都会生成一个新的 `String` 对象，然后将指针指向新的 `String` 对象。`StringBuffer` 每次都会对 `StringBuffer` 对象本身进行操作，而不是生成新的对象并改变对象引用。相同情况下使用 `StringBuilder` 相比使用 `StringBuffer` 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。

**对于三者使用的总结：**

1. 操作少量的数据: 适用 `String`
2. 单线程操作字符串缓冲区下操作大量数据: 适用 `StringBuilder`
3. 多线程操作字符串缓冲区下操作大量数据: 适用 `StringBuffer`


# 2.为什么会产生内存泄漏？
当一个对象已经不需要再使用本该被回收时，另外一个正在使用的对象持有它的引用从而导致它不能被回收，这导致本该被回收的对象不能被回收而停留在堆内存中，这就产生了内存泄漏(memory leak)。
## 2.1 内存溢出OOM
是指程序在申请内存时，没有足够的内存空间供其使用，出现out of memory；

# 3.java程序中，如何安全的结束一个正在运行的线程
+ 使用共享变量的方式
在这种方式中，之所以引入共享变量，是因为该变量可以被多个执行相同任务的线程用来作为是否中断的信号，通知中断线程的执行。
```java
public class ThreadFlag extends Thread 
{ 
    public volatile boolean exit = false; 
 
    public void run() 
    { 
        while (!exit); 
    } 
    public static void main(String[] args) throws Exception 
    { 
        ThreadFlag thread = new ThreadFlag(); 
        thread.start(); 
        sleep(3000); // 主线程延迟3秒 
        thread.exit = true;  // 终止线程thread 
        thread.join(); 
        System.out.println("线程退出!"); 
    } 
} 
```
在上面代码中定义了一个退出标志exit，当exit为true时，while循环退出，exit的默认值为false。在定义exit时，使用了一个Java关键字volatile，这个关键字的目的是使exit同步，也就是说在同一时刻只能由一个线程来修改exit的值。
+ 使用interrupt方法终止线程 
Thread提供的interrupt()方法，因为该方法虽然不会中断一个正在运行的线程，但是它可以使一个被阻塞的线程抛出一个中断异常，从而使线程提前结束阻塞状态，退出堵塞代码。
```java
public static void main1(String[] args) {

        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    TimeUnit.DAYS.sleep(1L);
                    System.out.println("睡眠结束");
                } catch (Exception e) {
                    System.out.println("异常:" + e);//在这里做一些回收或者收尾操作
                } finally {
                    System.out.println("finally块被执行");
                }
            }
        });

        thread.start();

        if (!thread.isInterrupted()) {
            thread.interrupt();
        }
        System.out.println("主线程结束");
    }
```
注意：在Thread类中有两个方法可以判断线程是否通过interrupt方法被终止。一个是静态的方法interrupted（），一个是非静态的方法isInterrupted（），这两个方法的区别是interrupted用来判断当前线是否被中断，而isInterrupted可以用来判断其他线程是否被中断。

# 4. Hash碰撞及避免碰撞策略
## Hash碰撞
对象Hash的前提是实现equals()和hashCode()两个方法，那么HashCode()的作用就是保证对象返回唯一hash值，但当两个对象计算值一样时，这就发生了碰撞冲突。如下将介绍如何处理冲突，当然其前提是一致性hash。

## 避免Hash碰撞策略
### 4.1.开放地址法(再散列法)
冲突法生时，通过查找数组的一个空位，并将数据填入，而不再用哈希函数得到的数组下表。
简单的理解：就是当发生冲突的时候，比如1和101,1占据了一个位置，101进入时候就向下查找，找到下面的一个空位插入， 如果没有继续查找空位，知道找到为止并进行插入。
开放地执法有一个公式:Hi=(H(key)+di) MOD m i=1,2,…,k(k<=m-1)
其中，m为哈希表的表长。di 是产生冲突的时候的增量序列。如果di值可能为1,2,3,…m-1，称线性探测再散列。
如果di取1，则每次冲突之后，向后移动1个位置.如果di取值可能为1,-1,2,-2,4,-4,9,-9,16,-16,…kk,-kk(k<=m/2)，称二次探测再散列。
如果di取值可能为伪随机数列。称伪随机探测再散列。

### 4.2.再哈希法Rehash  ？？？？
当发生冲突时，使用第二个、第三个、哈希函数计算地址，直到无冲突时。
哈希化有两个，第二个哈希化不能输出0，不能与第一个哈希化相同，第二个哈希化得到的结果是当前冲突时，索引需要加的值,即是在二次探测的基础上将步长的改进
缺点：计算时间增加。
比如上面第一次按照姓首字母进行哈希，如果产生冲突可以按照姓字母首字母第二位进行哈希，再冲突，第三位，直到不冲突为止.这种方法不易产生聚集，但增加了计算时间。

### 4.3.链地址法（拉链法）
将所有关键字为同义词的记录存储在同一线性链表中.基本思想:将所有哈希地址为i的元素构成一个称为同义词链的单链表，并将单链表的头指针存在哈希表的第i个单元中，因而查找、插入和删除主要在同义词链中进行。链地址法适用于经常进行插入和删除的情况。
对比JDK 1.7 hashMap的存储结构是不是很好理解。至于1.8之后链表长度大于6rehash 为树形结构不在此处讨论。

#### 拉链法的优缺点
**优点**：
①拉链法处理冲突简单，且无堆积现象，即非同义词决不会发生冲突，因此**平均查找长度较短**；
②由于拉链法中各链表上的结点空间是动态申请的，故它更**适合于造表前无法确定表长的情况**；
③开放定址法为减少冲突，要求装填因子α较小，故**当结点规模较大时会浪费很多空间**。而拉链法中可取α≥1，且结点较大时，拉链法中增加的指针域可忽略不计，因此节省空间；
④在用拉链法构造的散列表中，**删除结点的操作易于实现**。只要简单地删去链表上相应的结点即可。而对开放地址法构造的散列表，删除结点不能简单地将被删结 点的空间置为空，否则将截断在它之后填人散列表的同义词结点的查找路径。这是因为各种开放地址法中，空地址单元(即开放地址)都是查找失败的条件。因此在 用**开放地址法处理冲突的散列表上执行删除操作，只能在被删结点上做删除标记，而不能真正删除结点**。

**缺点**：
指针需要额外的空间，故当结点规模较小时，开放定址法较为节省空间，而若将节省的指针空间用来扩大散列表的规模，可使装填因子变小，这又减少了开放定址法中的冲突，从而提高平均查找速度。

### 4.4.建立一个公共溢出区
假设哈希函数的值域为[0,m-1],则设向量HashTable[0…m-1]为基本表，另外设立存储空间向量OverTable[0…v]用以存储发生冲突的记录。

# 5.final,finally,finalize三个关键字的区别
**final**：java中的关键字，修饰符。
　　可以作为修饰符修饰变量、方法和类。
　　被final修饰的变量必须在初始化时给定初值，以后在使用时只能被引用而不能被修改。
　　被final修饰的方法不能够在子类中被重写（override）；
　　被final修饰的类不能够被继承。
**finally**：java的一种异常处理机制。
　　finally用在异常处理中定义总是执行代码，无论try块中的代码是否引发异常，catch是否匹配成功，finally块中的代码总是被执行，除非JVM被关闭，通常用作释放外部资源(不会被垃圾回收器回收的资源)。
**finalize**：Object类中定义的一个方法。
　　finalize方法在垃圾回收器执行内存对象清理时会调用finalize()方法进行前期的清理工作。
　　这个方法是由垃圾收集器在确定这个对象没有被引用时对这个对象调用的。
　　它是在 Object 类中定义的，因此所有的类都继承了它。
　　子类覆盖 finalize() 方法以整理系统资源或者执行其他清理工作。

# 6.成员变量在堆内，局部变量在栈内
内存：
**栈** 
    1.存放局部变量 
    2.不可以被多个线程共享 
    3.空间连续，速度快
**堆** 
    1.存放对象 
    2.可以被多个线程共享 
    3.空间不连续，速度慢，但是灵活 
**方法区** 
    1.存放类的信息：代码、静态变量、字符串常量等等 
    2.可以被多个线程共享 
    3.空间不连续，速度慢，但是灵活

总的来说：我们先来记住两条黄金法则：
    1.引用类型总是被分配到“堆”上。不论是成员变量还是局部
    2.基础类型总是分配到它声明的地方：成员变量在堆内存里，局部变量在栈内存里。

1.  jvm只有一个堆区(heap)被所有线程共享，堆中不存放基本类型和对象引用，只存放对象本身（也就是值），类的成员变量在不同对象中各不相同，都有自己的存储空间(成员变量在堆中的对象中)，基本类型和引用类型的成员变量都在这个对象的空间中，作为一个整体存储在堆。

2.  **对于局部变量，如果是基本类型，会把值直接存储在栈；如果是引用类型，比如String s = new String("william");会把其对象存储在堆，而把这个对象的引用（指针）存储在栈。**

3.  **类的方法是该类的所有对象共享的，只有一套，对象使用方法的时候方法才被压入栈，方法不使用则不占用内存。一个类可以产生无数个对象，但类就那么一个，方法也就那么一个**

```java
String s1 = "china";  
String s2 = "china";  
String s3 = "china";  
String ss1 = new String("china");  
String ss2 = new String("china");  
String ss3 = new String("china");  
```
这里解释一下黄色这3个箭头，对于通过new产生一个字符串（假设为”china”）时，会先去常量池中查找是否已经有了”china”对象，如果没有则在常量池中创建一个此字符串对象，然后堆中再创建一个常量池中此”china”对象的拷贝对象。这也就是有道面试题：String s = new String(“xyz”);产生几个对象？**一个或两个**，如果常量池中原来没有”xyz”,就是两个。


# 7. Java中的锁
## 7.1 公平锁/非公平锁
**公平锁**是指多个线程按照申请锁的顺序来获取锁。

**非公平锁**是指多个线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请的线程比先申请的线程优先获取锁。有可能，会造成优先级反转或者饥饿现象。

对于Java ReentrantLock而言，通过构造函数指定该锁是否是公平锁，默认是非公平锁。非公平锁的优点在于吞吐量比公平锁大。

对于Synchronized而言，也是一种非公平锁。由于其并不像ReentrantLock是通过AQS的来实现线程调度，所以并没有任何办法使其变成公平锁。

## 7.2 可重入锁

**可重入锁**又名递归锁，是指在同一个线程在外层方法获取锁的时候，在进入内层方法会自动获取锁。说的有点抽象，下面会有一个代码的示例。

对于Java ReentrantLock而言, 他的名字就可以看出是一个可重入锁，其名字是Reentrant Lock重新进入锁。

对于Synchronized而言,也是一个可重入锁。可重入锁的一个好处是可一定程度避免死锁。
```java
synchronized void setA() throws Exception{

    Thread.sleep(1000);

    setB();

}

synchronized void setB() throws Exception{

    Thread.sleep(1000);

}
```
上面的代码就是一个可重入锁的一个特点，如果不是可重入锁的话，setB可能不会被当前线程执行，可能造成死锁。
## 7.3 独享锁/共享锁
**独享锁**是指该锁一次只能被一个线程所持有。

**共享锁**是指该锁可被多个线程所持有。

对于Java ReentrantLock而言，其是独享锁。但是对于Lock的另一个实现类ReadWriteLock，其读锁是共享锁，其写锁是独享锁。

读锁的共享锁可保证并发读是非常高效的，读写，写读 ，写写的过程是互斥的。

独享锁与共享锁也是通过AQS来实现的，通过实现不同的方法，来实现独享或者共享。

对于Synchronized而言，当然是独享锁。

## 7.4 互斥锁/读写锁
上面讲的独享锁/共享锁就是一种广义的说法，互斥锁/读写锁就是具体的实现。

互斥锁在Java中的具体实现就是ReentrantLock

读写锁在Java中的具体实现就是ReadWriteLock

## 7.5 乐观锁/悲观锁
乐观锁与悲观锁不是指具体的什么类型的锁，而是指看待并发同步的角度。

悲观锁认为对于同一个数据的并发操作，一定是会发生修改的，哪怕没有修改，也会认为修改。因此对于同一个数据的并发操作，悲观锁采取加锁的形式。悲观的认为，不加锁的并发操作一定会出问题。

乐观锁则认为对于同一个数据的并发操作，是不会发生修改的。在更新数据的时候，会采用尝试更新，不断重新的方式更新数据。乐观的认为，不加锁的并发操作是没有事情的。

从上面的描述我们可以看出，悲观锁适合写操作非常多的场景，乐观锁适合读操作非常多的场景，不加锁会带来大量的性能提升。

悲观锁在Java中的使用，就是利用各种锁。

乐观锁在Java中的使用，是无锁编程，常常采用的是CAS算法，典型的例子就是原子类，通过CAS自旋实现原子操作的更新。

## 7.6 分段锁
分段锁其实是一种锁的设计，并不是具体的一种锁，对于ConcurrentHashMap而言，其并发的实现就是通过分段锁的形式来实现高效的并发操作。

我们以ConcurrentHashMap来说一下分段锁的含义以及设计思想，ConcurrentHashMap中的分段锁称为Segment，它即类似于HashMap(JDK7与JDK8中HashMap的实现)的结构，即内部拥有一个Entry数组，数组中的每个元素又是一个链表;同时又是一个ReentrantLock(Segment继承了ReentrantLock)。

当需要put元素的时候，并不是对整个hashmap进行加锁，而是先通过hashcode来知道他要放在那一个分段中，然后对这个分段进行加锁，所以当多线程put的时候，只要不是放在一个分段中，就实现了真正的并行的插入。

但是，在统计size的时候，可就是获取hashmap全局信息的时候，就需要获取所有的分段锁才能统计。

分段锁的设计目的是细化锁的粒度，当操作不需要更新整个数组的时候，就仅仅针对数组中的一项进行加锁操作。

## 7.7 偏向锁/轻量级锁/重量级锁
这三种锁是指锁的状态，并且是针对Synchronized。在Java 5通过引入锁升级的机制来实现高效Synchronized。这三种锁的状态是通过对象监视器在对象头中的字段来表明的。

偏向锁是指一段同步代码一直被一个线程所访问，那么该线程会自动获取锁。降低获取锁的代价。

轻量级锁是指当锁是偏向锁的时候，被另一个线程所访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，提高性能。

重量级锁是指当锁为轻量级锁的时候，另一个线程虽然是自旋，但自旋不会一直持续下去，当自旋一定次数的时候，还没有获取到锁，就会进入阻塞，该锁膨胀为重量级锁。重量级锁会让其他申请的线程进入阻塞，性能降低。

## 7.8 自旋锁
在Java中，自旋锁是指尝试获取锁的线程不会立即阻塞，而是采用循环的方式去尝试获取锁，这样的好处是减少线程上下文切换的消耗，缺点是循环会消耗CPU。

典型的自旋锁实现的例子，可以参考自旋锁的实现



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