# java反射 Reflection

## 概述
概述：java反射就是在 **运行状态** 中，对于任意一个类，能够得到它的所有成员（所有属性和方法）；对于任意一个对象，能够调用它的所有属性和方法，这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。

反射就是把java类中的各种成分映射成一个个的java对象

## java反射的实现
java反射的实现所需的包都在 java.lang.Reflection中

1. Class类：代表一个类
2. Field类：代表类的成员变量（类的属性）
3. Method类：代表类的方法
4. Constructor类：代表类的构造器
5. Array类：提供了动态创建数组，以及访问数组的元素的静态方法

## 获取Class对象的三种方法
1. 使用Class.forName() 静态方法获取
2. 使用object类的getClass()方法获取
3. 使用类名+.class

## 判断是否为某个对象的实例的两种方法
1. 使用instanceof 关键字
2. 使用 Class类的isInstance()方法,这是一个native方法
