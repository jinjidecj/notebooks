```java
@Controller
public class HelloWorldController {
    @ResponseBody //表示返回的数据直接给浏览器，如果是对象会转成json数据
    @RequestMapping("/hello")
    public String hello(){
        return "hello world!";
    }
}
```

```java
@ResponseBody //表示这个类的所有方法返回的数据直接给浏览器，如果是对象会转成json数据
@Controller
//这两个方法可以替换成@RestController 意义一样
public class HelloWorldController {
    @RequestMapping("/hello")
    public String hello(){
        return "hello world!";
    }
}
```

+ resources文件目录：
  + static 保存所有的静态资源 js,css images
  + templates: 保存所有的模板页面 （spring boot 默认jar包使用嵌入式的tomcat，默认不支持jsp页面）；可以使用模板引擎（freemarker,thymeleaf）
  + application.properties:配置文件

## yaml

k:(空格)v

以空格的缩进来控制层级关系，只要是左对齐的一列数据，都是同一个层级的

属性和值也是大小写敏感的

##### 字面量

​	普通的值（数字，字符串，布尔）

 + 字符串不用加单引号或者双引号
 + 双引号不会转义特殊字符
 + 单引号会转义

##### 对象

​	map(属性和值) 键值对

```yaml
friends:
	lastName:zhangsan
	age:20
```

​	行内写法

``` yaml
friends:{last:name,age:10}
```

##### 数组 （list,set)

```yaml
pets:
 - cat
 - dog
 - pig
```

​	行内写法

``` yaml
pets:[cat,dog]
```

##### @value获取值和@ConfigurationProperties获取值的比较

|                      | @ConfigurationProperties | @Value     |
| -------------------- | ------------------------ | ---------- |
| 功能                 | 批量注入配置文件中的属性 | 一个个指定 |
| 松散绑定（松散语法） | 支持                     | 不支持     |
| JSR303数据校验       | 支持                     | 不支持     |
| 复杂类型封装         | 支持                     | 不支持     |
| SpEL                 | 不支持                   | 支持       |

如果在某个业务逻辑中需要获取一下配置文件中的某项值，就使用@Value

如果专门编写一个javaBean来和配置文件进行映射，就直接使用@ConfigurationProperities

## 多个配置文件的切换

文件名的格式是：application-{profile}.properties/yml

#### 激活指定的profile

+ 在主配置文件中指定：spring.profiles.active=dev

+ 命令行 java -jar spring-boot-02-config-0.0.1-SNAPSHOT.jar --spring.profiles.active=dev；

   可以直接在测试的时候，配置传入命令行参数

+ 虚拟机参数；

   -Dspring.profiles.active=dev

#### 如果使用yml，可以使用yml的文档块方式

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

## 配置文件加载位置

以下文件位置：

+ file:./config/
+ file:./
+ classpath:/config/
+ classpath:/

优先级从高到底，优先级高的会覆盖低优先级的配置，**互补配置**

+ 还可以改变默认的配置文件的位置：spring.config.location

  这个时候需要使用命令行参数的形式来定义配置文件的位置，这个新的文件会和默认加载的配置文件形成互补配置，这样做不用重新打包项目，只需要在重新运行项目的时候指定新配置文件的路径：

  java -jar sxxx.jar --spring.config.location=G:/application.properties


## 日志

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

Logger logger = LoggerFactory.getLogger(getClass());
//spring 默认级别由低到高： trace < debug < info < warn < error

logger.trace("trace 日志");
logger.debug("debug 日志");
//spring 默认打印到info级别，trace 和 debug不打印
logger.info("info 日志");
logger.warn("warn 日志");
logger.error("error 日志");
```

```yaml
logging:
  level:
    com:
      zcj: trace    #设置日志打印级别
  file: log/spring.log #日志的位置
  pattern:
  	console: '%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n'
  	file: '%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n'
  	#日志的格式
```

## web开发

#### springboot对静态资源的映射规则

1. 所有/webjars/** 都去classpath:/META-INF/resources/webjars/找资源

   webjars：以jar包的方式引入静态资源

   <https://www.webjars.org/>

   ```xml
   <!--引入jquery-webjar-->在访问的时候只需要写webjars下面资源的名称即可
   		<dependency>
   			<groupId>org.webjars</groupId>
   			<artifactId>jquery</artifactId>
   			<version>3.3.1</version>
   		</dependency>
   ```

   

2. “/**”访问当前项目的任何资源，都去(静态资源的文件夹)找映射：

   ```
   "classpath:/META-INF/resources/", 
   "classpath:/resources/",
   "classpath:/static/", 
   "classpath:/public/" 
   "/"：当前项目的根路径
   ```

   eg:localhost:8080/abc 去静态文件夹里面找abc

3. 欢迎页面

   静态文件夹下的所有index.html 被/** 映射

   如：localhost:8080/  找index.html

4. 所有的 **/favicon.ico 都是在静态资源文件下找，web的图标

通过修改配置来修改静态资源文件夹

```properties
spring.resources.static-loactions=classpath:/hello,classpath:/jitai
```

### thtmeleaf 

#### 1.引入

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

#### 2.使用和语法



