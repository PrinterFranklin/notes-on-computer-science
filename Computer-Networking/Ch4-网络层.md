# 网络层参考笔记

本文是我在学习计算机网络期间做的笔记。

内容主要来自于谢希仁《计算机网络·第七版》，同时参考了*Computer Networking: A Top-Down Approach* 以及  Dr.Tanenbaum 的 *Computer Networks*。

欢迎大家转载交流，但请勿将此文用作商业用途。

[TOC]

## 一、网络层概述

### 1. 网络层提供的服务

关于网络层向运输层提供的服务，主要有两种思路：**虚电路服务**和**数据报服务**。

![](http://oxglnwe1z.bkt.clouddn.com/18-1-7/70450661.jpg)

| 对比项          | 虚电路服务                  | 数据报服务               |
| ------------ | ---------------------- | ------------------- |
| 思路           | 可靠通信由网络来保证             | 可靠通信由用户主机来保证        |
| 连接建立         | 必须有                    | 不需要                 |
| 终点地址         | 仅在连接建立时使用，每个分组使用短的虚电路号 | 每个分组都包含终点的完整地址      |
| 分组转发         | 属于同一条虚电路的分组均按照同一路由进行转发 | 每个分组独立选择路由进行转发      |
| 结点出现故障时      | 所有经过故障结点的虚电路均不能工作      | 故障结点可能丢失分组，但路由会发生变化 |
| 分组顺序         | 总是按发送顺序到达终点            | 到达终点的顺序可能与发送顺序不一致   |
| 端到端差错处理与流量控制 | 可以由网络负责，也可以由用户主机负责     | 由用户主机负责             |

互联网的设计思路是网络层向上只提供简单灵活的、**无连接的**、**尽最大努力交付**的**数据报服务**（IP 数据报又称为分组）。原因是：

* 现代计算机有很强的差错处理能力，主机中的运输层完全可以提供可靠交付。
* 这使得网络中的路由器比较简单，网络造价大大降低，运行方式灵活，能适应多种应用。



## 二、网际协议 IP

### 1. 概述

网际协议 IP 是 TCP/IP 体系中两个最主要的协议之一，作用是实现**网际互连**。

网际协议又称 Khan-Cerf 协议，由 Robert Khan 和 Vint Cerf 共同研发，这两位学着因此共同获得 2005 年图灵奖。

网际协议共有 6 个版本，但只有 4 和 6 得到使用。这里主要介绍 IPv4。

与 IP 协议配套使用的 4 个协议：

* **地址解析协议 ARP**（Address Resolution Protocol）
* 逆向地址解析协议 RARP（Reverse Address Resolution Protocol），不常用
* **网际控制报文协议 ICMP**（Internet Control Message Protocol）
* **网际组管理协议 IGMP**（Internet Group Management Protocol）

![](http://oxglnwe1z.bkt.clouddn.com/18-1-7/62509583.jpg)

其中，ICMP 和 IGMP 会调用 IP，IP 会调用 ARP 和 RARP。

### 2. 虚拟互连网络

网络互连的四种中间设备：

* 物理层：转发器（repeater），即集线器/交换机。
* 数据链路层：网桥（bridge）
* 网络层：**路由器（router）**
* 网络层以上：网关（gateway）

其中，转发器和网桥仅仅是把一个网络扩大了，而路由器则用于进行网络互连。

虽然互连起来的各种物理网络的异构性客观存在，但它们都使用相同的网际协议 IP，因此可以把这些网络的集合看成单个**虚拟互连网络**，从而对上层屏蔽了底层物理网络的细节。

如果在这种覆盖全球的 IP 网上层使用 TCP 协议，就成了现在的互联网。、

### 3. IP 地址

**IP 地址**就是为互联网上的**每一台主机（或路由器）的每一个接口**分配一个全世界**唯一**的 32 位标识符。

当一台主机同时连接到多个网络时，就具有多个 IP 地址，被称为**多归属主机**。一个路由器至少有两个不同的 IP 地址。

若两台路由器直接相连，连线两端接口处可分配也可不分配 IP 地址。这种仅由一段连线构成的特殊网络被称为**无编号网络/无名网络**。

![](http://oxglnwe1z.bkt.clouddn.com/18-1-7/64411939.jpg)

IP 地址由 ICANN 负责分配。

为提高 IP 地址的可读性，通常采用**点分十进制记法**：每 8 位插入一个空格，用其等效的十进制数字表示，并在这些数字之间加上一个点。

![](http://oxglnwe1z.bkt.clouddn.com/18-1-7/37645903.jpg)

IP 地址有三种编址方法，且是按历史阶段演进的：

* 分类的 IP 地址
* 子网的划分
* 构成超网

#### （1）分类的 IP 地址

每一类地址都由两个固定长度的字段组成：
$$
\rm IP\,地址::=\{<网络号>,<主机号>\}
$$

* **网络号**：标志主机（或路由器）所连接的网络。在整个互联网范围内唯一。
* **主机号**：标志该主机（或路由器）。在网络号指定的局域网内唯一。

按照互联网观点，一个**网络**就是**具有相同网络号的主机的集合**。

之所以要采用分等级的地址结构，是因为：

1. 单位申请 IP 地址时，可以按照其规模划分给它一个网络号，具体到各台主机号则由单位自行分配。
2. 路由器仅根据目的主机所连接的网络号来转发分组，可以大幅节约路由表中的项目数。

这些 IP 地址又可以分为以下 5 类：

![](http://oxglnwe1z.bkt.clouddn.com/18-1-7/77941249.jpg)

* A 类、B 类、C 类地址都是**单播地址**（一对一通信），分别为 1 个、2 个、3 个字节长，最前面还有 1~3 位的类别位。
* D 类地址用于**多播**（一对多通信）。
* E 类地址保留以后使用。

##### ① A 类地址

A 类地址共 $2^{31}$ 个，占 IP 地址空间的 50%。

A 类地址网络号占 1 个字节，第一位是 0，还有 7 位可供使用。可指派的网络数是 $2^7-2=126$ 个，原因是：

* 网络号为全 0 的地址是保留地址，意思是“本网络”。
* 网络号为 127 的地址保留为**环回地址**，用作本主机进程通信测试。

A 类地址主机号占 3 个字节，每个 A 类网络中最大主机数是 $2^{24}-2$，因为：

* 全 0 主机号地址表示整个局域网地址，不表示具体主机。
* 全 1 主机号地址表示该网络上所有主机。

##### ② B 类地址

B 类地址共 $2^{30}$ 个，占 IP 地址空间的 25%。

B 类地址网络号字段占 2 个字节，前两位是 10，还有 14 位可供使用。可指派的网络数是 $2^{14}-1=16383$ 个，原因是 128.0.0.0 不指派，最小网络地址是 128.1.0.0。

同理，每个 B 类网络中最大主机数是 $2^{16}-2=65534$。

##### ③ C 类地址

C 类地址共 $2^{29}$ 个，占 IP 地址空间的 12.5%。

C 类地址网络号字段占 3 个字节，前三位是 110，还有 21 位可供使用。可指派的网络数是 $2^{21}-1$ 个，原因是 192.0.0.0 不指派，最小网络地址是 192.0.1.0。

同理，每个 C 类网络中最大主机数是 $2^{8}-2=254$。

![](http://oxglnwe1z.bkt.clouddn.com/18-1-7/50828270.jpg)

#### （2）划分子网

##### ① 从两级 IP 地址到三级 IP 地址

两级 IP 地址的弊端：

* IP 地址空间利用率有时很低，造成浪费。
* 给每个物理网络分配一个网络号，导致路由表过于庞大，网络性能变坏。
* 两级 IP 地址不够灵活。

为解决以上问题，1985 年起，IP 地址中又增加了一个“子网号字段”，使两级 IP 地址变成三级 IP 地址。这种做法叫做**划分子网**（subnetting）：

* 一个拥有许多物理网络的单位，可将所属物理网络划分为若干个子网，但**对外仍表现为一个网络**。

* 划分方法是从网络的**主机号**借用若干位作为**子网号**：
  $$
  IP \,地址::=\{<网络号>,<子网号>,<主机号>\}
  $$

* 从其它网络发送给主机的 IP 数据报，仍然根据网络号找到连接在本单位网络上的路由器，但路由器收到 IP 数据报后，要根据目的网络号和子网号找到目的子网，将数据报交付主机。

##### ② 子网掩码

从 IP 数据报首部无法看出网络是否进行了子网划分，这是因为 IP 地址本身没有包含任何有关子网划分的信息。

为了使路由器能方便地从数据报目的 IP 地址中取出要找的子网的网络地址，就需要使用**子网掩码**。子网掩码中的 1 对应 IP 地址中的**网络号和子网号**，0 对应**主机号**。根据 RFC 推荐，子网掩码应使用连续的 1。

![](http://oxglnwe1z.bkt.clouddn.com/18-1-20/59164543.jpg)

使用子网掩码的好处就是，不管网络有没有划分子网，只要把子网掩码和目的 IP 地址逐位做“与”运算，就能立即得到网络地址。

现行互联网标准规定，所有网络都必须使用子网掩码，路由器的路由表中也必须有子网掩码一栏。如果一个网络不划分子网，那该网络的子网掩码就使用默认子网掩码。这样做的好处是**不用查找该地址类别位就能知道是哪一类 IP 地址**。

![](http://oxglnwe1z.bkt.clouddn.com/18-1-20/96758417.jpg)

注意，上表中的子网号和主机号都是除去了全 0 和全 1 这两种情况的，且子网号位数中没有 0/1/15/16 四种情况，因为这没有意义。但随着 CIDR 的广泛使用，现在全 1 和全 0 的子网号也可以使用了，但一定要弄清楚路由选择软件是否支持这种用法。

子网号位数越多，每个子网上可连接的主机就越多。划分子网增加了灵活性，但减少了网络上能连接的主机总数。





#### （3）构造超网



#### （4）IP 地址与硬件地址比较

从层次上看，**物理地址是数据链路层和物理层使用的地址，IP 地址是网络层和以上各层使用的地址**，是一种逻辑地址。

![](http://oxglnwe1z.bkt.clouddn.com/18-1-7/87659323.jpg)

当 IP 数据报放入 MAC 帧后，整个数据报成为 MAC 帧的数据，因此在链路层看不到 IP 地址。只有当剥去 MAC 帧首尾，将数据上交给网络层后，网络层才能看到 IP 地址。

![](http://oxglnwe1z.bkt.clouddn.com/18-1-7/81694271.jpg)

如上图，**每经过一个路由器，MAC 帧的格式（首部尾部）都要变化，但 IP 数据报始终不变**。这是因为路由器收到 MAC 帧后，会丢弃原先的首部和尾部，提取出 IP 数据报，根据目的站 IP 地址进行路由选择，在转发时，再根据选择结果重新添加上 MAC 帧的首部和尾部。

这就是网络层抽象的精髓：**屏蔽链路层网络异构带来的复杂性，使用统一的、抽象的 IP 地址来研究主机和主机或路由器之间的通信**。

### 4. IP 数据报格式

一个 IP 数据报由首部和数据两部分构成：

![](http://oxglnwe1z.bkt.clouddn.com/18-1-8/84974571.jpg)

#### （1）首部字段

首部的前一部分是**固定字段**，共 20 字节，后一部分是长度可变的**可选字段**。

##### ① 固定字段

* **版本**
  占 4 位。指 IP 协议的版本（通信双方 IP 协议版本要一致），IPv4 版本号就是 4。
* **首部长度**
  占 4 位，最大值为 15，单位是 32 位字（4 个字节）。由于首部固定长度是 20 字节，因此首部长度值最小是 5（0101）；最大是 15（1111），即 60 字节。如果首部长度不是 4 字节整数倍，则必须利用最后的填充字段来补充。
* 区分服务
  占 8 位。用来获得更好的服务，但基本不用。
* **总长度**
  占 16 位，单位为字节。指首部和数据之和的长度，数据报最大长度是 $2^{16}-1=65535$ 字节，但这种长度基本不可能遇到。
  由于链路层数据帧规定了最大传送单元 MTU（对以太网而言是 1500 字节），因此所传送数据报长度超过 MTU 值时，就需要进行分片处理。
  进行分片时，首部中总长度等于分片后**每一个分片**首部长度与该分片数据长度的总和。
* **标识（identification）**
  占 16 位。每产生一个数据报就加 1，分片时标识字段的值被复制到每个分片的该字段中，以便于它们最后能被重装成原先的数据报。
* **标志（flag）**
  占 3 位，但目前只有 2 位有意义：
  * 最低位 MF（More Fragment）等于 1 表示后面还有分片数据报，等于 0 表示这是若干分片中的最后一个。
  * 中间位 DF（Don't Fragment）等于 0 时才允许分片。
* **片偏移**
  占 13 位，以 8 个字节为单位。较长的分组分片后，用于指出某片在原分组中的相对位置。
  ![](http://oxglnwe1z.bkt.clouddn.com/18-1-8/15583632.jpg)
* **生存时间（Time To Live, TTL）**
  占 8 位，单位是跳数，最大 255。表示数据报在网络中的寿命，防止其无限制地兜圈子。若设置 TTL=1，则数据报只能在本局域网传输，因为一传送到路由器就会被丢弃。
* **协议**
  占 8 位。用于指出所携带数据使用的协议，以便接受主机知道应将数据部分交给哪个协议处理。
* **首部检验和**
  占 16 位。只检验首部，不检验数据。为减少计算量，IP 协议不采用 CRC 检验码，采用以下算法：
  ![](http://oxglnwe1z.bkt.clouddn.com/18-1-8/51317749.jpg)
* 源地址&目的地址
  各占 32 位。

##### ② 可变字段

长度 1 到 40 个字节不等，用于支持排错、测量、安全等功能。如果长度不是 4 字节的整数倍，需要用全 0 的填充字段补齐。

可变字段很少被使用，IPv6 则把首部长度完全固定了。

### 5. IP 分组转发流程

![](http://oxglnwe1z.bkt.clouddn.com/18-1-19/55770371.jpg)

在路由表中，每条路由主要包括两个信息：
$$
(目的网络地址，下一跳地址)
$$
应注意到，路由表中的地址并不是具体到主机的 IP 地址，而是**目的主机所在的整个网络的地址**，如 30.0.0.0。

* 如果目的主机所在网络和路由器所在网络并非同一个网络（比如 $R_1$ 之于 $R_3$ 所在网络），则根据路由表将 IP 数据报转发到下一跳地址。
* 如果目的主机和路由器在同一个网络，则可通过 ARP 协议找到主机相应的硬件地址，直接交付目的地。

有一种特例是，对特定的目的主机指明一个路由，叫**特定主机路由**，以更方便地控制和测试网络。





## 三、地址解析协议 ARP

### 1. 概述

ARP 协议要解决的问题是：**在同一个局域网中，根据网络层使用的 IP 地址，解析出链路层使用的物理地址**。

ARP 协议介于网络层和链路层之间（常被划归为网络层），使用它的不是主机用户，而是 IP 协议，用户对于地址解析过程一无所知。

### 2. 具体实现

每台主机上都设有一个 **ARP 高速缓存**（ARP cache），里面有本局域网上各主机和路由器的 **IP 地址到物理地址的映射表**。

当主机 A 要向本局域网上某台主机 B 发送 IP 数据报时，先在 ARP cache 中查看有无 B 的 IP 地址：

* 若有，就在缓存中查出对应的物理地址，把该地址写入 MAC 帧，往局域网发出该 MAC 帧。
* 若没有，就自动运行 ARP 协议，按以下步骤找出主机 B 的物理地址：
  1. 在局域网上**广播发送一个 ARP 请求分组**，主要内容包含自己的 IP 地址、物理地址以及要查询的主机的 IP 地址。
  2. 若主机 B 的 IP 地址与请求分组中要查询的一致，就收下分组并向 A **发送 ARP 响应分组**（单播），其中写入了自己的物理地址。
  3. 主机 A 收到 B 的响应分组后，就向缓存中写入主机 B 的 IP 地址到物理地址的映射（其实之前 B 收到  A 的请求时也顺带写入了 A 的映射）。

注意：ARP 为每一个映射都设置了生存时间 TTL，凡是超过生存时间的映射都会被自动删除。

逆向地址解析协议 RARP 用于已知自己物理地址来查找 IP 地址，但此功能已经被 DHCP 协议完全包含。

## 四、网际控制报文协议 ICMP

**ICMP（Internet Control Message Protocol）**是互联网的标准协议，允许主机或路由器报告差错情况和提供有关异常情况的报告。

ICMP 不是高层协议（但看起来是，因为 ICMP 数据装在 IP 数据报的数据部分中），而是**网络层协议**。

![](http://oxglnwe1z.bkt.clouddn.com/18-1-20/7922140.jpg)

* ICMP 报文前 4 个字节是统一格式，共有 3 个字段：类型、代码、检验和。
  * 类型：指明 ICMP 报文类型。
  * 代码：进一步区分某种类型中的几种不同情况。
  * 检验和：用于检验整个 ICMP 报文（IP 数据报首部不检验数据内容）。
* 后 4 个字节内容与 ICMP 类型有关。
* 最后面是数据字段，长度取决于 ICMP 类型。

### 1. ICMP 报文类型

#### （1）ICMP 差错报告报文

差错报告报文又有 4 种：

* **终点不可达**：路由器或主机不能交付报文时就向源点发送
* **时间超过**：**路由器收到 TTL=0 的报文**时，除了要丢弃该数据报，还要向源点发送超时报文。当**终点在预先规定时间内不能收到一个数据报的全部数据**时，就丢弃已收到部分，并向源点发送超时报文。
* **参数问题**：路由器或目的主机收到的数据报首部中有字段的值不正确时，就丢弃该数据报，并向源点发送参数问题报文。
* **改变路由**（重定向）：路由器向主机发送改变路由报文，让主机下次将数据报发送给另外的路由（从而优化主机的路由）。
  主机要发送数据时，首先要查本地的路由表，找到应该从哪个接口发送数据报。但主机刚开始工作时，路由表往往不全，因此会设置一个**默认路由器**（它知道每个目的网络的最佳路由），不知道数据报要发送到哪就先把数据报送到那里。如果默认路由器发现发往某个目的地址的数据报的最佳路由是另一个路由器，就用改变路由报文把情况告诉主机，主机会在路由表中增加一个项目。

![](http://oxglnwe1z.bkt.clouddn.com/18-1-20/90841506.jpg)

所有 ICMP 差错报告报文中的数据字段都具有相同格式：把收到的需要进行差错报告的 **IP 数据报的首部和数据字段的前 8 个字节**提取出来，作为 ICMP 报文的数据字段。

之所以需要前 8 个字节，是因为里面包含了运输层端口号（TCP&UDP）和报文发送序号（TCP），这些信息对源点通知高层协议是有用的。

不应发送 ICMP 差错报告报文的情况：

* 对 ICMP 差错报告报文本身，不发送 ICMP 差错报告报文。
* 对第一个分片的数据报片之后的数据报片，不发送 ICMP 差错报告报文。
* 对具有多播地址的数据报，不发送 ICMP 差错报告报文。
* 对具有特殊地址的数据报（如 127.0.0.0），不发送 ICMP 差错报告报文。

#### （2）ICMP 询问报文

* 回送请求和回答
  由一个主机或路由器向一个特定的目的主机发送**回送请求报文**，主机收到报文后回复一个**回送回答报文**。
  这样可以**测试目的站是否可达以及了解其有关状态**。
* 时间戳请求和回答
  请某台主机或路由器回答当前日期时间，用于**时钟同步和时间测量**。

### 2. ICMP 应用举例

#### （1）分组网间嗅探 PING

PING（Packet InterNet Groper）用来**测试两台主机之间的连通性**。PING 是应用层直接使用网络层的一个例子，它没有经过运输层的 TCP 或 UDP。

PING 使用了 ICMP 回送请求与回送回答报文。

```
ping hostname（主机名或IP）
```

![](http://oxglnwe1z.bkt.clouddn.com/18-1-20/77050099.jpg)

#### （2）Traceroute

Traceroute 用于**查询到达目的主机所经过的路由器的 IP 地址，以及到达其中每一个路由器的往返时间**。

```
tracert hostname	// windows
traceroute hostname	// linux
```

Traceroute 从源主机向目的主机发送一连串 **IP 数据报**，其中封装了**无法交付的 UDP 用户数据报**。

* 第一个数据报 $P_1$ 的 TTL 设置为 1，当 $P_1$ 被第一个路由器 $R_1$ 收下时，生存时间为 0，被丢弃，$R_1$ 要向源主机发送一个 **ICMP 时间超过差错报告报文**。
* 类似地，源主机再发送第二个数据报 $P_2$，TTL 设置为 2 ……
* 直到刚好有一个数据报到达目的主机，此时 TTL 为 1。主机不转发数据报，也不把 TTL 值减 1。但数据报中封装的是无法交付的 UDP 数据报，因此目的主机要向源主机发送 **ICMP 终点不可达差错报告报文**。

就这样，这些路由器和目的主机发来的 ICMP 报文给出了源主机想知道的路由信息。

![](http://oxglnwe1z.bkt.clouddn.com/18-1-20/25870027.jpg)

上图中每行有三个时间，是因为对应于每个 TTL 值，源主机要发送三次同样的 IP 数据报。



## 五、路由选择协议

### 1. 理想的路由算法

* 算法正确且完整。
  沿着路由表所指引的路由，分组一定能最终到达目的网络和目的主机。
* 算法在计算上应简单。
* 算法应能适应通信量和网络拓扑的变化（仅适用于动态路由）。
  在网络拥塞/出现故障时，算法能自适应地改变路由，即算法具有健壮性。
* 算法具有稳定性。
  在网络状况相对稳定的情况下，算法应收敛于一个确定的可接受的解。
* 算法应是公平的。
* 算法应是最佳的。
  “最佳”指的是相对于某一种特定要求下得出的较为合理的选择。

### 2. 分层次的路由选择协议



分布式路由选择协议的三个要点：

* 和哪些路由器交换信息？
* 交换什么信息？
* 在什么时候交换信息？



### 3. 内部网关协议 RIP

##### ① 概述

**路由信息协议 RIP**（Routing Information Protocol）是一种**分布式的基于距离向量的**路由选择协议，在内部网关协议中最先得到广泛应用，最大优点是**简单、开销小**。

RIP 协议要求每个路由器都维护从它自己到其他每一个目的网络的距离纪录（因为有一组距离，所以又称距离向量）。“距离”其实就是**跳数**，从一路由器到直接连接的网络的距离定义为 1，到非直接连接的网络的距离定义为所经过的路由器数加 1。

RIP 协议认为距离短（即经过的路由器数目少）的路由是最优的。RIP 允许一条路径最多包含 15 个路由器，因此距离等于 16 就被认为**不可达**，只适合小型网络。

RIP 具有以下几个重要特点：

* 仅和**相邻的路由器**交换信息。
* 交换的信息是自己**现在的路由表**，即“我**到本自治系统中所有网络的（最短）距离**，以及**到每个网络应经过的下一跳路由器**”。
* 按**固定时间间隔**（如 30s）交换路由信息。

路由器刚开始工作时，路由表是空的。然后路由器就得出到直接相连的几个网络的距离（就是 1），接着，每个路由器和数目非常有限的相邻路由器交换并更新路由信息。经过若干次更新后，系统中所有路由器最终都会知道到达系统中任何一个网络的最短距离和下一跳路由器的地址。

##### ② RIP 协议报文格式

RIP 报文通过运输层的 UDP 进行传送（使用 UDP 端口 520）。

![](http://oxglnwe1z.bkt.clouddn.com/18-1-20/12461644.jpg)

RIP 报文由**首部**和**路由部分**组成。

首部中的命令字段指出报文的意义，例如，1 表示请求路由报文，2 表示响应报文或路由更新报文。首部后面的 0 是为了 4 字节字的对齐。

路由部分由若干路由信息（最多 25 个）组成，每个路由信息需要 20 个字节。

* 地址族标识符（或称地址类别）字段用于表示使用的地址协议，如采用 IP 地址就写 2。
* 路由标记填入自治系统号 ASN。这是考虑到可能收到本自制系统外的路由选择信息。

##### ② 距离向量算法

RIP 协议中路由表更新的原则是找出到每个目的网络的最短距离，这种更新算法又称“**距离向量算法**”。距离向量算法的基础是 Bellman-Ford 算法：

> 设 X 是结点 A 到 B 的最短路径上的一个结点。若把路径 A→B 拆成两段 A→X 和 X→B，则每一段路径 A→X 和 X→B 也都分别是结点 A 到 X 和结点 X 到 B的最短路径。

对每一个相邻路由器发来的 RIP 报文，执行以下步骤：

1. 对地址为 X 的相邻路由器发来的 RIP 报文，从中提取出路由表所需项目（目的网络 N、距离 d、下一跳路由器 X）。把“下一跳”字段中网络地址改为 X，“距离”字段值加 1。
2. 若原路由表中没有目的网络 N，就直接把收到的项目添加到路由表中。
   若原路由表中有目的网络 N，还要看这一项的下一跳路由器地址是不是 X，若是 X，就直接用收到的项目替换原项目；若不是 X，且收到项目中的距离 d 小于路由表中距离，也用收到的项目替换原项目。
   否则什么也不做。
3. 若 3 分钟还没有收到相邻路由器的更新路由表，则把该相邻路由器记为不可达，即把距离设置为 16。
4. 返回

不过，RIP 协议有一个致命弱点：**好消息传播得快，坏消息传播得慢**。一旦网络出现故障，有可能会经过很久才发现该网络不可达（见书 p158）。

### 4. 内部网关协议 OSPF



### 5. 外部网关协议 BGP





### 6. 路由器的构成

路由器是一种**具有多个输入端口和多个输出端口的专用计算机**，其任务是**转发分组**，即把路由器某个输入端口收到的分组，按照分组要去的目的地，从某个合适的输出端口转发给下一跳路由器，下一跳路由器也按照这种方法处理分组，直到分组到达终点。

下图是一种典型的路由器结构。

![](http://oxglnwe1z.bkt.clouddn.com/18-1-20/93964790.jpg)

如图可见，路由器结构分为两部分：**路由选择**部分和**分组转发**部分。

#### （1）路由选择处理机

任务是**根据所选定的路由选择协议构造出路由表**，同时定期和相邻路由器交换路由信息而不断**更新维护路由表**。

如何根据路由选择协议构造和更新，上已述及。

#### （2）分组交换部分

分组交换，就是转发，和路由选择的区别就在于，路由选择

本身就是一种路由器内部的网络，作用就是根据转发表对分组进行处理，将某个输入端口进入的分组从一个合适的输出端口转发出去。



## 六、IPv6



## 七、IP 多播



## 八、虚拟专用网 VPN 和网络地址转换 NAT



## 九、多协议标记交换 MPLS

