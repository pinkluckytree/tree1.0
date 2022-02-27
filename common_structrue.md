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
## 可重入锁设计
可重入锁设计三要点

1.记住加锁次数

2.记住加锁线程

3.是否加锁

```java
public class MyReentrantLock implements Lock {
    private int lockcount=0;
    private Thread lockby=null;
    private boolean isLocked=false;

    @Override
    public synchronized void lock() {//加锁操作
        Thread currentThread=Thread.currentThread();
        while(isLocked&&currentThread!=lockby){ //若已被锁住&&当前线程不是加锁线程 则需要wait() 一直到isLocked==false 也就是解锁
            try {
                wait();
            }
            catch (InterruptedException e){
                e.printStackTrace();
            }
        }
        
        isLocked=true;
        lockby=currentThread;
        lockcount++;
    }
    
    @Override
    public synchronized void unlock() {//解锁操作
        Thread currentThread=Thread.currentThread();
        //若加锁线程就是当前线程
        if(lockby==currentThread){
            lockcount--;
            if(lockcount==0){
                isLocked=false;
                notifyAll();
            }
        }
    }
    @Override
    public void lockInterruptibly() throws InterruptedException {

    }
    @Override
    public boolean tryLock() {
        return false;
    }
    @Override
    public boolean tryLock(long time, TimeUnit unit) throws InterruptedException {
        return false;
    }
    @Override
    public Condition newCondition() {
        return null;
    }
}
```




