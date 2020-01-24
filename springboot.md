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