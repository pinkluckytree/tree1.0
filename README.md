# tree1.0
# java运行时内存区域
## 程序计数器
## jvm虚拟机栈

虚拟机栈的每一个栈帧都包括四个部分：

局部变量表

操作数栈

动态链接（常量池引用）

方法出口：返回地址
## 本地方法栈
## 堆
## 方法区
## 运行时常量池

# 容器
## 深拷贝与浅拷贝？

浅拷贝只复制指向某个对象的指针，而不复制对象本身，新旧对象还是共享同一块内存。但深拷贝会另外创造一个一模一样的对象，新对象跟原对象不共享内存，修改新对象不会改到原对象。

Java 中所有类都默认继承 java.lang.Object 类，在 java.lang.Object 类中有一个方法 clone()，这个方法将返回 Object 对象的一个拷贝。Object 类里的 clone() 方法仅仅用于浅拷贝（拷贝基本成员属性，对于引用类型仅返回指向改地址的引用）。如果 Java 类需要深拷贝，需要覆写 clone() 方法。

## List与Set的区别？
1>都是继承自Collection接口

2>list元素有序，可重复；set无序，无重复

3>list可以有多个null元素，set只能有一个

## Arraylist与LinkedList区别？

1>扩容方式不同 Arraylist基于动态数组实现，存在容量限制，当元素数超过最大容量时，会自动扩容，LinkedList基于链表没有容量限制 linkedlist内部维护了一个双链表

2>ArrayList 查询更快，插入删除慢；LinkedList 插入删除快，顺序访问会非常高效，而随机访问效率比较低。

3>ArrayList 和 LinkedList 都不是线程安全的。

## 序列化与cloneable
序列化（serialize） - 序列化是将对象转换为字节流。

反序列化（deserialize） - 反序列化是将字节流转换为对象。

Java 通过对象输入输出流来实现序列化和反序列化：

java.io.ObjectOutputStream 类的 writeObject() 方法可以实现序列化；

java.io.ObjectInputStream 类的 readObject() 方法用于实现反序列化。

transient？

当某个字段被声明为 transient 后，默认序列化机制就会忽略该字段的内容,该字段的内容在序列化后无法获得访问。
