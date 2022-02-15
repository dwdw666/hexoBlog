---
title: java中的重写与重载
date: 2022-02-15 09:56:50
tags:   
---

重写
----

子类重写父类的方法时需要保持参数和返回值的一致性，否则编译器就会报错。
下面这个例子我们发现在*circle*类是属于Shape类对象的一个实例，在编译阶段只会检查*Shape*类中是否有*testPol*y这个方法。
而在运行阶段*jvm*则会访问*circle*实例上的*testPoly*方法

```
public class test{
 public static void main(String args[]){
  Shape circle = new Circle();
  circle.testPoly();
 }
}
class Shape{
 public void testPoly(){
  System.out.println("shape");
 }
}
class Circle extends Shape{
 //重写父类的方法 保持参数和返回值的一致性
 public void testPoly(){
  System.out.println("circle");
 }
}
```

### 重写的规则（摘自互联网）

* 参数列表与被重写方法的参数列表必须完全相同。

* 返回类型与被重写方法的返回类型可以不相同，但是必须是父类返回值的派生类（java5 及更早版本返回类型要一样，java7 及更高版本可以不同）。
* 访问权限不能比父类中被重写的方法的访问权限更低。例如：如果父类的一个方法被声明为 public，那么在子类中重写该方法就不能声明为 protected。
* 父类的成员方法只能被它的子类重写。
* 声明为 final 的方法不能被重写。
* 声明为 static 的方法不能被重写，但是能够被再次声明。
* 子类和父类在同一个包中，那么子类可以重写父类所有方法，除了声明为 private 和 final 的方法。
* 子类和父类不在同一个包中，那么子类只能够重写父类的声明为 public 和 protected 的非 final 方法。
* 重写的方法能够抛出任何非强制异常，无论被重写的方法是否抛出异常。但是，重写的方法不能抛出新的强制性异常，或者比被重写方法声明的更广泛的强制性异常，反之则可以。
* 构造方法不能被重写。
* 如果不能继承一个类，则不能重写该类的方法。

重载
----

重载的时候必须在类内部，其中 函数的参数列表必须不一样，不然会报错

```
public void test1(){
  System.out.println("test1");
 }
 public int test1(int param){
  System.out.println("test2");
  return 2;
 }
```

总结
----

类的重写是多态在**父类与子类**中的一种体现，
而类的重载是多态在一个**类内部**的一种体现
