ICMP差错报告报文共有5种

 - 终点不可达：终点不可达分为:网络不可达，主机不可达，协议不可达，端口不可达，需要分片但DF比特已置为1，以及源路由失败等六种情况，其代码字段分别置为0至5。当出现*以上六种情况*时就向源站发送终点不可达报文。
 - 端口不可达：UDP的规则之一是：如果**收到UDP数据报**而且目的端口与某个正在使用的进程不相符，那么UDP返回一个ICMP不可达报文。
 - 源站抑制：当**路由器或主机由于拥塞而丢弃数据报**时，就向源站发送源站抑制报文，使源站知道应当将数据报的发送速率放慢。
 - 时间超过：当路由器**收到生存时间为零的数据报**时，除丢弃该数据报外，还要向源站发送时间超过报文。当目的站在预先规定的时间内不能收到一个数据报的全部数据报片时，就将已收到的数据报片都丢弃，并向源站发送时间超过报文。
 - 参数问题：当路由器或目的主机收到的数据报的首部中的字段的值不正确时，就丢弃该数据报，并向源站发送参数问题报文。
 - 改变路由（重定向）路由器将改变路由报文发送给主机，让主机知道下次应将数据报发送给另外的路由器。

以下几种情况都不会导致产生ICMP差错报文

 - ICMP差错报文（但是，ICMP查询报文可能会产生ICMP差错报文）
 - 目的地址是广播地址或多播地址的IP数据报
 - 作为链路层广播的数据报
 - 不是IP分片的第一片
 - 源地址不是单个主机的数据报。即源地址不能为零地址、环回地址、广播地址或多播地址。