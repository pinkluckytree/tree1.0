# 操作系统的一些问题

## 并发，共享，虚拟，异步
**虚拟** ：把一个物理实体转变为多个逻辑实体，物理实体是虚拟的，逻辑实体是虚拟的
         
具体表现为：1. 时分复用技术
           2. 空分复用技术

## 进程管理（与线程管理）
### 进程与线程的区别
1. 基本单位：进程是操作系统分配资源的基本单位，线程是cpu调度的基本单位
2. 归属关系：进程至少有一个线程（1：n）
3. 系统开销：进程开销比线程大
4. 资源：    所有线程共享进程的资源，进程有自己独立的地址空间
5. 通信方式：线程间通信（wait（），sleep（），notify（）等），进程间通信

### 进程间通信
1. 管道（pipe）（不需亲缘关系）/匿名管道（有亲缘关系）
2. 消息队列
3. 管程（monitor），信号量（semaphore）
4. 共享内存： 也可以称为竞争条件（两个或多个进程读写某些共享数据，最后结果取决于进程运行的精确时序）
5. 套接字（socket） 不同主机之间的进程的双向通信的端口

### 线程间通信
1. 共享内存：线程之间通过对共享内存的读-写来实现隐式通信
2. 消息传递：线程之间通过明确的发送消息来通信，（其实就是wait(),notify()这些方法）
3. 管道

### 进程调度算法
1. FCFS 先来先服务
2. SJF 短作业有限
3. 最短剩余时间优先
4. 时间片轮询
5. （静态/动态）优先级调度
6. CTSS(compatible Time Sharing System)多级队列：属于最高优先级类的进程运行一个时间片，次高运行两个... 类推 ，当一个进程运行完分配的时间片后就将其放入下一优先级类

## 内存管理

**虚拟内存的思想**（分页）：每个程序有自己的地址空间，这个空间被分割成很多个块，每一个块被称为一个 **页**或者**页面**，每一页有连续的地址范围。这些页被映射到物理内存，
但是并不是所有的页都必须在内存中。
当程序引用到一部分在物理内存中的地址空间时，由硬件执行必要的映射。
当程序引用到一部分不在物理内存中的地址空间时，由操作系统负责将缺失的部分装入物理内存并重新执行失败的指令

### 虚拟地址与地址空间（简略理解）
程序产出的地址称为**虚拟地址**（virtual address），它们构成了一个**虚拟地址空间**(virtual address space)

如果没有虚拟内存，系统将直接将虚拟地址送到内存总线上，读写具有相同地址的物理内存

如果有虚拟内存，  虚拟内存不是被直接送到内存总线上，而是被送到**内存管理单元**（Memory Management Unit,MMU），由MMU把虚拟地址映射为物理内存地址

### 分页，页面，页框
页面： 虚拟地址空间按照固定大小划分称为**页面**

页框： 就是页面在物理内存中对应的地址单元

例如：64KB的虚拟地址空间和32KB的物理内存可以得到16个页面，8个页框

### 缺页中断和页面置换算法
缺页中断：当使用到一个页面，MMU发现该页面在物理内存没有被映射，也就是没有对应的页框。（操作系统需要找到一个很少使用的页框并把它的内容写入磁盘，然后把需要访问的页面读到刚才回收的页框中，修改映射关系）

#### 页面置换算法
1. 最优页面置换算法： 置换最晚才会访问的页面
2. 最近未使用页面置换算法(NRU)：淘汰一个最近没有被访问的已修改页面
3. 先进先出页面置换算法（FIFO）：由操作系统维护一个所有当前在内存中的页面的链表，最早进入的放在表头，最新进入的放在表尾，先淘汰表头的页面
4. 第二次机会页面置换算法：同3，只是添加了一个标志位，若为0，则淘汰，若为1则清零放入表尾
5. 时钟页面置换算法：和4思路一样，只是不需要变动链表，使用环形链表，用一个变量（表针）指示当前到达位置
6. 最近最少使用页面置换算法（LRU）：用一个链表维护所有页面的访问次数，
7. 