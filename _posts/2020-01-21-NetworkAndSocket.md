---
layout:     post
title: 计算机网络常见问题合集
subtitle:   计算机网络               
date:       2022-05-21
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags: 
    - CS basis
---      

## 计算机网络三种模型       
OSI七层网络模型 是国际标准化组织 ISO 给出的标准参考模型。OSI标准模型将计算机网络从下到上划分为七层：物理层、数据链路层、网络层、传输层、会话层、表示层和应用层。        
TCP/IP四层模型是实际生产中使用的模型，是事实上的国际标准。TCP/IP 四层模型将计算机网络从下到上划分为 网络接口层、网络层、传输层和应用层。        
TCP/IP 五层模型是一种更适合学习的网络模型，它将网络接口层进一步划分为 物理层和数据链路层。其中：      
1. **应用层** 面向用户程序，解决用户进程之间的交互问题。应用层协议定义了应用进程间的通信和交互规则，如 DNS、HTTP、SMTP 等常用协议都属于应用层协议。                

2. **传输层** 面相数据传输，解决两台主机之间数据传输的问题。应用层报文通过传输层传输。两个最知名的传输层协议是 UDP 和 TCP 。            

3. **网络层** 主要解决数据在网络中传播的问题。具体的，两台计算机之间可能会经过多个数据链路、会经过很多通信子网。网络层的任务就是选择合适的路由和交换节点，确保数据及时传送。            
    
4. **数据链路层** 。两台主机之间的通信链路通常由一段又一段的子链路连接组成，数据链路层负责将网络层交付下来的分组封装成帧，在两个相邻节点之间传送数据帧。每一帧包含数据和必要的控制信息（同步信息、地址信息、差错控制等等） 。   

5. **物理层** 物理层传输的单位是比特。物理层存在的意义是实现**相邻网络节点间比特流的透明传送**，尽可能屏蔽掉具体传输介质和物理设备的差异，使数据链路层不必考虑网络的具体传输介质是什么。透明比特流传输指的是经电路传送后的比特流不发生变化。     

#### 五层模型各自包含哪些协议       
1. **应用层**： 超文本传输协议 HTTP，文件传输协议 FTP，简单邮件传输协议 SMTP，域名协议 DNS，安全外壳协议 SSH，动态主机配置协议 DHCP，远程登录协议 TELNET ，实时流协议 RSTP 。         
2. **传输层**： 传输控制协议 TCP， 用户数据报协议 UDP。    
3. **网络层**： 网际协议 IP，地址转换协议 ARP， 反向地址转换协议 RARP， Internet 控制报文协议 ICMP， Internet 组管理协议 IGMP， 路由信息协议 RIP。        
4. **数据链路层**： 自动重传请求协议 ARQ， 带冲突检测的载波侦听多路访问协议 CSMA/CD，点对点协议 PPP。      
5. **物理层**： 主要是规范物理介质及接口，比如 用语规范无线网络连接的 IEEE802.11 协议族 ，有线网使用的 RJ45 接口协议。      


## ARP 协议          

网络层的 ARP 协议负责完成 **IP 地址与物理地址的映射**。每台主机都会在自己的 ARP 缓存区中建立一个 ARP 列表，用以存储IP 地址和 MAC 地址的对应关系。当源主机需要将一个数据包发送到目的主机，会首先检查自己的 ARP 列表中是否存在目的主机 IP 对应的 MAC 地址，如果存在就直接发送到这个 MAC；如果没有就向本网段发起一个 ARP 请求的广播包，查询此目的主机对应的 MAC 地址。         
ARP 请求数据包里包括源主机 IP 和 MAC 以及目的主机 IP。网络中所有主机手到这个 ARP 请求数据包后，会检查数据包中的目的主机 IP 是否和自己的 IP 一致。 如果不一致，则忽略该请求包。如果一致，目的主机首先将发送端的 IP-MAC 关系存入自己的 APR 缓存表，如果表中已有发送端 IP 信息，则覆盖，然后向发送端返回一个 ARP 响应数据包，包含该主机 IP-MAC 信息；源主机收到这个数据包后就将目的主机的 IP-MAC 关系记录到自己的 APR 表中，并开始数据传输。如果源主机一直没有收到响应包，表示 ARP 查询失败。    

## TCP 和 UDP       

1. TCP 是面向连接的，一定要建立端到端之间的连接（三次握手）才能开始通信；UDP 是无连接的，发送端只需要知悉接收端信息，只管发送。        
2. 每一条 TCP 连接只能有两个端点，每一条 TCP 连接只能是点对点的；UDP 支持一对一、一对多、多对一、多对多交互通信。      
3. TCP 是可靠的，UDP 尽最大努力交付，不保证可靠性。       
4. TCP 是面向字节流的，数据没有长度便捷的限制，接收可以逐字节；UDP是面向报文的，每个数据报都有一个长度，接收端必须要改长度为最小单位读出数据。       
   面向字节流的理解是：尽管应用程序和 TCP 的交互是一次一个数据块，但 TCP 把应用程序交付下来的数据仅仅看成是一串无结构的字节流，发送端的报文大小和接收端不必要对应。比如发送端可以分十次发送共1000 字节的数据，而接收端可以一次性读完。面向报文的理解是，UDP 发送的数据和对端的接收一定要是对应的。这是由 TCP 和 UDP 的特性决定的。TCP 是面向连接的是可靠的，通信连接建立起来，只要发送端有序，接收端随便读取。而 UDP 是不可靠的是无连接的，接收端和发送端必须按照约定的格式（报文）才能成功读取内容。      
5. TCP 首部除了源和目的端信息，还包含有控制、序号等部分，至少20字节，UDP 首部只有8字节，更轻便。     
6. UDP 可以实现可靠传输吗？ 可以，Http 3.0 的底层传输层协议 QUIC 就是基于 UDP协议实现的可靠传输。

## TCP 为什么可靠       
TCP 是可靠传输协议，保证对端接收到的字节流和发送方发出的字节流是完全一样的。这种可靠性首先来源于三次握手，三次握手确认服务端和客户端 互相能接受到对方数据 才建立连接进行通信。建立连接之后保证可靠性的机制主要有如下几种：     
1. 校验和机制        
2. 失序包重排序      
3. 重复包丢弃      
4. 序列号和确认应答机制       
5. 重传机制      
6. 流量控制       
7. 拥塞控制      

#### 校验和 CheckSum 机制       

校验和机制指的是发送端在数据发送之前计算报文段的校验和并添加到TCP 首部的 Checksum 字段，接收端接收到数据后按照同样的方法计算校验和并和接收到的 TCP 报文首部的 Checksum 字段进行比较，如果不一致则丢弃该报文段等待发送端重传。       
TCP 在计算校验和的时候需要添加一个十二字节的伪首部，包含源端IP 目的端 IP，保留字节，传输层协议和报文长度，首部在 IP 分组头获取，只用于计算校验和，不被传输。    
UDP 也可以使用校验和机制，使用时也需要添加 12 字节的伪首部。只不过 TCP 协议中校验和机制是必须的，而 UDP 是可选项。        


#### 序列号和确认应答 ACK 机制        

TCP 报文段的首部由一个序列号字段，指的是该报文段第一个字节的序号（一个字节一个序号）。同时报文首部也包含着该报文长度，于是接收端接收到 TCP 报文后会返回给一个确认应答 ACK 信号告知期望接收到的下一个序列号。需要注意的，并不是每发送一个报文接收端都会返回一个 ACK 信号，具体的在 滑动窗口一节进一步解释。         

#### 重传机制       

TCP 报文传输过程中有可能发生报文丢失问题，应用故障，设备过载，服务宕机等等因素都有可能导致报文丢失。重传机制用来应对报文丢失问题。常见的重传机制包括超时重传和快速重传。         

**超时重传：**      

超时重传就是 TCP 发送方在发送报文的时候设定一个定时器，如果在规定时间没没有收到接收方发来的 ACK 确认报文（包括报文段丢失和 ACK 相应丢失两种情况），发送方就会重传这个已发送的报文段。超时重传的等待时间用 RTO (Retransmission TImeOut) 表示。理想的 RTO 应该是略大于报文段往返时间 RTT（Round-Trip TIme） ，太大了，则网络空闲时间增大，降低网络传输效率；太小了将产生不必要的重传导致网络符合增大。受网络环境影响， RTT 的大小也是动态变化的。三次握手建立连接时得到一个 RTT 初始值，而后随着每次发送报文和接收到 ACK 报文的时间差会动态的更新 RTT  ，RTO 随之更新始终保持略大于 RTT。      
如果超时重传也失败了，TCP 协议的策略是将重传的时间间隔加倍，也就是说每一次超时重传失败，都会将下一次重传的超时时间间隔设置为先前值的两倍。这又带来了一个新的问题：超时重传周期可能会比较长。

快速重传机制可以解决超时重传时间间隔加倍带来的重传周期变长的问题。      

**快速重传：**       

快速重传机制不以时间为驱动，而以数据驱动重传。具体的，每当接收方收到比期望序列号大的报文段时，就想发送方发送一个 **冗余ACK** ，指明期望接收到的下一个报文。这句话指的是，接收方收到报文 n-1，返回确认报文 ACK 并期望收到 n ；报文段 n 丢失，后续 n+1, n+2, n+3 正常到达但是接收方由于没有收到报文段 n，仍然返回报文段 n-1 的确认应答 ACK 并期望接收到报文段 n 。 当然由于窗口的存在，接收方不需要丢弃已接收到的报文。当发送方连续接收到三个冗余 ACK，便认为报文段丢失，于是重新发送丢失报文。      

<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/CSbasis/Network01.png"></div>         

这里又有一个问题，重传哪一个报文？假如报文 #2 丢失了，发送端连续收到三份冗余对 #1 的ACK。但是发送端可能发送了 20 份报文，兴许后面丢了一大堆，这三个冗余 ACK 分别是 #6 #17 #20 这三个报文返回来的也说不准。那怎么办呢？  SACK（Selective ACKnoledgement）是一个好办法。       

SACK 是 TCP 的一个可选项，允许 TCP 单独确认非连续的片段，用于告知发送方真正丢失的包，从而只重传丢失的报文。当三次握手时确认使用 SACK，接收方在返回的 ACK 确认应答报文头部添加一个 SACK 字段，用来记录从第一个缺失的报文段到最近一个不连续报文段之间的接收到的报文段信息。以便发送方确认需要重传的报文段。      

#### 滑动窗口        

TCP 每发送一条报文，都要等到对方返回 ACK 才能发送下一条，效率太低。为解决这一难题，TCP 引入了**窗口**的概念，窗口内的数据无需等待确认应答即可发送。TCP 发送端的**窗口大小部分取决于接收方**的窗口大小，去接收方窗口控制参数和发送方拥塞窗口大小的较小值；同时由于TCP 是全双工通信，通信两端各自维护一个自己的接收窗口 size ，位于 TCP 报文首部窗口字段。        

接收端在发送 ACK 确认应答报文时将自己的实时窗口大小 (接收窗口 `rwnd`) 填入首部，并跟随 ACK 消息一起返回给发送方。发送方根据接收到的 `rwnd` 调整自己的发送速度，当窗口大小为零，发送方停止发送并定期向接收方发送窗口探测数据段，直到探测到的窗口大小值不再等于零。       

窗口大小字段占 2 个字节16比特位，最大可表示 65535 字节（注意单位是字节）数据。随着带宽不断提高，65535 已经不够用了，可以使用 Window Size Scaling 选项缩放窗口。这是个移位值。假设 scaling 值为7， 也就代表将 Window Size 的值左移 7 位，即乘以 128。Scaling 最大为 14。      

滑动窗口的实现实际尝试操作系统开辟的一个缓冲区，发送方在确认应答报文 ACK 到达之前必须在缓冲区中保留已发送的数据，只有在 RTO 内收到确认应答报文，才将数据在缓存区删除。       

**累积确认**       

有一种常见的情况，ACK 报文丢失。这种情况下虽然接收端收到了数据，但是发送端没有收到 ACK 以来无法将数据从缓存区删除，而且当时间超过 RTO 还会触发超时重传浪费资源。一种解决方案是 **累积确认**：     
即使发送方没有收到某个报文段的应答 ACK，只要其后续接收到了下一个报文的应答ACK，也认为该报文生共接收，可以将其从窗口移除。       
注意和快速重传中冗余 ACK 的区分，快速重传冗余 ACK 的产生来自于接收端没有接收到期望接收的下一条报文，这也正是累积确认机制有效的来源：如果 ACK#n+1 正常收到，则 ACK#n 是否收到意义不大。因为如果 TCP#n-1 没有被接收，即使 TCP#n 顺利接收发送端也不会接收到 ACK#n+1，而是会收到冗余的 ACK#n。        
通过累积确认机制可以将接收端已经接收但是相应 ACK 丢失的报文从窗口中删除，减轻窗口压力。       

**滑动窗口不意味着无序：TCP 队头阻塞问题**             
TCP 是基于字节流的，TCP 层必须保证接收到的字节数据是完整且有序的。使用滑动串口只是为了提高传输效率，并不意味着接收端是无序接受的。如果较低序号的 TCP 报文在传输中丢失，即使较高序号 TCP 报文已经被到达接收端，也仅仅是停留在内核缓存区，无法被应用层读取。        
这种特性导致了 HTTP 1.1  的队头阻塞问题。         

#### 流量控制             

滑动窗口的作用之一是流量控制，防止发送方无视接收方处理能力疯狂向接收方发送数据导致接收方丢包重传。滑动窗口的大小根据接收方的能力而定，发送方和接收方保持一致，通过滑动窗口，发送方可以根据接收方的接受能力控制发送速率，保证接收方的处理。        
在接收方没有返回 ACK 报文时，发送方窗口中的数据不会被删除。如果窗口已满，后续数据将无法发送并进入窗口，需要等待窗口中数据的 ACK 确认应答报文到达，窗口中对应数据被删除，后续数据才会发送并进入窗口等待 ACK。        


#### 拥塞控制        

当过多的数据注入到网络中，导致网络中的路由或者链路过载，或者说网络整体无法承受数据负荷，会造成大量丢包等问题。拥塞控制通过控制发送端发送速率解决这个问题。       

拥塞控制和流量控制都是控制发送端的发送速率，要区分两者的不同。流量控制是端到端的，只关乎发送端和接收端，问题的来源是发送端发送速率和接收端接收速率的不匹配，接收端通过告知发送端自身实时窗口大小的方式抑制发送端速率。拥塞问题的来源是网络整体，可能来自于路由、也可能来自于链路，还有可能来自于噪声等等，总之就是数据负荷超过了网络的实际承载能力而导致丢包重传。          

为了调节发送方的发送速率，TCP 中定义了 **拥塞窗口`cwnd`** 的概念。拥塞窗口是发送方维护的一个状态变量，它会根据网络的拥塞程度实时变化，当拥塞程度加重，`cwnd` 变小；当拥塞程度减小，`cwnd` 变大。      

在引入拥塞窗口概念之前，发送窗口大小和接收窗口大小基本是相等的关系（取决于接收窗口大小）。引入拥塞窗口后，发送窗口的大小就等于拥塞窗口和接收窗口的最小值。                 
TCP 的拥塞控制采用四种算法：       
- 慢开始       
- 拥塞避免      
- 快重传和快恢复      

**慢开始**        

慢开始的思路是，TCP 在刚建立连接完成后，如果立刻把大量数据注入网络，则很有可能引起网络阻塞。好的方法是先进行探测，一点一点提高发送数据包的数量，既有小到大逐渐增大拥塞窗口数值。`cwnd` 初始值为 1， 每经过一个传播轮次，`cwnd` 加倍。由于这种加倍是一个指数增长模型，我们不能一直执行慢启动，而是有一个 **慢启动轮限 `ssthresh`** 状态变量，使得：      
- 当 `cwnd < ssthresh` 时，使用慢启动算法        
- 当 `cwnd >=ssthresh` 时，使用拥塞避免算法            

**拥塞避免**        

拥塞避免算法的思路是让拥塞窗口 `cwnd` 缓慢增大，即经过一个传播轮次，`cwnd` 值加一。      

无论是慢开始还是拥塞避免阶段，只要出现网络拥塞（触发**超时重传**机制），慢开始轮限 `ssthresh` 和拥塞窗口大小 `cwnd` 的值就会发生变化：        
- `ssthresh = cwnd/2`          
- `cwnd = 1`      

由于 拥塞窗口大小重置为 1 ，则自动开始执行慢启动算法。      

**快重传和快恢复**        

快速重传和快速恢复算法一般同时使用。当发送方收到三个重复的冗余 ACK 确认的时候，即触发快速重传机制，根据 SACK 或 D-SACK 重新发送丢失的报文。TCP 认为触发快速重传的情况是不严重的，因为如果网络严重拥塞，必然触发超时重传机制，而不会有连续三个重复确认报文到达。于是快速重传发生后的快速恢复要做的事情有：      
- `ssthresh = cwnd / 2` 慢开始门限减半，预防网络拥塞。            
- `cwnd = ssthresh` TCP 认为快速重传发生时的网络条件并不是特别严重，不会将 `cwnd` 置一以开始慢启动算法，而是令 `cwnd = ssthresh` 从而开始拥塞避免阶段。          
- 进入拥塞避免阶段。      

也有的快恢复算法的实现将触发快速重传后的 `cwnd` 设置的更大一些，令 `cwnd = ssthresh+3`。该算法认为，同一时刻网络中数据包数量是恒定的。只有当老的数据包离开网络，新的数据包才可以被发送到网络。如果收到一个重复的 ACK，说明有一个老的数据包离开了网络，且ACK 对应的数据包已经在当前窗口中删除掉了，无法再次删除也自然无法加入新包到发送窗口。连续收到三个重复的 ACK，就代表有三个老的数据包离开网络，网络中减少了三个分组，从而可以有更多的三个数据包进入网络，将拥塞窗口设置为 `ssthresh + 3`：        
- `ssthresh = cwnd / 2` 慢启动门限还是减半，防止网络拥塞      
- `cwnd = ssthresh + 3` 可以有更多的三个数据包进入网络，仍然可以进入 拥塞避免。      
- 进入拥塞避免。      

在进入拥塞避免阶段后，如果继续收到重复的 ACK 包，拥塞窗口 `cwnd` 继续加一。原因同样是认为网络中减少了一个数据包，则可以进入一个新数据包。      
当收到新的数据包的 ACK 时，认为所有重复 ACK 对应的丢失数据都已经收到，快恢复过程结束。使 `cwnd = 快恢复最初更新的 ssthresh`，继续进入拥塞避免阶段。       


#### 最大报文长度 MSS 和最大传输单元 MTU       

此处提出这个概念是对上文 窗口大小等概念的补充，比如 `cwnd` 或者 `ssthresh` 加几减几，由于窗口大小是字节为单位的，所以这个加几减几的单位是 MSS (Maximum Segment Size) 。         
最大报文长度 MSS 是 TCP 数据包每次能够传输的最大**数据**分段，是第四层传输层的协议，受 MTU 限制。   
最大传输单元 MTU ( Maximum Transformer Unit) 是数据链路层上可以传输的数据包的最大大小，是硬件规定的。在 IP 协议中，MTU 长度是 1500 字节。             

一般的，MSS (最大 **数据** 分段长度)  = MTU - IP 头（20字节） - TCP 头（20字节） = 1460 字节。      



## 三次握手        

#### 三次握手过程     

<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/CSbasis/Network02.png"></div>         

0. 最初客户端和服务端无连接，服务端处于监听状态，客户端主动发起连接请求，开始三次握手建立连接。       
1. 客户端发送请求报文段，携带同步位 `SYN=1` 和 随机初始序号 `seq=x`，同时客户端进入 SYN-SENT 同步已发送状态。SYN 报文不携带数据，但是消耗一个序号。         
2. 服务端收到客户端请求报文，由同步位 `SYN=1` 得知客户端请求建立连接。如果同意建立连接，则向客户端发送一条确认报文，携带同步位 `SYN=1`，应答位 `ACK=1`，确认号 `ack=x+1` 和自己的随机初始号 `seq=y`，同时服务端进入 SYN-RCVD 同步已接收 状态。SYN 报文不携带数据，但是消耗一个序号。      
3. 客户端接收到服务端的确认报文，由应答位 `ACK=1` 得知服务端同意建立连接，有确认号 `ack=x+1` 确认这是对自己请求报文的回应。至此客户端确认服务端同意建立连接且可以收到自己的报文，客户端进入 `ESTABLISHED` 连接已建立状态。为了使服务端确认客户端可以收到服务端报文，需要返回给服务端一个确认应答报文，携带应答位 `ACK=1`，序列号 `seq=x+1` 和确认号 `ack=y+1`。该条报文可以携带数据，如果不携带，则不消耗额外的序列号，下一条数据数列号仍是 `seq=x+1`。         
4. 服务端收到第三条报文，由确认号得知确认是对自己发送的序号为 `y` 的应答，确认客户端能收到自己发送的报文，进入 `ESTABLISHED` 连接已建立状态。        

#### 为什么两次握手不可以         

防止已失效的的请求连接报文干扰。              

通过第三次客户端向服务端的握手，服务端可以确认客户端可以接受到自己的消息。考虑一种情况，客户端发送的第一次握手请求报文被阻塞在网络中，经过一段时间以后才到达服务端。此时该报文已经是一个失效请求了。       
- 如果没有第三次握手，服务端收到该请求就向客户端发送确认建立连接响应并进入 `ESTABLISHED` 连接已建立状态，等待客户端发送数据。由于请求已失效，客户端不会对服务端这一回复给出确认应答，也不会建立连接，这样服务端就处于无效等待阶段，造成资源浪费。        
- 如果有第三次握手，服务端一直等不到客户端的应答，自然不会建立连接进入 `ESTABLISHED` 阶段，避免了无效连接和资源浪费。     

#### 为什么不需要四次握手      

通过第二次握手客户端确认服务端可以接收到自己消息并给出回应；通过第三次握手服务端确认客户端可以接收到自己消息并给出回应。三次握手结束双方都确认了通信状况，可以建立通信，不再需要多余的握手。    


#### 二次握手时服务端为什么还要传回 SYN 标志位             

服务端返回 SYN 置一的报文可以证明服务端到客户端的通道没有问题。另一个理解是，SYN 是建立 TCP 连接时的握手信号，服务端在接到客户端发起的带有 SYN 标志位的建立连接请求时后，需要告知客户端本次返回报文是对**建立连接请求**的回应，是二次握手报文。           
此处比较疑惑，因为服务端返回的应答序号 `ack=x+1` 已经可以证明是对客户端发送的第一条消息的回应了，为什么还要 SYN 置 1 呢？没必要啊。协议就是这样规定的，暂不细究了。

## 四次挥手      

#### 四次挥手过程      

<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/CSbasis/Network03.png"></div>         

**一次挥手：**       

客户端发出连接释放报文，结束标志 `FIN=1` ，序号 `seq=u` 为顺序递增到客户端已发送数据的最后一个字节加一。发出该报文后客户端进入 `FIN-WAIT-1` 终止等待一阶段状态，等待服务端的确认报文。FIN 报文即使不携带数据也消耗一个序列号。        

**二次挥手：**         

服务端收到 `FIN=1` 的连接释放报文后立刻返回 `ACK=1` 确认消息，确认号 `ask=u+1` ，序列号 `seq=v` 为顺序递增到服务端已发送数据的最后一个字节加一。随后服务端进入 CLOSE-WAIT 关闭等待阶段 ，该阶段 TCP 处于半关闭状态，服务端不再接受客户端的数据；但服务端发往客户端的数据，客户端仍需要接收。 该报文不携带 `FIN=1` 标志位。       
客户端收到服务端的确认后立刻进入 FIN-WAIT-2 中止等待二阶段 状态，等待服务端的TCP 连接释放报文。     

**三次挥手：**        

服务端应用进程数据发送完毕后通知 TCP 进程可以释放连接，则服务端发送释放连接报文 `FIN=1` 到客户端，确认号不变还是 `ack=u+1`，序列号 `seq=w` 为顺序递增到已发送的最后一个字节加一（半关闭状态可能又发送了一些数据）。服务端发送连接关闭报文后立刻进入 LAST-ACK 最后确认状态，等待客户端的确认释放报文。     

**四次挥手：**      

客户端收到服务端的 `FIN=1` 连接释放报文后，立刻给出 `ACK=1` 确认报文，确认号 `ack=w+1`，序列号 `seq=u+1`， 这是由于客户端第一发送的 `FIN=1` 尽管不携带数据仍消耗一个序列号。        
服务端接收到客户端的确认后，立刻关闭连接，进入 `CLOSED` 关闭状态。客户端在发出确认报文后进入 TIME-WAIT 状态，仍需要等待 2* MLS（Message Longest Life，报文最长寿命）时间才能进入关闭状态。如意服务端结束 TCP 进入关闭状态的时间应该早于客户端。      

#### 为什么客户端最后要有等待 2 MLS 的 TIME WAIT 阶段      

为了保证客户端发送的报文能够到达服务端。在收到客户端最后的确认释放 ACK 报文之前服务端处于 LAST-ACK 最后确认阶段，如果服务端没有收到客户端的确认报文，会认为自己发送的 FIN=1 结束报文超时从而触发超时重传。这样客户端就可以在 2 MLS （超时+1MLS传输）时间内收到这个重传的报文，则随后客户端就并可以重新发送并重置 2MLS 的 TIME WAIT 等待时间。       
如果没有这个等待阶段，若客户端的应答报文丢失，服务端将无法保证正常关闭连接。         


## 保活计时器的作用    

当连接建立后，客户端主机发生故障或者不在活跃，服务端不再收到客户端发送来的数据，应当有措施使服务端不再白白等待下去以免浪费资源。保活计时器就是这个措施。服务端没受到一次客户端数据，就会重置保活计时器，时间通常设置为两个小时。若两个小时都没有收到客户端数据，服务端就会发送一个探测报文，以后每隔75秒发送一次。若连续发送十个探测报文后仍未收到客户端响应，服务端就认为客户端出了故障，即关闭这个链接。      

## TCP 的粘包和拆包         

拆包指的是，TCP 一次发送出的数据报文在实际传输过程中被拆成两份；粘包正相反，本来发送的多个独立的数据包在实际传输过程中被合并成一个。           
拆包和粘包有时候会同时发生，比如发送端连续发送了两条报文，其中一条被拆包，并且被拆的一部分和另一条报文数据发生粘包。也就是说，发生粘包拆包时，接收端读到的单条数据可能是不完整的或者多余的。由于，TCP 是面向字节的消息传递，TCP 协议将报文数据段当做无结构的字节流发送和接收，TCP 报文首部也没有记录报文长度的字段。如果发生了拆包粘包，接收端将很难处理。     
拆包和粘包仅发生在长连接连续不断发送数据的情况下，一次只发送一条数据的短连接不发生粘包。发生拆包粘包的原因有如下几种：          
- 当要发送的数据小于套接字缓冲区大小，网卡短时间将应用程序数据多次写入缓冲区并发送到网络，发送方将会发生粘包。            
- 当接收方不及时读取套接字缓存区的数据，接收方将发生粘包。           
- 当要发送的数据大于套接字缓冲区大小，网卡无法将应用程序的数据一次写入缓冲区，发送方将发生拆包。      
- 当要发送的数据大于最大报文长度 MSS = TCP报文长度 - TCP 首部长度，在传输前将发生拆包。       

**解决拆包粘包的方法：**            
1. 上层应用在将数据送入 TCP 发送前为每个数据包添加包含数据长度的包首部，这样接收方在收到TCP 报文后就知道了每个数据包的实际长度了。            
2. 发送端将每个数据包封装为固定长度，比如不够的地方补 0 填充，这样接收端每次从接收缓冲区读取固定长度的数据，自然就可以把每个数据包拆分开来。           
3. 在数据包之间设置边界，比如添加特殊符号，这样接收端通过这个边界进行判断，就可以将不同的数据拆分出来。        


## GET 和 POST 的区别         
1. 从本质来看：GET 是单次 HTTP 请求，POST 先发请求头再发请求体，实际上是两次请求。         
2. 从幂等性看，GET 请求是幂等的，对同一个资源无论 GET 多少次，获取到的数据总是相同的，GET 不会改变服务器上的资源；POST 不是幂等的，因为每次对资源的改变并不一定相同，POST 会改变服务器上的资源。       
3. 从请求参数看，GET 请求的数据回附在 URL 之后，即将请求数据放置在 HTTP 请求报文的请求头中，以 ? 分割 URL 和传输数据，以 & 连接参数；POST 请求的数据放置在后续发送的 HTTP 请求报文的请求体中。    
4. 从安全性看，POST 的安全性比 GET 更高，由于 GET 请求提交的数据明文出现在 URL 上，而 POST 请求参数则被包装到请求体中。       
5. 从请求大小看，GET 请求的长度受限于浏览器或服务器对 URL 长度的限制，允许发送的数据量较小，POST 请求则没有大小限制。            

## 在浏览器中输入 URL 地址到显示主页的过程        
1. DNS 解析。根据 url 内容搜索顺序是浏览器自身DNS 缓存、操作系统 DNS 缓存、本地 Host 文件，和本地 DNS 服务器。对于向本地 DNS 服务器发起查询，如果要查询的域名包含在本地配置区域资源中，则返回结果给用户机，完成域名解析；否则向上级 DNS 服务器发起迭代或递归查询。最终完成 域名向 IP 的解析。             
2. TCP 连接。浏览器获取到 IP 地址后，结合 URL  中的端口号向服务器请求建立连接，发起三次握手。       
3. 发送 HTTP 请求。TCP 连接建立起来后，浏览器向服务器发起 HTTP 请求，请求获取主页资源。     
4. 服务器处理请求并返回 HTTP 报文。服务器接收到浏览器的 HTTP 请求报文，并根据路径参数映射到特定的请求处理器进行处理，将处理结果和相应的视图返回给浏览器。      
5. 浏览器解析渲染页面。浏览器解析并渲染师徒，若遇到对 js, css 文件及图片等静态资源的引用，则重复上述步骤并向服务器请求这些资源；浏览器根据其请求到的资源数据渲染出完整页面展示给用户。      

#### DNS 的解析过程         
DNS 查询方式有递归查询和迭代查询两种。主机向本地域名服务器查询的过程是递归查询，本地域名服务器向根域名服务器查询的过程是迭代查询。        
递归查询，如果主机所查询的本地域名服务器不知道域名对应的 IP 地址，则本地域名服务器以 DNS 查询客户机的身份继续向根域名服务器发起查询，查询成功过返回给客户机 IP 结果，或者报错表示查询失败。     
迭代查询，根域名服务器收到本地域名服务器发出的查询请求报文，要么返回查询结果，要么返回给本地域名服务器下一步应该向哪个域名服务器发起查询，而不会代替本地域名服务器发起查询。       

#### 域名缓存      

域名缓存在本地表现为浏览器缓存和操作系统缓存，在域名服务器也有缓存并广泛使用。      
具体的，当访问某个域名并通过 DNS 解析获知了域名对应的 IP，浏览器会将该域名记录到本地缓存中，短时间内下次访问相同域名时则可以直接在浏览器域名缓存完成域名解析发起访问，而避免后续一系列查询过程。操作系统域名缓存同理，当浏览器缓存查询不到才开始查询操作系统缓存。          
操作系统缓存在主机启动时在本地域名服务器下载，本地域名服务器的缓存来自于，每台主机访问本地域名服务器时，如果查询不到域名信息，本地域名服务器会向上一级（根）域名服务器发起迭代访问，在迭代查询过程中获取到域名信息后就写入到自身缓存，这样下一次再有本地主机访问本地域名服务器，就可以返回缓存中存储的信息了。         
由于域名到地址的绑定并不经常改变，为了保持高速缓存的正确性，域名服务器应为每项内容设置计时器，并处理超过合理时间的项。这个计时器记录的有效值由上一级域名服务器随着查询的响应返回到本地域名服务器 。相应的，本地主机也需要定期检查域名服务器获取最新的域名缓存。       

缓存写在内存里，一方面保存时间较短，如果长时间不再访问，则缓存会被丢弃；另一方面，由于保存在内存而非磁盘，一旦内存下电，缓存也会丢失。         
事实上，在浏览器读取本地缓存查询不到域名信息时，并非直接向本地域名服务器发起访问，而是可以查询保存在本地磁盘中的 Hosts 文件。Hosts 将域名与 ip 对应关系保存在磁盘中，时效长，不会随下电而丢失。当本地 Hosts 也查询不到时，才会向域名服务器发起查询。           


## HTTP 1.0 和 1.1、2.0 的主要变化

**Http 1.0 到 Http 1.1**          
1. Http 1.1 默认使用长连接，可以在一个 TCP 中不间断的发送请求。           
2. Http 1.1 支持只发送 header 而不发送 body。原因是先用 header 判断能否成功，在发送数据，节约带宽。POST 方法就是这样。           
3. Http 1.1 请求头需要 host 字段以应对同一台虚拟主机支持多个域名的情况。         
4. Http 1.1 允许 pipe-line 管道传输，也即下一个请求的发送不必等待上一个请求的响应返回。        

**Http 1.1 到 Http 2.0**         
1. Http 2.0 支持多路复用，同一个连接可以并发处理多个请求。方法是把 HTTP 包拆为多个帧，并发有序的发送，根据序号在另一端进行重组，而不需要一个个HTTP 请求顺序到达。         
2. Http 2.0 支持服务端推送，就是服务端在 Http 请求达到后，除了返回响应数据，还可以推送额外的内容给给客户端，典型的就是多页浏览时提前推送下一页数据给浏览器以加快加载下一页的速度。        
3. Http 2.0 引入了头部压缩技术 HPACK，实现了报文头部压缩。         
4. Http 2.0 以**二进制流而非明文传输数据**，无结构的二进制帧流数据占用更小。             

#### 请求头中的 Host 字段

host 字段主要解决多个域名通过 A 解析或者 CNAME 绑定到同一台主机的问题。这种情况由于不知道目标服务在哪个域名下，下只通过 DNS 找到对端主机的 IP:Port 并建立连接无法保证对端可以收到请求。比如使用 Apache 部署 web 服务，在 /`root/www/` 路径下部署多个网站，只知道服务端 IP:Port 而不指定 host 就无法准确定位到具体的服务路径。通过在请求头指定 Host 字段，就可以确定具体向哪一个服务发起请求了。        
Http 1.1 中，Host 是必选字段，不指定则报错。       


#### 长连接和短连接        

Http 长连接和短连接本质上是 TCP 的长短连接。         
Http 0.9 时代没有长连接概念，都是短连接。Http 1.0 允许在请求头添加 Connection: Keep-Alive 使用长连接，但默认还是短连接。Http 1.1 开始默认使用长连接进行通信，在请求头添加 Connection: Closed 字段可以手动指定使用短连接通信。            
短连接也就是每次请求都会建立一次 TCP 连接，请求结束断开连接，也就是三次握手四次挥手过程，这个连接建立与断开的过程在一次请求中开销占比较大，实际的网络访问中往往客户端会对对同一个 Web 服务（如一个网页）发起多次请求，比如获取 html，获取 javascript, css, 图片等资源文件，访问同一个网页的过程中还可能有交互。此时如果对每一条请求都建立和销毁一次 TCP 连接，则显然整个访问过程中有相当大的开销浪费到了 TCP 建立和销毁上，并不划算。          
使用长连接的情况下，当一次网络服务发起，比如 浏览器打开一个网页，客户端和服务器之间用于传输 Http 数据的 TCP 连接不会关闭，客户端再次访问这个服务时，可以继续使用最初建立的一条 TCP 连接。Keep-Alive 也不是永久保持连接，而是有一个保持时间，保持时间内没有请求发送时才会关闭。     

Web 网站的 Http 服务一般使用短连接，因为长连接对于服务端来说会消耗更多资源。Web 网站在面临大量访问时，如果使用长连接，每个用户都占用一条连接，会对服务器形成极大的压力，而且建立了长连接的服务如果没有频繁发起请求而单单占据着连接，就形成了资源浪费。从而并发量大，但是每个用户无需频繁操作的情况下使用短连接更好。             
长连接多用于操作频繁的通信，且连接数量不宜太多，比如数据库连接。      

#### 二进制帧传输，从 pipeline 到多路复用        

Http 1.1 默认使用长连接，也即一个 TCP 连接中可以不断的发送 Http 请求。但是 Http 本身还是请求-相应模式的，也即上一个请求的响应收到后才可以发出下一条请求，这被称为非管道化连接，是一种相对低效的方式。Http 2.0 允许管道化连接：请求可以不必等待响应返回而并行发出（类似 TCP 协议的滑动窗口），但是响应必须串行返回。后一个响应必须在前一个响应之后。原因是，HTTP 的报文顺序依靠基于字节流的 TCP 保证，**Http本身没有序号标明顺序**，只能串行接收。              
管道化请求有两个缺陷：      
1. 会造成队头阻塞。前一个响应没有时返回，后面的相应会被阻塞。HTTP 的阻塞一方面来源于由于依赖 TCP 保证顺序，TCP队头阻塞则相应 HTTP  阻塞；另一方面，即使 TCP 传输没有发生阻塞，也可能因为某个 HTTP 请求需要较长的处理时间或者其他情况，导致后来的请求即使完成响应无法及时发送。        
2. 请求必须是幂等的，也就是不允许更改服务端资源。由于意外中断的时候，客户端需要重发未收到响应的请求非幂等请求将破坏资源。        

由于以上两者缺陷，管道化并不常见。Http 2.0 通过多路复用解决了非 TCP 阻塞导致的 Http 队头阻塞问题。多路复用Http 2.0 不使用管道化，而使用无结构的二进制数据流传输数据。Http 2.0 引入了帧、消息、数据流等概念，每个请求或响应称为一个消息，每个消息被拆分为若干个二进制帧进行传输，帧在流中传输。     
Http 2.0 允许一条 TCP 链接包含多个双向的流，流承载着消息拆分出的二进制帧的传输，不同流中的二进制帧各自独立传输。帧头包含了所在的数据流和序号，到达接收端后再组装为消息。     

多路复用是应用层的解决方案，无法解决 TCP 阻塞。     

#### Http 的头部压缩：HPACK                
在 Http 1.1 实现了消息体的压缩（通过 deflate, zlib 或 gzip 算法），但没有实现头部压缩。Http 2.0 通过 HPACK 实现了消息头压缩。HPACK 压缩包含两部分：`Indexs Table` 索引表和 `Static Huffman Encoding` 静态霍夫曼。其中 索引表用于压缩头部的索引，分为静态表和动态表；静态霍夫曼用于压缩头部内容。             

`Index Table` 包含 `Static Table` 静态表和 `Dynamic Table` 动态表，静态表包含61个预定义的 `key-value`，动态表包含自定义的 `key-value`。通过 `Index Table`索引表，只需使用一个单字节索引，就能表示一个常见的 key-value 对，比如 `:method GET = 2 = 00000010`。          
静态表只包含了常用的 61 个键值对，当传输内容不在静态表内，就需要使用动态表了。动态表是个先进先出队列，每次增加都从第 62 位置（队尾）开始，删除则从最高位（队头）开始。使用 `Index Table` 传输消息，接收端根据 `Index Table` 查表解析出原有消息，实现了头部的压缩。        

HPACK 对 String 类型内容提供静态 `Huffman Coding` 霍夫曼编码。霍夫曼码是基于霍夫曼树形成的编码方式。霍夫曼树是最小带权二叉树，权值越大的节点越靠近根节点，依照从根节点开始向左为 0，向右为 1 的方式进行编码。静态霍夫曼编码就是，实现统计出 http 头部个字符出现的频率，频率越大即权值越大，由此构造一棵霍夫曼树，得到每个字符的固定的霍夫曼码。从而将占8 bits 的 char 类型进行压缩，比如字符 `h` 以 char 类型存储占 8bits，但其 huffman 编码为 100111 ，只需 6bits 即可表示。        

#### HTTPS 与 HTTPS 的握手过程        

HTTPS 就是 HTTP over TSL (Transport Layer Security，传输层安全协议)，也即在传输层 TCP 协议和应用层 HTTP 协议之间添加了一层 TSL 用作数据加密，HTTP 数据经过这个加密层加密后再送入 TCP 传输，从而解决 HTTP 无加密明文传输数据的安全隐患。进一步的，不像 HTTP 在 TCP 连接建立之后即可明文通信，HTTPS 在 TCP 连接建立完成后还需要进行四次交互（不含 TCP 三次握手），每次交互可能发送多条消息给对方，用来交换和产生秘钥以建立连接。       
1. 第一次握手 Client -> Server            
  - ClientHello Message           
    客户端向服务端发送随机数 Client Key、TLS 版本列表、加密组件列表、压缩算法列表。             
    随机数 Client Key： 用于后续对称加密的计算因子。            
	TLS 版本列表：告知服务端当前客户端支持的 TLS 协议版本，服务端会从中选择一个，下次交互时告知客户端。            
	加密组件列表：告知服务端当前客户端支持的 加密组件，服务端会从中选择一个，下次交互时告知客户端。          
	压缩算法列表：告知服务端当前客户端支持的 加密算法，服务端会从中选择一个，下次交互时告知客户端。         
2. 第二次握手 Server -> Client         
  - ServerHello Mesage       
    服务端向客户端发送随机数 server key，TLS 版本、加密组件、压缩算法。          
	随机数 server key：用于后续对称加密的计算因子。           
	TLS 版本：服务端从客户端支持的 TLS 版本号列表中选择协议版本，一般优先选择安全性较高的版本。       
	加密组件：服务端从客户端支持的 加密组件列表中选择加密组件，一般优先选择安全性较高的版本。       
	压缩算法：服务端从客户端支持的 压缩算法列表中选择压缩算法。       
  - Server Certificate              
    服务端发送证书，客户端收到证书后队进行有效性验证，从而确认服务端身份。       
  - CertificateRequest Message （可选）        
    发送客户端证书验证请求（可选项）。对于服务端需要验证客户端身份的场景发送此请求，客户端收到此请求后将客户端证书发送给服务端。       
  - ServerHelloDone Message           
    告知客户端本次交互结束。             
3. 第三次握手 Client -> Server                
   本次交互客户端发送消息前会对服务端证书进行验证，校验完毕发送消息。          
  - Client Certificate           
    如果上一次交互收到服务端的 CertificateRequest Message，则将客户端证书发送给服务端。         
  - ClientKey Exchange Message         
    发送通过服务端证书携带的公钥加密的第三个随机数 Pre-master Secret 给服务端（只讨论 RSA 加密算法，如果是 DH 算法则根据证书中包含的两个整数和前两个随机数可计算出 Pre-master Secret），用于后续对称加密的计算因子。     
  - ChangeCipherSpec Message        
    加密方式改变通知，告知服务端以后的通信使用协商好的对称秘钥进行加密。        
  - Finished Message            
    客户端握手结束通知，使用对称秘钥加密。该报文包含前面所发送所有内容的哈希值（称摘要信息），供服务端校验。        
4. 第四次握手 Server -> Client           
  - ChangeCipherSpec Message             
    加密方式改变通知，告知客户端以后的通信使用对称密钥加密。         
  - Finished Message             
    服务端握手结束通知，使用对称秘钥加密。该报文包含前面所发送所有内容的哈希值（称摘要信息），供客户端校验。          

保留疑惑：对话中加密的是消息本身还是消息的哈希值？未找到明确答案。       

#### 对称加密与非对称加密，Https 的选择             

对称加密指的是加密解密使用同一个秘钥，对称加密效率比非对称加密要高，但是无法保证安全。只要第三方获取到了秘钥既可以破译加密的信息。          
非对称加密则是加密使用公钥，解密使用私钥，安全性较高。由于私钥为设备私有不参与网络传输，第三方即使获取到公钥，也无法破译加密的信息。但是非对称加密计算复杂、效率较低。          
HTTPS 的实现方案是正式会话通信使用效率较高的对称加密，生成对称加密密钥的随机数因子则使用非对称加密传输。        
1. 客户端向服务端发起 ClientHello Message请求建立 TLS 后，服务端将自己的证书（包含服务端公钥）和对称加密算法相关信息返回给客户端。此两步交互过程会明文交换客户端和服务端随机数。        
2. 客户端检查服务端证书是否由可信赖的 CA 机构颁发。如果不是则显示警告信息，并询问客户是否继续访问；如果是，客户端使用从证书中获取的 RSA 公钥加密一个随机数 Pre-master Secret 发送给服务端。同时，客户端此时已经拥有前两步交互获得的和本次生成的共三个随机数，通过第二次交互服务端确认的对称加密算法即可以生成用于后续对称加密的对话秘钥 Master Secret 。     
   Pre-master 发送后客户端会将此前发送的（也即服务端已收到的）消息的哈希值使用该 Master Secret 进行加密发送给服务端进行秘钥验证，就是第三次交互最后的 Finished Message 信息。   
3. 服务端使用自己的私钥解密客户端发送的 Pre-master Key，此时服务端也拥有域客户端相同的三个随机数，可以按照相同的加密算法生成用于对称加密的会话秘钥 Master Secret。     

其中第 2,3 两步就是非对称加密传输对称加密秘钥生成因子的过程。         

#### HTTP 和 HTTPS 的区别，HTTP 的优缺点           

1. 开销。HTTPS  需要申请收费的 CA 数字证书，HTTP 则不需要。            
2. 资源消耗。HTTPS 是 HTTP over TLS ，加密解密过程消耗 CPU 资源；HTTP 是明文传输不需要加密。          
3. 默认端口。HTTP 默认端口是 80，HTTPS 默认端口 443 。       
4. 安全性。HTTPS 是加密传输，且对称加密的公钥生成因子通过非对称加密传输，安全性较高；HTTP 明文传输数据，安全性较差。     

**优点：**         
1. HTTPS 由于需要证书验证身份，可保证数据来自于正确的服务端或客户端。         
2. HTTPS 传输消息比 HTTP 更加安全，具体体现在其对消息进行加密传输从而防止了消息的窃取和改变，保证消息的完整性。    

**缺点：**       
1. HTTPS 的 TLS 握手阶段更加费时间费资源。            
2. TLS 证书收费，功能越强大的证书收费越高。      
3. TLS 证书需要绑定 IP，且无法在一个 IP 上面绑定多个域名。         
4. HTTPS 只能防止中间人攻击，而不是绝对保证安全。        


#### 数字签名，数字证书和 CA        

**数字签名**          

数字签名是对公钥密码的逆应用：用私钥加密信息，用公钥进行解密。用私钥加密的信息称为签名，只有拥有私钥的用户可以生成签名；用公钥解密签名这一步称为验证签名。        
由于公钥是公开的，所有用户都可以验证签名。而由于私钥是唯一的，一旦签名验证成功，根据公私钥数学上的对应关系，就可以确定该消息一定是拥有对应私钥的唯一用户发送的。        

**生成签名和验证签名**           

一般来说，不直接对消息进行签名，而是对消息的哈希值进行签名。      
1. 对消息进行哈希计算，得到消息哈希值。       
2. 利用死要对哈希值进行加密，生成签名。         
3. 将签名附加在消息后面一并发送。          

验证签名就是生成签名的反过程：       
1. 收到消息后提取其中的签名。         
2. 用公钥对签名进行解密，得到哈希值 1 。     
3. 对消息中的正文进行哈希计算，得到哈希值 2 。        
4. 比较两个哈希值，如果相同则验证成功。            

**数字证书与CA**             

数字证书就是对公钥进行数字签名。       
考虑服务端如果直接发送公钥，也有被劫持更换的风险，由权威的证书认证机构 Certificate Authority (CA) 签名并颁发的证书可以用来证明公钥的合法性，由于 CA 是公认可信的，从而认为 CA 被证书签名的公钥是可信的。证书通常包含公钥、公钥的数字签名和公钥拥有者的信息。           

**生成证书和验证证书**               

生成过程：        
1. 服务器将自己的公钥 A 提供给 CA            
2. CA 用自己的私钥 B 加密公钥 A 的哈希值，生成签名          
3. CA 将服务器公钥 A 和公钥的签名，附加一些服务器信息整合在一起生成证书，返回给服务器         

验证过程：            
1. 客户端接受到证书。          
2. 客户端使用（通常已经内置于浏览器的）CA 的公钥 B 解密证书中的数字签名，得到哈希值      
3. 客户端对证书中的公钥进行哈希计算得到哈希值        
4. 比较两个哈希值，如果相同，则证书合法，验证成功          

