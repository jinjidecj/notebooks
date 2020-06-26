# Lambda 表达式

lambda表达式（也称为闭包），允许把函数作为一个方法的参数（函数作为参数传递到方法中），或者把代码看成数据，用于简化接口式的匿名内部类（即函数式接口，函数式接口是指一个具有一个方法的普通接口，如果该接口还有默认方法和静态方法，也不影响lambda的使用）。函数式接口可以被隐式转换为lambda表达式。

语法：

```java
(parm,parm...)->{...}
```

举例：

```java
interface IEat{
    public void eat();
}
class IEatImpl implements  IEat{
    @Override
    public void eat() {
        System.out.println("eat apple");
    }
}

public class LambdaDemo {
    public static void main(String[] args) {
        //正常使用接口的方式
        IEat iEat1 = new IEatImpl();
        iEat1.eat();
        IEat iEat2 = new IEat() {
            @Override
            public void eat() {
                System.out.println("eat banana");
            }
        };
        iEat2.eat();

        //使用lambda表达式
        IEat iEat3 = ()-> System.out.println("eat pear");
        iEat3.eat();
    }
}
/*
eat apple
eat banana
eat pear
*/
//当函数需要参数时，需要这么写
interface IDrink{
    public void drink(String drink);
}
IDrink drinkImpl = (String drink)-> System.out.println("drink"+drink);//此处可以省略drink的数据类型
drinkImpl.drink("juice");

//当有返回值时，单个表达式就会return该表达式的值，多个表达式就要写return

//实例：
class Student{
    int age;
    String name;
}
Student[] students = {
    new Student("张三",19),
    new Student("张三1`",13),
    new Student("张三2",14),
}
//常规写法：
Arrays.sort(students,new Comparator<Student>(){
    public int compare(Student o1,Student o2){
        return o1.getAge() - o2.getAge();
    }
});
//使用lambda表达式
Comparator<Student> c = (o1,o2)->o1.getAge()-o2.getAge();
Arrays.sort(students,c);
//或者：
Arrays.sort(students,(o1,o2)->o1.getAge()-o2.getAge());
```

使用lambda表达式的好处：

+ 代码更简洁
+ 不会单独生成class文件

