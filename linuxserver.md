#TCP/IP
##主要协议
###数据链路层和网络层
Internet使用主流的协议是TCP/IP协议族，它是一个分层的，多协议的通信协议，四层协议：自底而上分别是数据链路层(ARP,Data Link)、网络层(ICMP,IP)、传输层(TCP,UDP)和应用层(ping,telnet,DNS)。数据链路层的两个常用的协议是ARP(adress resolve proto
col,地址解析协议)和RAPR(reverse)，实现ip和物理地址之间的相互转化。网络层对上层隐藏了网络拓扑连接中的细节（wan、lan），网络层的ip协议如果不能直接发送给目标主机，那么ip就为他寻找合适的下一跳，交给该路由器转发，ICMP协议并非严格意义上的
网络层协议，因为它使用同一层的IP协议提供的服务。传输层协议主要有三个：TCP，UDP，和SCTP。
###传输层
TCP：使用可靠的（使用超时重传、数据确认等方式来确保数据被正确的发送到目的端）面向连接的（使用tcp连接的双方必须是建立tcp连接，并在内核中为该连接维持一些必要的数据结构，比如连接的状态，读写缓存区，以及诸多的定时器，当通信结束时，双方关
连接并释放这些内核数据）和基于流（基于流的数据没有长度限制，它源源不断的从通信的一端流向另一端）的服务。

UDP：为应用层提供不可靠的（无法保证数据从发送端正确的传送到目的端，如果在中途丢失或者目的端使用数据校验发现数据错误而将它丢弃，则udp只是简单的通知应用程序发送失败）无连接的（通信双方不保持一个长久的连接，每次发送数据需要明确指定接受端
地址）基于数据报（每个udp的数据报都有一个长度，接收端必须以该长度为最小长度将其内容一次性读出，否则数据将被截断）的服务。

SCTP：为因特网上传输电话信号而设计的传输层协议。

###应用层
应用层处理应用程序的逻辑。少数服务器程序处于内核空间。大部分处于用户空间的网络编程。
ping:是应用程序，不是协议。利用ICMP报文检测网络连接。直接跳过传输层直接使用网络层提供的服务。
telnet:远程登录协议。
OSPF：动态路由更新协议。
DNS:域名到ip的转换。

##封装
上层如何使用下层提供的服务通过封装实现的。每层协议都在上层数据的基础上加上自己的头部信息，以实现该层的功能。tcp报文，udp报文，ip数据报，数据链路层封装后称为帧。根据传输媒介的不同，以太网上传输的是以太帧，6字节的目的物理地址，4字节的CRC
提供循环冗余校验。帧的最大传输单元MTU受网络类型的限制，以太帧是1500字节，过长的ip数据报可能需要分片传输。

##分用
当帧到达目的主机时，将沿着协议栈自底而上一次传递，最终交给应用程序，不同的帧类型交给不同的模块，如ip模块，arp模块。
TCP，UDP，根据ip数据报的ip协议字段来区分他们，tcp报文段和udp报文段通过端口号来区分。

##ARP协议工作原理
###详解
ARP协议能实现任意网络层地址到任意物理地址的转换，原理：主机向所在的网络广播一个arp请求，该请求包含目标机器的网络地址，次网络上的其他机器收到这个请求，但是只有被请求的目标机器会回应一个arp应答，其中包含自己的物理地址。
- 硬件类型字段定于物理地址的类型，1表示mac
- 协议类型字段表示要映射的协议地址类型，0x800，表示ip地址
- 硬件地址长度字段和协议地址长度，对mac地址长度为6，ipv4长度为4
- 操作字段支出4中操作类型，arp请求（1）arp应答（2）rarp请求（3）rarp应答（4）
- 最后4个字段指定通信双方的以太网地址和ip地址
一个arp请求应答报文长度为28字节，加上以太帧头部和尾部18字节，则一个帧最长度为46字节。arp维护了一个高速缓存。
tcpdump arp
15:49:08.065775 ARP, Request who-has 192.168.19.244 tell 192.168.19.200, length 46
15:49:08.065804 ARP, Reply 192.168.19.244 is-at 00:50:56:ab:32:2d (oui Unknown), length 28
向dns服务器询问244的地址。
tcpdump host 192.168.19.244
1stening on eth0, link-type EN10MB (Ethernet), capture size 65535 bytes
15:43:49.463037 IP 192.168.19.242.29348 > 192.168.19.244.http: Flags [S], seq 3868978780, win 14600, options [mss 1460,nop,nop,sackOK,nop,wscale 9], length 0
15:43:49.463082 IP 192.168.19.244.http > 192.168.19.242.29348: Flags [S.], seq 924688772, ack 3868978781, win 14600, options [mss 1460,nop,nop,sackOK,nop,wscale 9], length 0
15:43:49.463298 IP 192.168.19.242.29348 > 192.168.19.244.http: Flags [.], ack 1, win 29, length 0
15:43:49.463401 IP 192.168.19.242.29348 > 192.168.19.244.http: Flags [P.], seq 1:211, ack 1, win 29, length 210
15:43:49.463415 IP 192.168.19.244.http > 192.168.19.242.29348: Flags [.], ack 211, win 31, length 0
15:43:49.476343 IP 192.168.19.244.http > 192.168.19.242.29348: Flags [P.], seq 1:221, ack 211, win 31, length 220
15:43:49.476570 IP 192.168.19.242.29348 > 192.168.19.244.http: Flags [.], ack 221, win 31, length 0
15:43:49.476848 IP 192.168.19.242.29348 > 192.168.19.244.http: Flags [F.], seq 211, ack 221, win 31, length 0
15:43:49.478960 IP 192.168.19.244.http > 192.168.19.242.29348: Flags [F.], seq 221, ack 212, win 31, length 0
15:43:49.479349 IP 192.168.19.242.29348 > 192.168.19.244.http: Flags [.], ack 222, win 31, length 0

10 packets captured
10 packets received by filter
0 packets dropped by kernel5:43:49.463037 IP 192.168.19.242.29348 > 192.168.19.244.http: Flags [S], seq 3868978780, win 14600, options [mss 1460,nop,nop,sackOK,nop,wscale 9], length 0

ff:ff:ff:ff:ff:ff以太网的广播地址
##DNS工作原理
###详解
域名查询服务有很多的实现方式，如nis,dns和本地静态文件等。
DNS是一套分布式的域名服务系统，16位的标识字段用于标记一对dns查询和应答，一次来区分一个dns应答是哪个dns查询的回应。16位的标志用于协商具体的通信方式和反馈通信状态，dns报头的16位标识字段的细节如下：
QR：1位，0表名查询，1标识应答。
opcode:查询和应答的类型，0表示是标准查询，1表示反向查询（ip到主机域名）,2表示请求服务器状态。
AA：授权应答标识，仅由应答报文使用，1标识域名服务器是授权服务器。1位
TC：仅当dns报文使用udp服务时使用，因为udp数据报有长度限制，所以过长的dns报文将被截断，1标识dns报文超过521字节，被截断。1位
RD：递归查询标识，1标识执行递归查询，0表示执行迭代递归，如果目标服务器无法解析某个主机名，则将自己知道的其他的dns的服务器ip返回给客户端。1位
RA：允许递归标识，仅有应答报文使用，1标识dns服务器支持递归查询。1位
zero：3位未用，必须设置为0
rcode：返回码，0标识无状态，3标识域名不存在

类型A,值是1，标识获取目标主机的ip地址
CNAME：值是5，获得主机的别名
PTR：值是12，标识反向查询

16位查询类通常为1，

###linux下的dns服务
在/etc/resolv.conf 存放dns服务器的ip，如：
nameserver 192.168.2.180
nameserver 192.168.2.200
search int.uops.cn
options timeout:1 attempts:2

测试：开启tcpdump
tcpdump  -i eth0 -nt -s 500 port domain
host -t A www.baidu.com 
结果：
listening on eth0, link-type EN10MB (Ethernet), capture size 500 bytes
IP 192.168.3.2.16553 > 192.168.3.1.domain: 29564+ A? www.baidu.com. (31)
IP 192.168.3.1.domain > 192.168.3.2.16553: 29564 3/5/5 CNAME www.a.shifen.com., A 115.239.210.27, A 115.239.211.112 (260)

##IP协议
IP头部信息：出现在每个IP数据报，用于指定ip通信的远端ip和目的端的ip，指导ip分组和重组。不能用来表示接受顺序。
ip数据报的路由和转发
ip协议提供无状态（独立没有上下文，无法处理乱序和重复的ip数据报）无连接（不长久的维持对方的任何信息）不可靠（不能保证能准确的达到目的端，如路由发现ip数据长久的存活，TTL判断，则丢弃，并返回一个超时错误，而不会重传）
/etc/protocols中定义了上层协议对应的字段的数值。16为头部校验和，用于检测（仅检测头部）在传输过程中是否损坏。
###ip分片
ip数据报的长度超过帧的MTU，将被分片传输，分片可能发生在发送端。第一个分片有MF标志，第二个没有表示已经是最后的了。
###ip路由
IP协议的一个核心任务是ip路由。
route命令：
1.查找路由表和数据报的目标ip完全匹配的主机ip地址，找不到转步骤2
2.查找路由表中和目标ip具有相同路id的网络ip地址，没有找到转步骤3
3.默认路由项，吓一跳路由是网关
##TCP
tcp头部信息（端口号，控制两个方向的数据流）tcp状态转移（连接的每个端都是状态机，tcp的状态转移），tcp数据流，tcp数据流的控制（内核控制，超时重传和拥塞控制）。
tcp协议是一对一的，基于广播和多播的不能使用tcp
tcp头部结构：16位端口号，客户端通常使用系统自动选择的临时端口号，而服务器端口选择知名服务器端口，/ect/services，
32位序号，一次tcp通信过程中字节流的每个字节的编号，A发送B的进行通信第一个tcp报文段中，序号被系统初始化为ISN，后续的tcp报文段序号值将被系统设置为ISN，加上该报文段所携带数据的第一个字节在字节流中的偏移。

16:16:54.611823 IP 192.168.19.108.43137 > 192.168.19.111.http: Flags [S], seq 2155597558, win 14600, options [mss 1460,nop,nop,sackOK,nop,wscale 9], length 0
16:16:54.611862 IP 192.168.19.111.http > 192.168.19.108.43137: Flags [S.], seq 1592817050, ack 2155597559, win 14600, options [mss 1460,nop,nop,sackOK,nop,wscale 9], length 0
16:16:54.612122 IP 192.168.19.108.43137 > 192.168.19.111.http: Flags [.], ack 1592817051, win 29, length 0
16:16:54.612307 IP 192.168.19.108.43137 > 192.168.19.111.http: Flags [P.], seq 2155597559:2155597637, ack 1592817051, win 29, length 78
16:16:54.612325 IP 192.168.19.111.http > 192.168.19.108.43137: Flags [.], ack 2155597637, win 29, length 0
16:16:54.612639 IP 192.168.19.108.43137 > 192.168.19.111.http: Flags [P.], seq 2155597637:2155597772, ack 1592817051, win 29, length 135
16:16:54.612660 IP 192.168.19.111.http > 192.168.19.108.43137: Flags [.], ack 2155597772, win 31, length 0
16:16:54.620475 IP 192.168.19.111.http > 192.168.19.108.43137: Flags [P.], seq 1592817051:1592817274, ack 2155597772, win 31, length 223
16:16:54.620578 IP 192.168.19.111.http > 192.168.19.108.43137: Flags [F.], seq 1592817274, ack 2155597772, win 31, length 0
16:16:54.621012 IP 192.168.19.108.43137 > 192.168.19.111.http: Flags [.], ack 1592817274, win 31, length 0
16:16:54.621352 IP 192.168.19.108.43137 > 192.168.19.111.http: Flags [F.], seq 2155597772, ack 1592817275, win 31, length 0
16:16:54.621394 IP 192.168.19.111.http > 192.168.19.108.43137: Flags [.], ack 2155597773, win 31, length 0

第一个TCP报文段包含SYN标识，标识它是一个同步报文段，包含一个ISN值为2653998486的字段，第二个也是同步报文段，标识同意建立连接。确认值是ack 2653998487，即加1。

###TCP状态转移图
服务器通过listen系统调用进入LISTEN状态，被动等待客户端连接，服务端一旦监听到某个连接请求，就将该连接放入内核等待队列中，并向客户端发送待syn标志的确认报文段，此时连接处于SYN_RCVD状态，如果服务器成功的接收到客户端发送的确认报文段，
则连接转移到ESTABLISHED状态，ESTABLISHED是双方能够进行双向数据传输的状态。
FIN_WAIT1:客户端主动关闭连接，它将想服务器发送一个结束报文段。
FIN_WAIT2:收到服务端的专门用于确认报文段，连接转移到此状态。
###TIME_WAIT状态
客户端收到服务器的结束报文段，并没有直接进入CLOSED状态，在这个状态，客户端连接要等待一段时长为2MSL，才能完全关闭，rfc建议值是2min。
TIME_WAIT状态的存在的原因有两点：
+ 可靠的终止tcp连接。（）
+ 保证让迟来的tcp报文段有足够的时间被识别并丢弃。
在一个linux上一个tcp端口,不能被同时打开多次。假如没有time_wait将会延迟的报文段和新建立的tcp公用一个端口。
###拥塞控制
tcp还能提高网络利用率，降低丢包率，保证网络资源最每条数据的公平性，这就是拥塞控制。
在/proc/sys/net/ipv4/tcp_congestion_control中存放了当前使用的拥塞控制算法。
拥塞控制的最终受控量是发送端向网络一次连续写入的数据量，SWND（发送窗口），不过是使用tcp来发送数据的，限定了能连续发送的tcp报文段的数量，报文段的最大长度称为SMSS，如果swnd太小，会引起明显的网络延迟，太大会引起网络拥塞。
慢启动：每次发送cwnd每收到接受端一个确认，cwnd就按指数式扩大，当cwnd的大小超过该值时，tcp将进入到拥塞避免阶段，