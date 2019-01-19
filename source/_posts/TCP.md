---
title: TCP
date: 2019-01-17 11:35:07
tags:
categories:
    - 技术(未分类)
---
### TCP简介
1. TCP是一种面向连接的单播协议，在发送数据前，通信双方必须在彼此间建立一条连接。所谓的“连接”，其实是客户端和服务器的内存里保存的一份关于对方的信息，如ip地址、端口号等。
2. TCP可以看成是一种字节流，它会处理IP层或以下的层的丢包、重复以及错误问题。在连接的建立过程中，双方需要交换一些连接的参数。这些参数可以放在TCP头部。
3. TCP提供了一种可靠、面向连接、字节流、传输层的服务，采用三次握手建立一个连接。采用4次挥手来关闭一个连接。

### TCP keepalive TCP连接保鲜设置
keepalive是TCP保鲜定时器。当网络两端建立了TCP连接之后，闲置idle（双方没有任何数据流发送往来）了tcp_keepalive_time后，服务器内核就会尝试向客户端发送侦测包，来判断TCP连接状况(有可能客户端崩溃、强制关闭了应用、主机不可达等等)。如果没有收到对方的回答(ack包)，则会在tcp_keepalive_intvl后再次尝试发送侦测包，直到收到对对方的ack,如果一直没有收到对方的ack,一共会尝试tcp_keepalive_probes次，每次的间隔时间在这里分别是15s, 30s, 45s, 60s, 75s。如果尝试tcp_keepalive_probes,依然没有收到对方的ack包，则会丢弃该TCP连接。
```
echo 1800 > /proc/sys/net/ipv4/tcp_keepalive_time
echo 15 > /proc/sys/net/ipv4/tcp_keepalive_intvl
echo 5 > /proc/sys/net/ipv4/tcp_keepalive_probes
```

### 滑动窗口
1. TCP的流量控制由滑动窗口来实现的，滑动窗口控制流量取决于接收方的窗口大小。
2. /proc/sys/net/core/rmem_max 定义了接收窗口可以使用的最大值，可以根据BDP值进行调整。
3. /proc/sys/net/core/wmem_max 定义了发送窗口可以使用的最大值，可以根据BDP值进行调整。
4. 滑动窗口
![滑动窗口](tcp/滑动窗口.jpg)

### 有序
* TCP是有序的，保持有序是在TCP这一层做的，TCP层在有小序列号的报文段被填满时才会将大段的报文交给应用层
* 4层协议
![4层协议](tcp/4层协议.png)

### TCP头
* ACK —— 确认。ACK是累积的，一个确认字节号N的ACK表示所有直到N的字节（不包括N）已经成功被接收了
* RST —— 重置连接（经常看到的connection reset by peer）就是此字段搞的鬼。
* SYN —— 用于初如化一个连接的序列号。
* FIN —— 该报文段的发送方已经结束向对方发送数据。
* ISN —— 传包的系列号client和server都有一个。例如在服务器向客户端发包的时会携带一个ISN，服务器回包的时候会把ISN+1传回，如果客户端没有收到ISN+1的回包，就会将系列号为ISN的包重发
    * ISN = M + F
    * M是一个计时器，每隔4毫秒加1。
    * F是一个Hash算法，根据源IP、目的IP、源端口、目的端口生成一个随机数值。
    * tcp序列号回绕
* TCP头
![TCP头](tcp/TCP头.jpg)

### TCP序列号回绕
* pass占位(后续更新)

### 三次握手，四次挥手
1. 三次握手，目的就是双方都能明确自己和对方的收、发是正常的
2. 四次挥手，因为tcp是双工传输，一个FIN一个ACK只关闭了一个方向的传输
3. 三次握手，四次挥手
![三次握手，四次挥手](tcp/三次握手四次挥手.jpg)
4. 状态变迁图
![状态变迁图](tcp/状态变更.jpg)

### syn flood攻击
1. syn flood属于Dos攻击的一种。
2. syn flood步骤
  1. 如果伪造不存在的ip恶意的向某个服务器端口发送大量的SYN包，
  2. 服务器就需要为该请求分配一个TCB，通常一个TCB至少需要280个字节，在某些操作系统中TCB甚至需要1300个字节，并返回一个SYN ACK命令，立即转为SYN-RECEIVED即半开连接状态。系统会为此耗尽资源。
  3. Linux下默认会进行5次重发SYN-ACK包，重试的间隔时间从1s开始，下次的重试间隔时间是前一次的双倍，5次的重试时间间隔为1s, 2s, 4s, 8s, 16s, 总共31s, 称为指数退避，第5次发出后还要等32s才知道第5次也超时了，所以，总共需要 1s + 2s + 4s+ 8s+ 16s + 32s = 63s, TCP才会把断开这个连接。
3. syn flood攻击的效果就是使得服务器的半连接队列爆满（下面会介绍半连接爆满的情况）
4. 常见的防攻击方法有：
  1. 无效连接的监视释放
    * 监视系统的半开连接和不活动连接，当达到一定阈值时拆除这些连接，从而释放系统资源。这种方法对于所有的连接一视同仁，而且由于SYN Flood造成的半开连接数量很大，正常连接请求也被淹没在其中被这种方式误释放掉，因此这种方法属于入门级的SYN Flood方法。
  2. 延缓TCB分配方法
    * Syn Cache技术: 消耗服务器资源主要是因为当SYN数据报文一到达，系统立即分配TCB，从而占用了资源。而SYN Flood由于很难建立起正常连接，因此，当正常连接建立起来后再分配TCB则可以有效地减轻服务器资源的消耗。常见的方法是使用Syn Cache和Syn Cookie技术。
    * Syn Cookie技术: Syn Cookie技术则完全不使用任何存储资源，这种方法比较巧妙，它使用一种特殊的算法生成Sequence Number，这种算法考虑到了对方的IP、端口、己方IP、端口的固定信息，以及对方无法知道而己方比较固定的一些信息，如MSS(Maximum Segment Size，最大报文段大小，指的是TCP报文的最大数据报长度，其中不包括TCP首部长度。)、时间等，在收到对方 的ACK报文后，重新计算一遍，看其是否与对方回应报文中的（Sequence Number-1）相同，从而决定是否分配TCB资源。
  3. 使用SYN Proxy防火墙
    * 一种方式是防止墙dqywb连接的有效性后，防火墙才会向内部服务器发起SYN请求。防火墙代服务器发出的SYN ACK包使用的序列号为c, 而真正的服务器回应的序列号为c', 这样，在每个数据报文经过防火墙的时候进行序列号的修改。另一种方式是防火墙确定了连接的安全后，会发出一个safe reset命令，client会进行重新连接，这时出现的syn报文会直接放行。这样不需要修改序列号了。但是，client需要发起两次握手过程，因此建立连接的时间将会延长。

### 连接队列
1. 服务器端会维护两种队列，
  1. 处于SYN_RCVD状态的SYN(半连接)队列，
  2. 而处于ESTABLISHED状态但仍未被应用程序accept的为全连接队列。
  3. 如果这两个队列满了之后，就会出现各种丢包的情形。
2. 查看是否有连接溢出
```
netstat -s | grep LISTEN
```
3. SYN队列 中linux提供的参数
  1. /proc/sys/net/ipv4/tcp_max_syn_backlog SYN队列的长度，时常称之为未建立连接队列。系统内核维护着这样的一个队列，用于容纳状态为SYN_RESC的TCP连接(half-open connection),即那些依然尚未得到客户端确认(ack)的TCP连接请求。加大该值，可以容纳更多的等待连接的网络连接数。
  2. /proc/sys/net/ipv4/tcp_syn_retries 向远端新建TCP连接请求，需要发送一个SYN包，该值决定内核需要尝试发送多少次syn连接请求才决定放弃建立连接。默认值是5. 对于高负责且通信良好的物理网络而言，调整为2
  3. /proc/sys/net/ipv4/tcp_synack_retries 对于远端SYN连接请求，内核会发送SYN+ACK数据包来确认收到了上一个SYN连接请求包，然后等待远端的确认(ack数据包）。该值则指定了内核会向远端发送tcp_synack_retires次SYN+ACK数据包。默认设定值是5，可以调整为2
  4. /proc/sys/net/ipv4/tcp_syncookies 该选项默认值是1，即启用Syn Cookie技术（介绍见上文）。
4. accept队列 中linux提供的参数
  1. min(/proc/sys/net/ipv4/tcp_max_syn_backlog, /proc/sys/net/core/somaxconn) accept队列队列长度
  2. /proc/sys/net/ipv4/tcp_abort_on_overflow 当accept队列满了之后，tcp_abort_on_overflow为0服务器会直接丢弃该ACK，为1会发送RST通知client。相应的，client则会分别返回read timeout（超时） 或者 connection reset by peer（重置连接）。

### TCP连接断开相关配置
1. tcp_fin_timeout 对于由本端主动断开连接的TCP连接，本端会主动发送一个FIN数据报，在收到远端ACK后,且并没有收到远端FIN包之前，该TCP连接的状态是FIN_WAIT_2状态，此时当远端关闭了应用，网络不可达（拔网张），程序不可断僵死等等，本端会一直保留状态为FIN_WAIT_2状态的TCP连接，该值tcp_fin_timeout则指定了状态为FIN_WAIT_2的TCP连接保存多长时间，一个FIN_WAIT_2的TCP连接最多占1.5k内存。系统默认值是60秒，可以将此值调整为30秒，甚至10秒
2. tcp_max_tw_buckets 系统同时处理TIME_WAIT sockets数目。如果一旦TIME_WAIT tcp连接数超过了这个数目，系统会强制清除并且显示警告消息。设立该限制，主要是防止那些简单的DoS攻击，加大该值有可能消耗更多的内存资源。如果TIME_WAIT socket过多，则有可能耗尽内存资源。默认值是18w,可以将此值设置为5000~30000
3. tcp_tw_resue 是否可以使用TIME_WAIT tcp连接用于建立新的tcp连接。
4. tcp_tw_recycle 是否开启快带回收TIME_WAIT tcp连接的功能。
```
echo 30 >  /proc/sys/net/ipv4/tcp_fin_timeout
echo 15000 > /proc/sys/net/ipv4/tcp_max_tw_buckets
echo 1 > /proc/sys/net/ipv4/tcp_tw_reuse
echo 1 >  /proc/sys/net/ipv4/tcp_tw_recycle
```

### 参考资料
* [三次握手，四次挥手 https://mp.weixin.qq.com/s/YTtvp6gEXesM0WiuTGDW2Q](https://mp.weixin.qq.com/s/YTtvp6gEXesM0WiuTGDW2Q)
* [TCP参数设置 https://www.cnblogs.com/zengkefu/p/5635088.html](https://www.cnblogs.com/zengkefu/p/5635088.html)
