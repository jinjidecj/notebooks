# 1.语法

```objective-c
#import<Foundation/Foundation.h>
int main(){
    @autoreleasepool{
        NSLog(@"hello world");
    }
    return 0;
}
```

+ OC相对于C语言：

  在c的基础上增加了一小部分面向对象的语法，封装，且完全兼容c语言

+ OC程序的源文件后缀名为.m   m代表message （消息机制）

+ main-函数入口 

+ import命令：预处理命令，一个文件无论被import多少次，都只会包含一次

+ Foundation框架：提供最基础的功能--输入和输出，一些数据类型

  ​	foundation.h 包含了Foundation框架的所有头文件

+ @autoreleasepool 自动释放池
+ NSLog函数：printf函数的增强版 ，输出一些调试相关的信息：代码时间，程序名称 ，[进程编号:线程编号]，会自动换行，如果在字符串最后面加"\n"，则自动换行会失效，但依旧会换行。
  + NSLog(@"格式字符串",变量列表)
  + NSLog(@"格式字符串")

**NSString**  指针变量类型，存储OC字符串地址，OC的字符串常量必须使用一个前缀@符号，不加则是C语言的字符串，NSString是不可变的，可变的话需要使用NSMutableString

**NS前缀** NextStep公司来的

OC的关键字绝大多数以@开头

## OC中的数据类型

+ OC支持c语言的所有数据类型
  + 基本数据类型： int double float char
  + 构造类型： 数组  结构体   枚举
  + 指针类型： int* pl;
  + 空类型： void
  + typedef 自定义类型
+ BOOL类型   存储 YES或NO
+ Boolean  存储 true或 false
+ class 类型
+ id类型 万能指针
+ nil
+ SEL 方法选择器
+ block 代码段

## 类

```objective-c
1.声明
@interface 类名:NSObject{
    属性
}
函数
@end
2.实现
@implementation  类名
函数的实现
@end
3.声明
类名 * 对象名 = [类名 new]
```

```objective-c
#import <Foundation/Foundation.h>
@interface Person : NSObject{
    @public
	NSString *_name;
    int _age;
}
- (void) jump;
- (void) eat: (NSString) foodName;
@end
    
@implementation Person
- (void) jump(){
    NSLog(@"jump");
    int _age=1;
    self->_age = 1;
}
@end
int main(){
    Person *p1 = [Person new];
    *p1->_name=@"pp1";
    (*p1)._name=@"p2";
}
```

OC中不存在真正的私有方法，也无法把某个方法标为私有，从而禁止其他代码调用。

### 继承

被继承的类仍然需要方法定义，在方法的定义中可以不写任何内容，或者返回一个虚(dummy)值。

调用父类方法：[super jump : c];当向super发消息时，实际上是在请求OC向该类的超类发送消息。

@class  类名;    创建了一个钱向引用，告诉编译器这是一个类。

### 初始化

```objective-c
Car * car = [[Car alloc] init];
@implementatio Car
-(id)init{
	if(self=[super init]){

	}
	return (self);
}
@end
```

### 属性

```objective-c
@interface car:t{
    float rain;
}
@property float rain;//会自动声明setter和getter方法
@end
implementation car{
    float rain;
}
@synthesize rain;
-(id) init{
    
}
@end
```

### 协议（接口）

```objective-c
@protocol NSCoding
-(void) encodeWithCoder:(NSCoder*) encoder;
@end
    
@interface Car:NSObject<NSCoding>{
    
}
```

### 代码块

```objective-c
int(^name)(int a)=^(int a){return a+1;};
void (^theBlock)()=^{printf("s");};
```







