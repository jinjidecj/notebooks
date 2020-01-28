# Kotlin learn notebook

[kotlin官方文档](<https://www.kotlincn.net/docs/reference/>)

## 基础

### 1.基本类型

所有东西都是对象

kotlin 没有隐式拓宽转换，若要转换不同的类型，需要使用显式转换函数。

| 类型   | 比特数    | exp                             |
| ------ | --------- | ------------------------------- |
| Byte   | 8         | val oneB: Byte = 1              |
| Short  | 16        |                                 |
| Int    | 32        | var one = 1                     |
| Long   | 64        | var oneLong = 1L                |
| Float  | 32(24+8)  | val efloat = 1.2323f or 1.2314F |
| Double | 64(53+11) | val e = 2.1234                  |

**字面常量：**

+ 十进制
+ 十六进制  0x0f
+ 二进制 0b0001
+ 没有八进制

可以在数字中使用下划线

**类型转换函数：**

+ toByte()
+ toShort()
+ toInt()
+ toLong()
+ toFloat()
+ toDouble()
+ toChar()

但是在算术运算中会有重载，进行转换

**数组**

创建：arrayOf(1,2,3)   arrayOfNulls()指定大小，元素为空的数组

**字符串**

+ 可以使用[]来访问
+ 可以用+ 来操作，第一个元素得是字符串
+ 用双引号，可以有转义字符
+ 三个引号，没有转义，可以有换行和其他字符

```kotlin
val text = """
	for(c in "foo")
		print(c)
"""
```

+ 用trimMargin()去除前导空格，默认`|`作为边界前缀，也可以传其他参数，如`trimMargin(">")`

```kotlin
val text = """
	|tell
	|me 
""".trimMargin()
```

+ **字符串模板**

```kotlin
val i = 10
print("i=$i")
val s = "abc"
println("$s.length is ${s.length}")
```

### 2.控制流

**if表达式**

会返回值

``` kotlin
val max = if(a>b) a else b
val max = if(a>b){
    print("choose a")
    a
}
```

**when**

取代了switch

```kotlin
when(x){
    1 -> print()
    2,3 -> print()
    parseInt(s) -> p
    in 1..10 -> 
    !in 20..34 ->
    is String -> 
    else -> {
        
    }
}
```

**for**

```kotlin
for(item in collection) print(item)
for(i in array.indices){
    println(array[i])
}
for((index, value)in array.withIndex()){
    println("$index is $value")
}
```

**while ;  do..while; break; continue**



