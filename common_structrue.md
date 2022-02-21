# 使用Deque接口实现队列，栈
## 栈
        Deque<Integer>stack=new LinkedList<>();
        stack.push(1);
        stack.peek();
        stack.pop();

## 队列
        Deque<Integer>queue=new LinkedList<>();
        queue.offer(1);
        queue.poll();
## 单例模式
属于创建型模式

保证一个类仅有一个实例，并提供一个访问它的全局访问点。
实现的时候关键的两点就是，私有（静态）实例，公开（静态）实例获取方法，私有构造器
```java
//不安全的懒汉式，最简单的线程安全方式就是在getInstance方法前加上synchronized
public class Singleton {
    private  static  Singleton  singleton_instance;
    private Singleton(){};
    public static Singleton getInstance(){
        if(singleton_instance==null){
            singleton_instance=new Singleton();
        }
        return singleton_instance;
    }
    private void showMessage(){
        System.out.println("单例模式");
    }
}
//饿汉式
public class Singleton {
    private static Singleton singleton_instance=new Singleton();
    private Singleton(){};
    public static Singleton getInstance(){
        return singleton_instance;
    }
    private void showMessage(){
        System.out.println("单例模式");
    }
}
```

使用双重校验锁实现线程安全
```java
public class Singleton {  
    private static Singleton instance;  
    private Singleton (){}  
    public static synchronized Singleton getInstance() {  
    if (instance == null) {  
        instance = new Singleton();  
    }  
    return instance;  
    }  
}
```




