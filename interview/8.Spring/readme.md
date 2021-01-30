# Spring
[toc]
# 1. spring中BeanFactory和FactoryBean的区别
[link](https://blog.csdn.net/qiesheng/article/details/72875315)

BeanFactory是个Factory，也就是IOC容器或对象工厂，FactoryBean是个Bean。在Spring中，所有的Bean都是由BeanFactory(也就是IOC容器)来进行管理的。但对FactoryBean而言，这个Bean不是简单的Bean，而是一个能生产或者修饰对象生成的工厂Bean,它的实现与设计模式中的工厂模式和修饰器模式类似。
# 2. IOC/DI  AOP
## 2.1 IOC/DI
Ioc：Inversion of Control —— 控制反转

DI：Dependency Injection —— 依赖注入

## 2.2 AOP
 AOP:Asepct-Orentid-Programming，面向切面编程
