## http长连接and短连接
HTTP1.1规定了默认保持长连接（HTTP persistent connection ，也有翻译为持久连接），数据传输完成了保持TCP连接不断开（**不发RST包、不四次握手**），等待在同域名下继续用这个通道传输数据；相反的就是短连接。

HTTP首部的Connection: Keep-alive是HTTP1.0浏览器和服务器的实验性扩展，当前的HTTP1.1 RFC2616文档没有对它做说明，因为它所需要的功能已经默认开启，无须带着它，但是实践中可以发现，浏览器的报文请求都会带上它。如果HTTP1.1版本的HTTP请求报文不希望使用长连接，则要在HTTP请求报文首部加上Connection: close。

#### 长连接的过期时间
客户端的长连接不可能无限期的拿着，会有一个超时时间，服务器有时候会告诉客户端超时时间，譬如：

![](image/persistent0.png)

  上图中的Keep-Alive: timeout=20，表示这个TCP通道可以保持20秒。另外还可能有max=XXX，表示这个长连接最多接收XXX次请求就断开。对于客户端来说，如果服务器没有告诉客户端超时时间也没关系，服务端**可能主动发起四次握手**断开TCP连接，客户端能够知道该TCP连接已经无效；另外TCP还有**心跳包**来检测当前连接是否还活着，方法很多，避免浪费资源。

#### 长连接完成标识
使用长连接之后，客户端、服务端怎么知道本次传输结束呢？两部分：1是判断传输数据是否达到了Content-Length指示的大小；2动态生成的文件没有Content-Length，它是分块传输（chunked），这时候就要根据chunked编码来判断，chunked编码的数据在最后有一个**空chunked块**，表明本次传输数据结束。

更详细的<a>http://www.cnblogs.com/skynet/archive/2010/12/11/1903347.html</a>

#### 并发连接的数量限制

在web开发中需要关注浏览器并发连接的数量，RFC文档说，客户端与服务器最多就连上两通道，但服务器、个人客户端要不要这么做就随人意了，有些服务器就限制同时只能有1个TCP连接，导致客户端的多线程下载（客户端跟服务器连上多条TCP通道同时拉取数据）发挥不了威力，有些服务器则没有限制。

浏览器则限制了同域名下只能启动若干个并发的TCP连接去下载资源。

并发数量的限制也跟长连接有关联，打开一个网页，很多个资源的下载可能就只被放到了少数的几条TCP连接里，这就是TCP通道复用（长连接）。如果**并发连接数少**，意味着网页上所有资源下载完需要更长的时间（用户感觉页面打开卡了）；并发数多了，**服务器可能会产生更高的资源消耗峰值**。浏览器只对同域名下的并发连接做了限制，也就意味着，web开发者可以把资源放到**不同域名**下，同时也把这些资源放到不同的机器上，这样就完美解决了。

### 优缺点

长连接可以**省去较多的TCP建立和关闭的操作，减少浪费，节约时间**。对于频繁请求资源的客户来说，较适用长连接。不过这里存在一个问题，存活功能的探测周期太长，还有就是它只是探测TCP连接的存活，属于比较斯文的做法，遇到恶意的连接时，保活功能就不够使了。在长连接的应用场景下，client端一般不会主动关闭它们之间的连接，Client与server之间的连接如果一直不关闭的话，会存在一个问题，随着客户端连接越来越多，server早晚有扛不住的时候，这时候server端需要采取一些策略，如关闭一些长时间没有读写事件发生的连接，这样可 以避免一些恶意连接导致server端服务受损；如果条件再允许就可以以客户端机器为颗粒度，限制每个客户端的最大长连接数，这样可以完全避免某个蛋疼的客户端连累后端服务。
