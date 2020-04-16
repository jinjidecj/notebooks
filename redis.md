[toc]

# 一、概述

## 1.1 简介

Redis 是完全开源免费的，遵守BSD协议，是一个高性能(NOSQL)的key-value数据库,Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。

**Redis 与其他 key - value 缓存产品有以下三个特点：**

- Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
- Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
- Redis支持数据的备份，集群等高可用功能。

**Redis 的特点:**

- 性能极高 – Redis能读的速度是110000次/s,写的速度是81000次/s 。
- 丰富的数据类型 – Redis支持的类型 String, List, Hash, Set 及 Ordered Set 数据类型操作。
- 原子 – Redis的所有操作都是原子性的，意思就是要么成功执行要么失败完全不执行。单个操作是原子性的。多个操作也支持事务，即原子性，通过MULTI和EXEC指令包起来。
- 丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性。

Redis是一个简单的，高效的，分布式的，基于内存的缓存工具。 架设好服务器后，通过网络连接（类似数据库），提供Key－Value式缓存服务。简单，是Redis突出的特色。 简单可以保证核心功能的稳定和优异。

**总结**

redis单个key 存入512M大小

redis支持多种类型的数据结构(string,list,hash.set.zset)

redis 是单线程   原子性    

redis可以持久化  因为使用了 RDB和AOF机制  

redis支持集群   而且redis 支持库(0-15) 16个库 

redis 还可以做消息队列  比如聊天室  IM 

企业级开发中:可以用作数据库、缓存(热点数据（经常会被查询，但是不经常被修改或者删除的数据)和消息中间件等大部分功能。

**优点：**  

1. 丰富的数据结构

2. 高速读写，redis使用自己实现的分离器，代码量很短，没有使用lock（MySQL），因此效率非常高。

**缺点：**  

1. 持久化。Redis直接将数据存储到内存中，要将数据保存到磁盘上，Redis可以使用两种方式实现持久化过程。定时快照（snapshot）：每隔一段时间将整个数据库写到磁盘上，每次均是写全部数据，代价非常高。第二种方式基于语句追加（aof）：只追踪变化的数据，但是追加的log可能过大，同时所有的操作均重新执行一遍，回复速度慢。  
2. 耗内存，占用内存过高。 

## 1.2 安装

+ [官网下载](http://redis.io/ )

+ Redis是C语言开发，安装Redis需要先将官网下载的源码进行编译，编译依赖gcc环境，如果没有gcc环境，需要安装gcc

+ tar zxvf redis-4.0.1.tar.gz -C /opt

+ 进入解压目录，执行：make

+ 编译完成后，执行安装命令，将其安装到指定文件夹：`make PREFIX=/user/local/redis install` 

  PREFIX必须大写，同时会为我们自动创建目录

+ 运行：/user/local/redis/bin/redis-server 后，运行客户端：/user/local/redis/bin/redis-cli

+ 运行远程客户端：redis-cli -h ip地址 -p 端口

## 1.3 配置

Redis 的配置文件位于 Redis 安装目录下，文件名为 **redis.conf**(Windows 名为 redis.windows.conf)。

`cp redis.conf /usr/local/redis`  将配置文件复制到安装文件的目录下

**配置文件详解**

```txt
1. Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程(守护进程(daemon)是一类在后台运行的特殊进程，用于执行特定的系统任务。很多守护进程在系统引导的时候启动，并且一直运行直到系统关闭。另一些只在需要的时候才启动，完成任务后就自动结束。)
    daemonize no
2. 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
    pidfile /var/run/redis.pid
3. 指定Redis监听端口，默认端口为6379，为什么选用6379作为默认端口，因为6379在手机按键上MERZ对应的号码，而MERZ取自意大利歌女Alessia Merz的名字
    port 6379
4. 绑定的主机地址
    bind 127.0.0.1
5. 当客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
    timeout 300
6. 指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose
    loglevel verbose
7. 日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null
    logfile stdout
8. 设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
    databases 16
9. 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
    save <seconds> <changes>
    Redis默认配置文件中提供了三个条件：
    save 900 1
    save 300 10
    save 60 10000
    分别表示900秒（15分钟）内有1个更改，300秒（5分钟）内有10个更改以及60秒内有10000个更改。
 
10. 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
    rdbcompression yes
11. 指定本地数据库文件名，默认值为dump.rdb
    dbfilename dump.rdb
12. 指定本地数据库存放目录
    dir ./
13. 设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
    slaveof <masterip> <masterport>
14. 当master服务设置了密码保护时，slav服务连接master的密码
    masterauth <master-password>
15. 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password>命令提供密码，默认关闭
    requirepass foobared
16. 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
    maxclients 128
17. 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区
    maxmemory <bytes>
18. 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
    appendonly no
19. 指定更新日志文件名，默认为appendonly.aof
     appendfilename appendonly.aof
20. 指定更新日志条件，共有3个可选值： 
    no：表示等操作系统进行数据缓存同步到磁盘（快） 
    always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全） 
    everysec：表示每秒同步一次（折中，默认值）
    appendfsync everysec
 
21. 指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）
     vm-enabled no
22. 虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
     vm-swap-file /tmp/redis.swap
23. 将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据 就是keys),也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0
     vm-max-memory 0
24. Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；如果存储很大大对象，则可以使用更大的page，如果不 确定，就使用默认值
     vm-page-size 32
25. 设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。
     vm-pages 134217728
26. 设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4
     vm-max-threads 4
27. 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启
    glueoutputbuf yes
28. 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法
    hash-max-zipmap-entries 64
    hash-max-zipmap-value 512
29. 指定是否激活重置哈希，默认为开启（后面在介绍Redis的哈希算法时具体介绍）
    activerehashing yes
30. 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
    include /path/to/local.conf
```

## 1.4 Redis中的内存维护策略

redis作为优秀的中间缓存件，时常会存储大量的数据，即使采取了集群部署来动态扩容，也应该及时地整理内存，维持系统性能。

在Redis中有两种解决方案

+ **为数据设置超时时间**

  ```txt
  expire key time(以秒为单位)  这是最常用的方式
  setex(String key,int seconds,String value)    字符串独有的方式
  ```

  + 除了字符串自己独有设置过期时间的方法外，其他数据都需要依靠expire方法来设置过期时间
  + 如果没有设置过期时间，缓存就是永不过期，使用命令 `ttl [key]` 可以查看过期时间(以秒为单位)，大于等于0则为即将过期的秒数，-1则不过期，-2表示过期
  + 如果设置了过期时间，又不想让缓存永不过期，则使用命令：`persist [key]`

+ **采用LRU算法动态地将不用的数据删除**

  内存管理的一种页面置换算法，对于在内存中但又不用的数据块（内存块）叫做LRU，操作系统会根据哪些数据属于LRU而将其移出内存而腾出空间来加载另外的数据。

  **1.volatile-lru：设定超时时间的数据中,删除最不常使用的数据.**(常用)

  **2.allkeys-lru：查询所有的key中最近最不常使用的数据进行删除，这是应用最广泛的策略.**(常用)

  3.volatile-random：在已经设定了超时的数据中随机删除.

  4.allkeys-random：查询所有的key,之后随机删除.

  5.volatile-ttl：查询全部设定超时时间的数据,之后排序,将马上将要过期的数据进行删除操作.

  6.noeviction：如果设置为该属性,则不会进行删除操作,如果内存溢出则报错返回.

  7.volatile-lfu：从所有配置了过期时间的键中驱逐使用频率最少的键

  8.allkeys-lfu：从所有键中驱逐使用频率最少的键

## 1.5 自定义配置Redis

在使用redis时我们需要修改一些必须项：

```txt
daemonize no  修改为 daemonize yes 守护进程启动
bind 127.0.01  注释掉 允许除本机外的机器访问redis服务
requirepass 设置密码 设定数据库密码(保证服务安全、有些情况下不设定密码是无法进行远程连接访问的)
```

Redis采用的是单进程多线程的模式。当redis.conf中选项daemonize设置成yes时，代表开启守护进程模式。在该模式下，redis会在后台运行，并将进程pid号写入至redis.conf选项pidfile设置的文件中，此时redis将一直运行，除非手动kill该进程。但当daemonize选项设置成no时，当前界面将进入redis的命令行界面，exit强制退出或者关闭连接工具(putty,xshell等)都会导致redis进程退出。 **服务端开发的大部分应用都是采用后台运行的模式** 

**配置完成后服务端启动命令：**

```txt
./bin/redis-server ./redis.conf
```

**客户端登录：**

```txt
redis-cli -h host -p port -a password  //redis-cli -h ip地址 -p 端口 -a 密码
```

远程连接时记得开放6379端口

```txt
firewall-cmd --zone=public --add-port=6379/tcp --permanent
firewall-cmd --reload
firewall-cmd --list-ports
```

## 1.6 Redis关闭

**第一种关闭方式：（断电、非正常关闭。容易数据丢失）**

**查询PID   ps -ef | grep -i redis**

**kill -9 PID**

**第二种关闭方式（正常关闭、数据保存）**

**./bin/redis-cli shutdown**     关闭redis服务，通过客户端进行shutdown

如果redis设置了密码，需要先在客户端通过密码登录，再进行shutdown即可关闭服务端

# 二、Redis命令

Redis支持五种数据类型：string（字符串），hash（哈希），list（列表），set（集合），zset（sorted set 有序集合）等

| 命令                     | 操作                                                         |
| ------------------------ | ------------------------------------------------------------ |
| keys *                   | 返回满足的所有键，可以模糊匹配，如keys abc* 表示abc开头的所有key |
| exists key               | 是否存在key，存在返回1，不存在0                              |
| expire key second        | 设置key的过期时间                                            |
| del key                  | 删除key                                                      |
| ttl key                  | 查看key的剩余时间，key不存在返回-2，存在但没有剩余时间返回-1，否则返回以秒为单位的剩余时间 |
| persist key              | 取消剩余时间                                                 |
| PEXPIRE key milliseconds | 修改key的过期时间为毫秒数                                    |
| select  db               | 选择数据库，0-15 默认一共16个数据库，设计成多个数据库是为了数据安全和备份 |
| move key dbindex         | 将数据库中的key转移到其他数据库                              |
| randomkey                | 返回随机一个key（数据库中已经存在的key）                     |
| rename key key2          | 重命名key                                                    |
| echo                     | 打印                                                         |
| dbsize                   | 查看数据库key数量                                            |
| info                     | 查看数据库信息                                               |
| config get *             | 实时传储收到的请求，返回相关的配置                           |
| flushdb                  | 清空当前数据库                                               |
| flushall                 | 清空所有数据库                                               |

**key的命名建议**

+ key不要太长，尽量不超过1024字节，否则会消耗内存，降低查找效率
+ key不要太短，否则可读性降低
+ 在一个项目中，key最好使用同一的命名模式，如 user:123:password;
+ key区分大小写

# 三、数据类型

## 3.1 String

string类型是redis最基本的类型，一个键最大能存储512mb，value可以是string，数字或包含很多其他类型。

string类型是二进制安全的，可以包含任何数据

String命令

```txt
赋值语法：
SET KEY_NAME VALUE: 如果数据库中已经有KEY_NAME,则新值会覆盖旧值
SETNX key1 value：如果key1不存在，就设值，返回1 如果key1存在，则不设值，并返回0
SETEX key1 10 1x : (expired)设置key1的值为1x，过期时间为10s
SETRANGE string range value:替换字符串，替换从range开始的值为value

取值语法：
GET KEY_NAME
GETRANGE key start end:取存储在指定key中字符串的子字符串，包括（start和end在内）
GETBIT key offset： 取指定偏移量上的位(bit)
GETSET KEY_NAME VALUE：设置指定key的值，并返回key的旧值，如果key不存在则返回nil
STRLEN key：返回key所存字符串的值的长度

删值语法：
DEL KEY_NAME

批量写：MSET k1 v1 k2 v2 ...一次性写入多个值
批量读：MGET k1 k2 k3 

GETSET name value：一次性设置和读取，返回旧值，写上新值

自增、自减
INCR KEY_NAME : 将key中存储的数字值+1 如果不存在则会初始化为0，再执行incr操作
INCRBY KEY_NAME num : 加上num
DECR KEY_NAME ： 减值
DECYBY KEY_NAME NUM : 减num

字符串拼接：APPEND KEY_NAME VALUE  追加至末尾，如果不存在则为其赋值

字符串长度：STRLEN KEY
```

**应用场景**

+ string通常用于保存单个字符串或json字符串数据
+ string是二进制安全的，可以把一个图片文件的内容作为字符串来存储
+ 计数器（key-value缓存应用，如微博数，粉丝数）

INCR等指令本身具有原子性。

## 3.2 Hash类型

Hash可以看成是String类型的field和value的映射表，或者说是一个String集合。hash适合存储对象，相比string类型存储对象占用更少的内存空间。

**常用命令**

```txt
赋值语法：
HSET KEY FIELD VALUE：为指定的key 设定fild/value
HMSET KEY FIELD VALUE [field1,value1,....] 同时将多个field-value （域-值）对设置到哈希表key中

取值语法：
HGET KEY FIELD：获取存储在hash中的值，根据field得到value
HMGET KEY [FIELD1,FIELD2...] ：批量获取
HGETALL KEY ：返回所有字段和值

删除语法：
HDEL KEY [field1,field2...]：删除一个或多个hash表字段

其他语法：
HSETNX KEY field value ： 只有在field不存在时，设置哈希表字段的值
HINCRBY KEY field increment：为hash key中的制定字段的整数值加上增量increment
HINCRBYFLOAT KEY field increment：为hash key中的制定字段的浮点数值加上增量increment
HEXISTS KEY field：查看hash key中是否存在field
```

**应用场景**

用于存储一个对象 

## 3.3 List类型

list类型是链表结构的集合，主要功能：push，pop，获取元素等，双端链表，可以作为栈和队列

常用命令：

```txt
赋值语法：
LPUSH key [v1,v2,...] ：一个或多个插入列表头部，左侧添加
	LPUSH L1 a b c d    存储的在数据库中的顺序为： d c b a 
RPUSH key [v1,v2,...] ：一个或多个插入列表尾部，右侧添加
	RPUSH L1 a b c d    存储的在数据库中的顺序为： a b c d 
LPUSHX key value ：将一个值插入已存在的列表头部，不存在列表则操作无效
RPUSHX key value : 将一个值插入已存在的列表尾部，不存在列表则操作无效

取值语法：
LLEN key ：列表长度
LINDEX key index ： 通过索引获取列表元素
LRANGE key start stop ： 获取列表指定范围内的元素 0为第一个，1为第二个，-1为最后一个，-2为倒数第二个

删除语法：
LPOP key ： 移出并获取list的第一个元素（从左侧删除）
RPOP key ： 移出并获取list的最后一个元素（从右侧删除）
BLPOP [key1,key2..] timeout ：移出并获取list的第一个元素，如果没有元素会阻塞列表直到等待超时或发现可弹出元素为止）
BRPOP [key1,key2..] timeout ：移出并获取list的最后一个元素，如果没有元素会阻塞列表直到等待超时或发现可弹出元素为止
LTRIM key start stop ： 对一个列表进行修剪(trim)，只保留指定区间内的元素

修改语法：
LSET key index value: 通过索引设置列表元素的值
LINSERT key BEFORE|AFTER world value：在列表的元素world前或后插入元素value

高级语法：
RPOPLPUSH source destination ：移出source列表的最后一个元素（最右侧），并将该元素添加到destination列表中（最左侧）并返回该元素。
BRPOPLPUSH source destination timeout 
```

**应用场景：**

+ 对数据量大的集合数据进行删减

  关注列表，粉丝列表等

+ 任务队列，如消息队列等

## 3.4 Set类型

Set是string类型的无序集合，集合成员是唯一的，不重复，通过哈希表实现。

**命令** 

```txt
赋值语法：
SADD key [value1,value2...]：向集合添加成员

取值语法：
scard key：返回集合成员数
smembers key：返回集合中所有成员
sismember key member：判断memeber元素是否是集合key成员的成员
srandmember key [count]：返回集合中一个或多个随机数

删除语法：
srem key member1 [member2]：移除集合中一个或多个成员
spop key [count]：移除并返回集合中的一个随机元素
smove source destination member：将member元素从source集合移动到destination集合

差集语法：
sdiff key1 [key2]：返回所有集合的差集
sdiffstore destination key1[key2]：返回给定所有集合的差集并存储在destination中
交集语法：
sinter key1 [key2]：返回所有集合的交集
sinterstore destination key1[key2]：返回给定所有集合的交集并存储在destination中
并集语法：
sunion key1 [key2]：返回所有集合的并集
sunionstore destination key1[key2]：返回给定所有集合的并集并存储在destination中
```

**应用场景：**

**对两个集合间的数据[计算]进行交集、并集、差集运算**
1、以非常方便的实现如共同关注、共同喜好、二度好友等功能。对上面的所有集合操作，你还可以使用不同的命令选择将结果返回给客户端还是存储到一个新的集合中。
2、利用唯一性，可以统计访问网站的所有独立 IP、存取当天或者某天的活跃用户列表。

## 3.5 zset类型

有序且不重复。每个元素都会关联一个double类型的分数，Redis通过分数进行从小到大的排序。分数可以重复

```txt
赋值语法：
ZADD key score1 memeber1[score2 member2] :向有序集合中添加一个或者多个成员，或者更新已有成员的分数

取值语法：
ZCARD key ：获取集合中的元素数量
ZCOUNT key min max 计算在有序集合中指定区间分数的成员数
ZRANK key member：返回有序集合指定成员的索引

ZRANGE key start stop :通过索引区间返回zset指定区间内的成员（低到高）
ZREVRANGE key start stop ：:通过索引区间返回zset指定区间内的成员（高到低）

ZRANGEBYSCORE key min max：通过分数返回有序集合指定区间内的成员（低到高）
ZREVRANGEBYSCORE key min max：通过分数返回有序集合指定区间内的成员（高到低）

删除语法：
DEL key：
ZREM key member [member …] 移除有序集合中的一个或多个成员
ZREMRANGEBYRANK key start stop 移除有序集合中给定的排名区间的所有成员(第一名是0)(低到高排序）
ZREMRANGEBYSCORE key min max 移除有序集合中给定的分数区间的所有成员

修改语法：
ZINCRBY key increment member :增加member元素的分数increment，返回值是修改后的分数
```

常用于排行榜：
1、如推特可以以发表时间作为score来存储
2、存储成绩
3、还可以用zset来做带权重的队列，让重要的任务先执行

## 3.6 HyperLogLog

HyperLogLog是用来做基数统计算法的，其优点是：在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的，并且是很小的。

**常用命令：**

```txt
PFADD key v1 v2... : 添加指定元素到hyperloglog中
PFCOUNT key [key...] :返回给定的hyperloglog的基数估算值
PFMERGE destkey sourcekey[sourcekey..]:将多个hyperloglog合并为一个
```

**应用场景**

基数不大，数据量不大则用不上

只能统计基数数量，而无法知道具体的内容是什么

# 四、Redis 发布订阅功能

Redis发布订阅（pub/sub）是一种消息通信模式，发送者(pub)发送消息，订阅者(sub)接收消息。

```txt
订阅：
subscribe channel [channel…]：订阅一个或多个频道的信息
psubscribe pattern [pattern…]：订阅一个或多个符合规定模式的频道
发布：
publish channel message ：将信息发送到指定频道
退订：
unsubscribe [channel[channel…]]：退订频道
punsubscribe [pattern[pattern…]]：退订所有给定模式的频道
```

# 五、事务功能

Redis事务可以一次执行多个命令，并且：

1. Redis会将一个事务中的所有命令序列化，然后按顺序执行
2. 执行中不会被其他命令插入，不允许加塞行为

常用命令：

```txt
DISCARD: 取消事务，放弃执行事务快内的所有事务
EXEC:执行所有事务块内的命令
MULTI:标记一个事务的开始
UNWATCH:取消WATCH命令对所有key的监视
WATCH key [key...]:监视一个或多个key，如果在事务执行之前这个key被其他命令所改动，则事务会被打断
```

事务开始的执行过程：开始事务---命令入队---执行事务

例如：

```txt
set account:a 100
set account:b 50
multi
decrby account:a 50
incrby account:b 50
get account:a
get account:b
exec（点击回车后依次执行，并打印出最终结果）

multi
set aa 123
discard（执行后，前面的命令都没有执行，退出事务）
```

事务执行时如果执行的某个命令报出了错误，只有出错的命令不会被执行，其他被执行的命令不会回滚。

如果某个命令出现了报告错误（如输入的命令错误，如输入了increaaaaa命令），则整个所以队列都会被取消执行。

# 六、Redis缓存

## 6.1 Redis持久化

Redis支持两种持久化方式：  RDB和AOF

### RDB

RDB是Redis DataBase的缩写，功能核心函数rdbSave(生成RDB文件)和rdbLoad(从文件加载内存)

RDB是Redis默认持久化机制。RDB相当于快照，保存的是一种状态

**优点**：
	保存速度、还原速度极快
	适用于灾难备份
**缺点**：
	小内存的机器不符合使用。RDB机制符合要求就会快照。

**快照条件**

1. 服务器正常关闭  ./bin/redis-cli shutdown

2. key满足一定条件会进行快照

   save 900 1

   save 300 10

   save 60 10000

### AOF

如果Redis意外down掉，RDB方式会丢失最后一次快照后的所有修改。如果要求应用不能丢失任何修改，可以采用AOF持久化方式。

Append-only file：AOF。Redis会将每一个收到的写命令都追加到文件中（默认是appendonly.aof）。当Redis重启时会通过重新执行文件中的写命令重建整个数据库的内容。

每当执行服务器（定时）任务或者函数时，flushAppendOnlyFile函数都会被调用

AOF的缺点：持久化文件会越来越大，有可能有些命令是多余的。	 

## 	6.2 缓存穿透、缓存雪崩、缓存击穿

### 缓存穿透
缓存穿透是指查询一个一定不存在的数据，由于缓存是不命中时需要从数据库查询，如果数据库也查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到数据库去查询，造成缓存穿透。

解决办法： 持久层查询不到就缓存空结果，查询时先判断缓存中是否exists(key) ,如果有直接返回空，没有则查询后返回，
简单的伪代码如下：

```java
//首先到redis中查询数据
if(redis.exist(key)){
	String str = redis.get(key);
	//如果为空字符串那么直接返回null
	if(str == ""){
		return null;
	}
	//不是的话直接返回该字符串
	return str;
//redis 没有数据则到DB中查找
}else{
	String value = selectFromDB(key);
	//如果数据库也没有，那么在redis中也要设置一个key,同时设置失效时间
	//这样下次查询的时就走redis
	if(value == null){
		reids.set(key, "");
		return null;
	}
	redis.set(key, value);
	return value;
}
注意：insert时需清除查询的key，否则即便DB中有值也查询不到(当然也可以设置空缓存的过期时间）
```

### 缓存雪崩
在某一个时间段，缓存集中大量失效时，引发大量查询数据库。

产生雪崩的原因之一，比如在写本文的时候，马上就要到双十二零点，很快就会迎来一波抢购，这波商品时间比较集中的放入了缓存，假设缓存一个小时。那么到了凌晨一点钟的时候，这批商品的缓存就都过期了。而对这批商品的访问查询，都落到了数据库上，对于数据库而言，就会产生周期性的压力波峰。

在做电商项目的时候，一般是采取不同分类商品，缓存不同周期。在同一分类中的商品，加上一个随机因子。这样能尽可能分散缓存过期时间，而且，热门类目的商品缓存时间长一些，冷门类目的商品缓存时间短一些，也能节省缓存服务的资源。

### 缓存击穿
缓存击穿，是指一个key非常热点，在不停的扛着大并发，大并发集中对这一个点进行访问，当这个key在失效的瞬间，持续的大并发就穿破缓存，直接请求数据库，就像在一个屏障上凿开了一个洞。
解决办法：

+ 使用锁，单机用synchronized,lock等，分布式用分布式锁。
+ 缓存过期时间不设置，而是设置在key对应的value里。如果检测到存的时间超过过期时间则异步更新缓存。
+ 在value设置一个比过期时间t0小的过期时间值t1，当t1过期的时候，延长t1并做更新缓存操作。
+ 设置标签缓存，标签缓存设置过期时间，标签缓存过期后，需异步地更新实际缓存

# 七、其他

## 7.1 性能扩展

一般来说，要将Redis运用于工程项目中，只使用一台Redis是万万不能的，原因如下：
1. 从结构上，单个Redis服务器会发生单点故障，并且一台服务器需要处理所有的请求负载，压力较大； (容错性)
2. 从容量上，单个Redis服务器内存容量有限，就算一台Redis服务器内容容量为256G，也不能将所有内容用作Redis存储内存，一般来说，单台Redis最大使用内存不应该超过20G。

### 高可用

“高可用性”（High Availability）通常来描述一个系统经过专门的设计，从而减少停工时间，而保持其服务的高度可用性。(一直都能用)

### 高并发
高并发（High Concurrency）是互联网分布式系统架构设计中必须考虑的因素之一，它通常是指，通过设计保证系统能够同时并行处理很多请求。

高并发相关常用的一些指标有响应时间（Response Time），吞吐量（Throughput），每秒查询率QPS（Query Per Second），并发用户数等。

+ 响应时间：系统对请求做出响应的时间。例如系统处理一个HTTP请求需要200ms，这个200ms就是系统的响应时间。
+ 吞吐量：单位时间内处理的请求数量。
+ QPS：每秒响应请求数。在互联网领域，这个指标和吞吐量区分的没有这么明显。
+ 并发用户数：同时承载正常使用系统功能的用户数量。例如一个即时通讯系统，同时在线量一定程度上代表了系统的并发用户数。

**提升系统的并发能力**
提高系统并发能力的方式，方法论上主要有两种：垂直扩展（Scale Up）与水平扩展（Scale Out）。
+ 垂直扩展：
  垂直扩展：提升单机处理能力。垂直扩展的方式又有两种：

  1. 增强单机硬件性能，例如：增加CPU核数如32核，升级更好的网卡如万兆，升级更好的硬盘如SSD，扩充硬盘容量如2T，扩充系统内存如128G；
  2. 提升单机架构性能，例如：使用Cache来减少IO次数，使用异步来增加单服务吞吐量，使用无锁数据结构来减少响应时间；

  在互联网业务发展非常迅猛的早期，如果预算不是问题，强烈建议使用“增强单机硬件性能”的方式提升系统并发能力，因为这个阶段，公司的战略往往是发展业务抢时间，而“增强单机硬件性能”往往是最快的方法。

  总结：不管是提升单机硬件性能，还是提升单机架构性能，都有一个致命的不足：单机性能总是有极限的。所以互联网分布式架构设计高并发终极解决方案还是水平扩展。

+ 水平扩展：
  水平扩展：只要增加服务器数量，就能线性扩充系统性能。水平扩展对系统架构设计是有要求的，难点在于：如何在架构各层进行可水平扩展的设计，

### 高性能

高性能（High Performance）就是指程序处理速度快，所占内存少，cpu低

## 7.2 主从复制

一个Redis服务可以有多个该服务的复制品，这个Redis服务称为`Master`，其它复制称为`Slaves`，主库只负责写数据，每次有数据更新都将更新的数据同步到它所有的从库，而从库只负责读数据。这样一来，就有了两个好处：

1. 读写分离，不仅可以提高服务器的负载能力，并且可以根据读请求的规模自由增加或者减少从库的数量。
2. 数据被复制成了了好几份，就算有一台机器出现故障，也可以使用其他机器的数据快速恢复。
   需要注意的是：在Redis主从模式中，一台主库可以拥有多个从库，但是一个从库只能隶属于一个主库。

**Redis主从复制配置:**

在Redis中，要实现主从复制架构非常简单，只需要在从数据库的配置文件中加上如下命令即可：

主数据库不需要任何配置，创建一个从数据库：

```txt
-- port 6380 //从服务的端口号
--slaveof 127.0.0.1 6379 //指定主服务器
./bin/redis-server ./redis.conf --port 6380 --slaveof 127.0.0.1 6379
加上slaveof参数启动另一个Redis实例作为从库，并且监听6380端口
```

