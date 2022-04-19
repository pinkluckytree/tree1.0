## Arrays.sort()详解
Arrays.sort(T[])默认是升序排列

Arrays.sort(T[])使用工具类 DualPivotQuicksort

在这其中有几种原始的sort()算法

1. 快速排序
2. 双轴快速排序
3. 插入排序
4. 计数排序

## 计数排序大致流程
1. 找到最大数与最小数的差值，申请额外的空间，一般是数组arr[max-min+1] ,偏移量为bias=max-min
2. 遍历待排序集合，将每一个值都放在数组对应的位置上 arr[x+bias]++
3. 从前到后取出arr中的数即可

## Arrays.sort源码解释
```java
//源码
    public static <T> void sort(T[] a, Comparator<? super T> c) {
        if (c == null) {
            sort(a);
        } else {
            if (LegacyMergeSort.userRequested)
                legacyMergeSort(a, c);
            else
                TimSort.sort(a, 0, a.length, c, null, 0, 0);
        }
    }
```
```java
//自定义 比较算子，一维数组降序
        //使用这种方式不可使用基本数据类型，需要使用包装类
        Integer []nums=new Integer[100];
        Arrays.sort(nums,new Comparator<Integer>(){
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2-o1;
            }
        });
//自定义比较算子，二维数组根据某一维进行排序
       Arrays.sort(boxTypes,new Comparator<int[]>(){
            @Override
            //返回值为正数就 交换 参数位置，反之不交换
            public int compare(int[] o1, int[] o2) {
                //return o1[1]-o2[1];//升序
                return o2[1]-o1[1];//降序
            }
        });

```




## 使用Deque接口实现队列，栈
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
## 可重入锁设计 Reentrantlock
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




