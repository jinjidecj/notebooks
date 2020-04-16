[toc]

# 一、简介

## 1.数据库的分类

1. 关系型数据库（RDBMS:Relational Database Management System)

    MySql、Oracle、DB2、SQL Server...关系型数据库中全都是表

2. 非关系型数据库（No Sql --Not only sql）(Sql:结构化查询语言)

   MongoDB、Redis...键值对数据库

   MongoDB是nosql中的文档数据库

MongoDB是为快速开发互联网web应用而设计的数据库系统，极简、灵活、作为web应用栈的一部分，MongoDB的数据模型是面向文档的，所谓文档就是一种类似于json的结构，MongoDB这个数据库中存的是各种各样的json(BSON)。

## 2.MongoDB的安装

+ [安装网址](https://www.mongodb.com/download-center/community)

+ 配置环境变量到Path里：xxx\mongoDB\bin

+ 创建文件夹“data/db"，cmd窗口中输入命令  `mongod --dbpath E:\MongoDB\data\db` (默认端口27017 尽量使用默认的端口，如要指定其他端口则在后面加上  `--port 27017`)

+ **mongod** 是启动数据库服务端，**mongo**是启动客户端，在另外一个cmd窗口输入`mongo`可以连接数据库

+ 将mongoDB设置为系统服务，可以在后台启动

  + 在mongoDB安装的根目录下创建mongod.cfg配置文件，文件内容为：

    ```txt
    systemLog:
      destination: file
      path: D:\software\monogoDB\data\log\mongod.log
    storage:
      dbPath: D:\software\monogoDB\data\db
    ```

  + cmd管理员执行命令:

    ```txt
    sc.exe create MongoDB binPath= "\"mongod的bin目录\mongod.exe\" --service --config=\"mongo的安装目录\mongod.cfg\"" DisplayName= "MongoDB" start= "auto"
    eg:
    sc.exe create MongoDB binPath= "\"D:\software\monogoDB\bin\mongod.exe\" --service --config=\"D:\software\monogoDB\mongod.cfg\"" DisplayName= "MongoDB" start= "auto"
    ```

  + 在“管理-服务”中可以启动mongoDB

  + 如果启动失败，证明上边的操作有误，在控制台输入 sc delete MongoDB 删除之前配置的服务，然后重新配置

## 3.基本概念

### 1.数据库(database)

一个mongodb中可以创建多个数据库，有特殊作用的数据库如下：

+ **admin**:从权限的角度来看，这是"root"数据库。要是将一个用户添加到这个数据库，这个用户自动继承所有数据库的权限。一些特定的服务器端命令也只能从这个数据库运行，比如列出所有的数据库或者关闭服务器。
+ **local**:这个数据永远不会被复制，可以用来存储限于本地单台服务器的任意集合
+ **config**:当Mongo用于分片设置时，config数据库在内部使用，用于保存分片的相关信息。

### 2.集合(collection)

集合是MongoDB文档组，类似于关系型数据库RDBMS中的表格，集合没有固定的结构，可以插入不同的格式和类型，但通常插入集合的数据会有一定的关联性。

### 3.文档(document)

文档是一组键值对（key-value），即BSON，不需要设置相同的字段，同一字段存储的内容不必是相同类型。

RDBMS与mongodb对应的术语：

| RDBMS  | MongoDB                           |
| ------ | --------------------------------- |
| 数据库 | 数据库                            |
| 表格   | 集合                              |
| 行     | 文档                              |
| 列     | 字段                              |
| 表联合 | 嵌入文档                          |
| 主键   | 主键（MongoDB提供的key默认为_id） |

| mysql/oracle的 数据库服务和客户端 | MongoDB的数据库服务和客户端 |
| ----------------------------- | ------------------------ |
| Mysqld        | mongod |
| mysql/sqlplus | mongo  |

需要注意的是：

1. 文档中的键/值对是有序的。
2. 文档中的值不仅可以是在双引号里面的字符串，还可以是其他几种数据类型（甚至可以是整个嵌入的文档)。
3. MongoDB区分类型和大小写。
4. MongoDB的文档不能有重复的键。
5. 文档的键是字符串。除了少数例外情况，键可以使用任意UTF-8字符。



在mongoDB中，数据库和集合不需要手动创建，当我们创建文档时，如果文档所在的集合和数据库都不存在，则会**自动**创建数据库和集合。MongoDB中默认的数据库为test，如果没有创建新的数据库，则集合将会存放在test数据库中。

# 二、指令

## 1.基本指令

| 指令                            | 操作                              |
| ------------------------------- | --------------------------------- |
| show dbs                        | 显示当前数据库                    |
| show databases                  | 显示当前数据库                    |
| use 数据库名                    | 进入到指定的数据库中              |
| db                              | db表示当前所处的数据库            |
| show collections  / show tables | 显示当前数据库中的所有集合        |
| db.dropDatabase()               | 删除当前数据库                    |
| db.collection.drop()            | 删除集合，成功返回true，否则false |

+ **创建集合**

  `db.createCollection(name,options)`

  - name: 要创建的集合名称
  - options: 可选参数, 指定有关内存大小及索引的选项

  options 可以是如下参数：

  | 字段        | 类型 | 描述                                                         |
  | :---------- | :--- | :----------------------------------------------------------- |
  | capped      | 布尔 | （可选）如果为 true，则创建固定集合。固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档。 **当该值为 true 时，必须指定 size 参数。** |
  | autoIndexId | 布尔 | （可选）如为 true，自动在 _id 字段创建索引。默认为 false。   |
  | size        | 数值 | （可选）为固定集合指定一个最大值，以千字节计（KB）。 **如果 capped 为 true，也需要指定该字段。** |
  | max         | 数值 | （可选）指定固定集合中包含文档的最大数量。                   |

  ```
  db.createCollection("mycol", { capped : true, autoIndexId : true, size : 
     6142800, max : 10000 } )
     创建固定集合 mycol，整个集合空间大小 6142800 KB, 文档最大个数为 10000 个。
  ```

## 2.插入

+ 向集合中插入一个文档 ： db.<collection>.insert(doc)

  ```txt
  db.stus.insert({name:"孙悟空",age:18,gender:"男"})
  ```

+ 向集合中插入多个文档：db.<collection>.insert([doc,doc])

  ```txt
  db.stus.insert([
  	{name:"孙悟空",age:18,gender:"男"},
  	{name:"孙悟空",age:18,gender:"男"}])
  ```

+ db.<collection>.insertOne()

+ db.<collection>.insertMany()

+ save()命令也可以插入文档，如果不指定_id，则和insert()方法一样，如果指定，则会更新该 _id 的数据。

## 3.查询

+ 查询当前集合中的所有的文档 ：db.<collection>.find({key:value})
  + {} 表示查询集合中所有文档
  + {key:value} 查询属性是指定值的文档
  + 返回的是一个**数组**
  
  ```
  db.collection.find(query, projection)
  query ：可选，使用查询操作符指定查询条件
  projection ：可选，使用投影操作符指定返回的键。查询时返回文档中所有键值， 只需省略该参数即可（默认省略）。
  ```
  
  如果你需要以易读的方式来读取数据，可以使用 pretty() 方法，以格式化的方式来显示所有文档。语法格式如下：
  
  ```
  db.col.find().pretty()
  ```
  
+ 查询集合中符条件的第一个文档：db.<collection>.findOne({key:value})  返回的是一个**对象**

+ 查询结果的数量：db.<collection>.find().count() 

### 条件操作符

| 操作       | 格式                   | 范例                                        | RDBMS中的类似语句       |
| :--------- | :--------------------- | :------------------------------------------ | :---------------------- |
| 等于       | {<key>:<value>}        | `db.col.find({"by":"菜鸟教程"}).pretty()`   | `where by = '菜鸟教程'` |
| 小于       | {<key>:{$lt:<value>}}  | `db.col.find({"likes":{$lt:50}}).pretty()`  | `where likes < 50`      |
| 小于或等于 | {<key>:{$lte:<value>}} | `db.col.find({"likes":{$lte:50}}).pretty()` | `where likes <= 50`     |
| 大于       | {<key>:{$gt:<value>}}  | `db.col.find({"likes":{$gt:50}}).pretty()`  | `where likes > 50`      |
| 大于或等于 | {<key>:{$gte:<value>}} | `db.col.find({"likes":{$gte:50}}).pretty()` | `where likes >= 50`     |
| 不等于     | {<key>:{$ne:<value>}}  | `db.col.find({"likes":{$ne:50}}).pretty()`  | `where likes != 50`     |

### AND 条件

MongoDB 的 find() 方法可以传入多个键(key)，每个键(key)以逗号隔开，即常规 SQL 的 AND 条件。

```
db.col.find({key1:value1, key2:value2}).pretty()
```

### OR条件

MongoDB OR 条件语句使用了关键字 **$or**,语法格式如下：

```
db.col.find(
   {
      $or: [
         {key1: value1}, {key2:value2}
      ]
   }
).pretty()
```

### AND和OR联合使用

```
db.col.find({"likes": {$gt:50}, $or: [{"by": "yyyy"},{"title": "xxxxxx"}]}).pretty()
 等价于：'where likes>50 AND (by = '菜鸟教程' OR title = 'MongoDB 教程')'
```

## 4.修改

+ db.<collection>.update(查询条件，新对象)   **当查询条件得到多个对象时，默认update()只会修改一个**

  ```
  db.collection.update(
     <query>,
     <update>,
     {
       upsert: <boolean>,
       multi: <boolean>,
       writeConcern: <document>
     }
  )
  参数说明：
  query : update的查询条件，类似sql update查询内where后面的。
  update : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的
  upsert : 可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
  multi : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。
  writeConcern :可选，抛出异常的级别。
  ```

  + 默认会用新对象替换旧对象

  ```txt
  db.stus.update({name:"www"},{age:29})
  会把原对象替换为只有age和_id属性的对象
  ```

  + 如果需要修改制定的属性，则需要使用“**修改操作符**”
    + $set  可以用来修改文档中的指定属性
    + $unset 可以用来删除文档的指定属性（key要对，value是什么值无所谓）

  ```txt
  db.stus.update(
  	{"_id":.xxx},
  	{$set:{
  		age:12,          //已有的会修改值
  		address:"xxxx"   //没有的部分也会加进去
  	}}
  )
  db.stus.update(
  	{"id":xxx},
  	{$unset:{
  		address:1
  	}}
  )
  ```

+ db.<collection>.updateMany(查询条件，新对象) **会把查询到的所有对象都更新**

+ db.<collection>.updateOne(查询条件，新对象) **会把查询到的对象都更新一个**

+ db.<collection>.save() 通过传入的文档来替换已有文档

  ```
  db.collection.save(
     <document>,
     {
       writeConcern: <document>
     }
  )
  参数说明：
  document : 文档数据。
  writeConcern :可选，抛出异常的级别。
  ```

## 5.删除

remove()用来移除集合中的数据。

```
db.collection.remove(
   <query>,
   <justOne>
)
2.6版本后：
db.collection.remove(
   <query>,
   {
     justOne: <boolean>,
     writeConcern: <document>
   }
)
参数说明：
query :（可选）删除的文档的条件。
justOne : （可选）如果设为 true 或 1，则只删除一个文档，如果不设置该参数，或使用默认值 false，则删除所有匹配条件的文档。
writeConcern :（可选）抛出异常的级别。
```

db.collection.remove({})会删除所有数据，但是效率比较低，因为他是一边查询（条件满足“{}”的）一边删除的，可以使用drop来删除，效率比较高。

## 6.其他

### $type操作符

$type操作符是基于BSON类型来检索集合中匹配的数据类型，并返回结果。

| **类型**                | **数字** | **备注**         |
| :---------------------- | :------- | :--------------- |
| Double                  | 1        |                  |
| String                  | 2        |                  |
| Object                  | 3        |                  |
| Array                   | 4        |                  |
| Binary data             | 5        |                  |
| Undefined               | 6        | 已废弃。         |
| Object id               | 7        |                  |
| Boolean                 | 8        |                  |
| Date                    | 9        |                  |
| Null                    | 10       |                  |
| Regular Expression      | 11       |                  |
| JavaScript              | 13       |                  |
| Symbol                  | 14       |                  |
| JavaScript (with scope) | 15       |                  |
| 32-bit integer          | 16       |                  |
| Timestamp               | 17       |                  |
| 64-bit integer          | 18       |                  |
| Min key                 | 255      | Query with `-1`. |
| Max key                 | 127      |                  |

```
db.col.find({"title" : {$type : 2}})
或
db.col.find({"title" : {$type : 'string'}})
均是获取集合中title的数据类型为string的文档。
```

### Limit()与Skip()方法

如果你需要在MongoDB中读取指定数量的数据记录，可以使用MongoDB的Limit方法，limit()方法接受一个数字参数，该参数指定从MongoDB中读取的记录条数。

```
db.COLLECTION_NAME.find().limit(NUMBER)
从集合中获取NUMBER条文档。
```

我们除了可以使用limit()方法来读取指定数量的数据外，还可以使用skip()方法来跳过指定数量的数据，skip方法同样接受一个数字参数作为跳过的记录条数。

```
db.COLLECTION_NAME.find().limit(n1).skip(n2)
从集合中获取从n2+1开始的n1条文档。
```

### sort()方法

在 MongoDB 中使用 sort() 方法对数据进行排序，sort() 方法可以通过参数指定排序的字段，并使用 1 和 -1 来指定排序的方式，其中 1 为升序排列，而 -1 是用于降序排列。

```
db.COLLECTION_NAME.find().sort({KEY:1})
```

# 三、SpringBoot+mangoDB

**pom文件添加依赖**

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

**配置文件**

```properties
## 无密码配置
spring.data.mongodb.uri=mongodb://localhost:27017/learn

## 有密码配置
## name：用户名 / password：密码
###spring.data.mongodb.uri=mongodb://name:password@localhost:27017/springboot_mongodb

## 多个集群的配置
## spring.data.mongodb.uri=mongodb://user:secret@mongo1.example.com:12345,mongo2.example.com:23456/test
```

**实体类**

`spring-data-mongodb`中的实体映射是通过`MongoMappingConverter`这个类实现的。它可以通过注释把java类转换为mongodb的文档。

它有以下几种注释：

`@Id` - 文档的唯一标识，在mongodb中为ObjectId，它是唯一的，通过时间戳+机器标识+进程ID+自增计数器（确保同一秒内产生的Id不会冲突）构成。

`@Document` - 把一个java类声明为mongodb的文档，可以通过collection参数指定这个类对应的文档。@Document(collection=”mongodb”) mongodb对应表

`@DBRef` - 声明类似于关系数据库的关联关系。ps：暂不支持级联的保存功能，当你在本实例中修改了DERef对象里面的值时，单独保存本实例并不能保存DERef引用的对象，它要另外保存，如下面例子的Person和Account。

`@Indexed` - 声明该字段需要索引，建索引可以大大的提高查询效率。

`@CompoundIndex` - 复合索引的声明，建复合索引可以有效地提高多字段的查询效率。

`@GeoSpatialIndexed` - 声明该字段为地理信息的索引。

`@Transient` - 映射忽略的字段，该字段不会保存到mongodb。

`@PersistenceConstructor` - 声明构造函数，作用是把从数据库取出的数据实例化为对象。该构造函数传入的值为从DBObject中取出的数据

```java
import org.springframework.data.mongodb.core.mapping.Document;
@Document("student")
public class Student {
    private Integer id;
    private String name;
    private Integer age;
	//get set//
}
```

**配置类**

```java
@Configuration
public class MongoDBConfigure {
    // 如果不加上该方法的话，插入mongodb collection中的数据会多出“_class”字段
    @Bean
    public MappingMongoConverter mappingMongoConverter(MongoDbFactory factory, MongoMappingContext context, BeanFactory beanFactory) {
        DbRefResolver dbRefResolver = new DefaultDbRefResolver(factory);
        MappingMongoConverter mappingConverter = new MappingMongoConverter(dbRefResolver, context);
        try{
            mappingConverter.setCustomConversions(beanFactory.getBean(CustomConversions.class));
        } catch (NoSuchBeanDefinitionException ignore) {
            
        }

        // Don't save _class to mongo
        mappingConverter.setTypeMapper(new DefaultMongoTypeMapper(null));
        return mappingConverter;
    }
}
```

**操作**

```java
package com.zcj.mongodb;
import com.fasterxml.jackson.databind.BeanProperty;
import com.zcj.mongodb.entity.Student;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.mongodb.core.MongoTemplate;
import org.springframework.data.mongodb.core.query.Criteria;
import org.springframework.data.mongodb.core.query.Update;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.data.mongodb.core.query.Query;
import java.util.List;
@RestController
public class StuController {
    @Autowired
    private MongoTemplate mongoTemplate;
    @GetMapping(value = "/find")
    private String findall(){
        Query query = Query.query(Criteria.where("name").is("11111"));
        List<Student> list = mongoTemplate.find(query,Student.class);
        if(list.size()!=0)
            System.out.println(list.get(0).toString());
        else
            System.out.println("is null");
        return "find";
    }
    @GetMapping(value = "/insert")
    public String  insert(){
        Student  student = new Student();
        student.setAge(12);
        student.setId(1004);
        student.setName("11111");
        mongoTemplate.insert(student);
        return "insert";
    }
    @GetMapping(value = "/delete")
    public String delete(){
        Query query = Query.query(Criteria.where("name").is("11111"));
        mongoTemplate.remove(query,Student.class);
        return "delete";
    }
    @GetMapping(value = "/update")
    public String up(){
        Query query = Query.query(Criteria.where("name").is("11111"));
        Update update = new Update();
        update.set("name","11111");
        update.set("age",99);
        mongoTemplate.updateFirst(query,update, Student.class);
        return "update";
    }
}
```





> 参考：
>+ [菜鸟教程MongoDB](https://www.runoob.com/mongodb/mongodb-tutorial.html)
>+ [spring boot集成mongodb的增删改查](https://www.cnblogs.com/ssjf/p/11465718.html)
>+ [Spring Boot 中使用 MongoDB 增删改查](https://www.ymq.io/2018/02/05/spring-boot-mongodb-example/)