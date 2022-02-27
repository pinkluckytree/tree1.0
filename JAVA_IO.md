# JAVA IO 模型

I/O定义：就是**计算机内存**与**外部设备**之间拷贝数据的过程，（这是因为cpu访问内存的速度极快，一般都需要将数据读到内存进行处理）

一个简单的网络IO通信过程

用户线程等待内核将数据从网卡拷贝到内核空间。

内核将数据从内核空间拷贝到用户空间。

**网卡----->内核空间----->用户空间**
## IO交互方式
1.同步阻塞
用户线程发起 read 调用后就阻塞了，让出 CPU。内核等待网卡数据到来，把数据从网卡拷贝到内核空间，接着把数据拷贝到用户空间，再把用户线程叫醒。

2.同步非阻塞

用户线程不断的发起 read 调用，数据没到内核空间时，每次都返回失败，直到数据到了内核空间，
这一次 read 调用后，在等待数据从内核空间拷贝到用户空间这段时间里，线程还是阻塞的，等数据到了用户空间再把线程叫醒。

3.IO多路复用

同 同步非阻塞 只不过发出的是select调用可以同时查看多个通道

4.异步IO

用户线程发起 read 调用的同时注册一个回调函数，read 立即返回，等内核将数据准备好后，再调用指定的回调函数完成处理。在这个过程中，用户线程一直没有阻塞。

## java IO模型

### BIO(Blocking IO)
基于流 实现

交互方式：同步阻塞（在读取输入流，写入输出流时，线程会一直阻塞，一直到动作完成）

优点是简单有序，缺点是不能应付高并发，并发性不强

### NIO（Non-blocking IO）
基于块 实现

交互方式：IO多路复用，同步非阻塞

#### NIO的优化
1.使用缓存区优化读写流

NIO是基于块的，以块为基本单位处理数据，涉及到两个重要组件，Buffer与channel

buffer是一块连续的内存，可以数据一次性读进内存然后处理，传统方式是边读边处理

channel是访问buffer的接口，可以进行读取缓存，写入数据，是双向的


2.使用DirectBuffer减少内存复制

introduction:可以直接访问物理内存的类 DirectBuffer。普通的 Buffer 分配的是 JVM 堆内存，而 DirectBuffer 是直接分配物理内存。

传统方式                         ：用户空间<--->内核空间（某种意义上就是物理内存）<----->外部设备

使用DirectBuffer，也就是直接内存  ：物理内存（directBuffer）<---->外部设备