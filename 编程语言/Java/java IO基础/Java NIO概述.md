## java NIO

### 背景
BIO 即阻塞(blocking) I/O，不管是磁盘 I/O 还是网络 I/O，数据在写入 OutputStream 或者从 InputStream 读取时都有可能会阻塞。一旦有线程阻塞将会失去 CPU 的使用权，这在当前的大规模访问量和有性能要求情况下是不能接受的。


当前一些需要大量 HTTP 长连接的情况，像淘宝现在使用的 Web 旺旺项目，服务端需要同时保持几百万的 HTTP 连接，但是并不是每时每刻这些连接都在传输数据，这种情况下不可能同时创建这么多线程来保持连接。

zwlj：**也就是说，多线程或者多进程的技术确实是一个办法，但这个办法不够好**


*有关于网络IO的5种相关模型，可以去操作系统的笔记中去寻找。*

这些情况都说明，我们需要另外一种新的 I/O 操作方式。java.nio（java non-blocking IO），是jdk1.4 及以上版本里提供的新api（New IO） ，为所有的原始类型（boolean类型除外）提供缓存支持。

它引入了一种基于通道和缓冲区的 I/O 方式，它可以使用 Native 函数库直接分配堆外内存，然后通过一个存储在 Java 堆的 DirectByteBuffer 对象作为这块内存的引用进行操作，避免了在 Java 堆和 Native 堆中来回复制数据。


在操作系统知识中，我们是知道了5种基本的网络IO模型的。

![](image/nio0.jpg)

以socket.read()为例子：

传统的BIO里面socket.read()，如果TCP RecvBuffer里没有数据，函数会一直阻塞，直到收到数据，返回读到的数据。

对于NIO，如果TCP RecvBuffer有数据，就把数据从网卡读到内存，并且返回给用户；反之则直接返回0，永远不会阻塞。

最新的AIO(Async I/O)里面会更进一步：不但等待就绪是非阻塞的，就连数据从网卡到内存的过程也是异步的。

换句话说，BIO里用户最关心“我要读”，NIO里用户最关心"我可以读了"，在AIO模型里用户更需要关注的是“读完了”。

NIO一个重要的特点是：socket主要的读、写、注册和接收函数，在等待就绪阶段都是非阻塞的(通过轮询完成)，真正的I/O操作是同步阻塞的（消耗CPU但性能非常高）。

所以说，NIO中，一个线程请求写入一些数据到某通道，不需要等待它完全写入，这个线程同时可以去做别的事情。 线程通常将非阻塞IO的空闲时间用于**在其它通道上执行IO操作**，所以一个单独的线程现在可以管理多个输入和输出通道（channel）。如若一个线程可以管理好几个通道IO，那么就不需要开大量线程来解决问题了。

### 概述
java NIO主要有三大核心部分：**Channel(通道)，Buffer(缓冲区), Selector**。传统IO基于字节流和字符流进行操作，而NIO基于Channel和Buffer(缓冲区)进行操作，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中。


NIO和传统IO之间第一个最大的区别是，IO是面向流的，NIO是面向缓冲区的。 Java IO面向流意味着每次从流中读一个或多个字节，直至读取所有字节，它们没有被缓存在任何地方。此外，它不能前后移动流中的数据。

zwlj：传统的IO操作，是直接从内核区读取流数据到虚拟机程序InputStream里的，而NIO，则用了类似用户区缓存技术，开辟了一个Buffer，然后通过通道读取。

![](image/nio1.jpg)
