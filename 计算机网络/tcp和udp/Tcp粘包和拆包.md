## TCP粘包和拆包问题
TCP 是一个“流”协议，所谓流就是没有界限的一串数据。TCP并不了解上层业务数据的具体定义，它只会根据TCP缓冲区的实际情况进行包的划分，所以在业务上认为，**一个完整的包可能会被TCP拆分成多个包进行发送（拆包）**；也有可能 **把多个小的包封装成一个大的数据包一起发送(黏包)**。

我们都知道TCP属于传输层的协议，传输层除了有TCP协议外还有UDP协议。那么UDP是否会发生粘包或拆包的现象呢？答案是不会。UDP是基于报文发送的，从UDP的帧结构可以看出，在UDP首部采用了16bit来指示UDP数据报文的长度，因此在应用层能很好的将不同的数据报文区分开，从而避免粘包和拆包的问题。而TCP是基于字节流的，虽然应用层和TCP传输层之间的数据交互是大小不等的数据块，但是TCP把这些数据块仅仅看成一连串无结构的字节流，没有边界；另外从TCP的帧结构也可以看出，在TCP的首部没有表示数据长度的字段，基于上面两点，在使用TCP传输数据时，才有粘包或者拆包现象发生的可能。

### 表现形式
现在假设客户端向服务端连续发送了两个数据包，用packet1和packet2来表示，那么服务端收到的数据可以分为三种，现列举如下：

第一种情况，接收端正常收到两个数据包，即没有发生拆包和粘包的现象，此种情况不在本文的讨论范围内。

![](image/tcp14.jpg)

第二种情况，接收端只收到一个数据包，由于TCP是不会出现丢包的，所以这一个数据包中包含了发送端发送的两个数据包的信息，这种现象即为粘包。这种情况由于接收端不知道这两个数据包的界限，所以对于接收端来说很难处理。

![](image/tcp15.jpg)

第三种情况，这种情况有两种表现形式，如下图。接收端收到了两个数据包，但是这两个数据包要么是不完整的，要么就是多出来一块，这种情况即发生了拆包和粘包。这两种情况如果不加特殊处理，对于接收端同样是不好处理的。

![](image/tcp16.jpg)

### 粘包、拆包发生原因
发生TCP粘包或拆包有很多原因，现列出常见的几点，可能不全面，欢迎补充，

1. 要发送的数据大于TCP发送缓冲区剩余空间大小，将会发生拆包。

2. 待发送数据**大于MSS（最大报文长度），TCP在传输前将进行拆包**。

3. 要发送的数据**小于TCP发送缓冲区的大小**，TCP将多次写入缓冲区的数据一次发送出去，**将会发生粘包**。

4. 接收数据端的应用层**没有及时读取接收缓冲区中的数据，将发生粘包**。

### 粘包、拆包解决办法
通过以上分析，我们清楚了粘包或拆包发生的原因，那么如何解决这个问题呢？解决问题的关键在于如何给每个数据包添加边界信息，常用的方法有如下几个：

1. 发送端给每个数据包添加包首部，**首部中应该至少包含数据包的长度**，这样接收端在接收到数据后，通过读取包首部的长度字段，便知道每一个数据包的实际长度了。

2. 发送端将每个数据包**封装为固定长度**（不够的可以通过补0填充），这样接收端每次从接收缓冲区中读取固定长度的数据就自然而然的把每个数据包拆分开来。

3. **可以在数据包之间设置边界，如添加特殊符号，这样，接收端通过这个边界就可以将不同的数据包拆分开**。
