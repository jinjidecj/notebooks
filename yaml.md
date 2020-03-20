# yaml

## 1.语法规则与数据结构

### 语法规则

+ 大小写敏感
+ 缩进表示层级关系
+ 缩进不允许用tab，只允许空格
+ 缩进的空格数无规定，只要保持相同层级的元素左侧对齐即可

### 数据结构

+ 对象：键值对，又称为映射，哈希，字典
+ 数组：一组按次序排列的值，又称序列，列表
+ 纯量：单个、不可再分的值

## 2.对象

**key: value** 冒号后面要加一个空格

也可用 **key: {key1: value, key2: value2,...}**

 ```yaml
key:
    child-key: value
    child-key2: value2
 ```

## 3. 数组

用一个短横线加一个空格代表一个数组项

```yaml
pets:
	- cat
	- dog
	
pets:
	-
	   id: 1
	   name: jummy
	-
	   id: 2
	   name: pol
```

## 4.复合结构

```yaml
languages:
	- english
	- Chinese
websites:
	yaml: yaml.org
	baidu: baidu.com
```

## 5.纯量

+ 字符串
+ 布尔值
+ 整数
+ 浮点数
+ Null
+ 时间
+ 日期

```yaml
boolean:
	- TRUE #true,True 都可以
	- FALSE #false,False 都可以
float:
	- 3.14
	- 3.1432423e+5 #科学计数法
int：
	- 123
	- 0b1010_0111 #二进制
null:
	nodeName: 'node'
	parent: ~ #使用~表示null
string:
	- haha #字符串不用加单引号或者双引号
	- 'hello world' #可以使用双引号或者单引号包裹特殊字符,双引号不会转义特殊字符,单引号会转义
	- newline
	  newline2 #字符串可以拆分成多行，每一行会被转化成一个空格
date:
	- 2018-02-14 #日期必须使用ISO 8601格式，yyyy-MM-dd
datetime:
	- 2018-02-17T15:02:31+08:00 # 时间使用ISO 8601格式，时间和日期用T链接，最后的+代表时区
```

## 6.引用

``&``锚点和 ``*``别名，可以引来引用

```yaml
defaults: &defaults
  adapter:  postgres
  host:     localhost

development:
  database: myapp_development
  <<: *defaults

test:
  database: myapp_test
  <<: *defaults
```

相当于：

```yaml
defaults:
  adapter:  postgres
  host:     localhost

development:
  database: myapp_development
  adapter:  postgres
  host:     localhost

test:
  database: myapp_test
  adapter:  postgres
  host:     localhost
```

**&** 用来建立锚点（defaults），**<<** 表示合并到当前数据，***** 用来引用锚点。

```yaml
- &showell Steve 
- Clark 
- Brian 
- Oren 
- *showell 
```

相当于

```javascript
[ 'Steve', 'Clark', 'Brian', 'Oren', 'Steve' ]
```

## 7.springboot多个配置文件的切换

文件名的格式是：application-{profile}.properties/yml

#### 激活指定的profile

+ 在主配置文件中指定：spring.profiles.active=dev

+ 命令行 java -jar spring-boot-02-config-0.0.1-SNAPSHOT.jar --spring.profiles.active=dev；

   可以直接在测试的时候，配置传入命令行参数

+ 虚拟机参数；

   -Dspring.profiles.active=dev

#### 使用yml的文档块方式

```yaml
server:
  port: 8081
spring:
  profiles:
    active: prod
---
server:
  port: 8083
spring:
  profiles: dev
---
server:
  port: 8084
spring:
  profiles: prod  #指定属于哪个环境
```