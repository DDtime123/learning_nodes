# Wireshark 网络实验

在计算机网络中，层和数据包这样的抽象概念是怎样在现实中的网络中转换的呢？下面来通过一个实验查看这些层，数据包在真实网络中的形态吧。

实验大纲：捕获实时数据包 -> 分析数据包中的内容。

网络结构通过一个 7 层的 OSI 协议栈来体现，而在我们的实验中会用到 5 层 Internet 协议栈。

实验所需工具：Windows操作系统的电脑一台，Wireshark 软件。

### 1. 使用 Wireshark 对网络传输数据包进行分析

Wireshark 是一种网络协议分析器，可以捕获和分析通过网络接口卡（NIC）传输/接收的数据包。

#### 1.1 捕获数据包

在开启 Wireshark 后，请根据自己的网络属性选择网络连接，我这里因为连接Wifi 上网，所以选择 WLAN ：

这里可以看到 Wireshark 捕获了许多种不同协议的数据包，包括 TCP，HTTP等：

![1633266017125](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633266017125.png)

现在先来看看应用层 HTTP 协议的数据包格式，我们在上边的 “显示应用过滤器 ... ” 里输入 http ，过滤出 HTTP 协议的连接。

现在准备捕获我们通过 NIC 的数据包：

![1633266188020](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633266188020.png)

然后我们打开浏览器，输入 www.baidu.com 打开百度网页，再在 Wireshark 上找到一个新的 GET 请求

![1633266320609](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633266320609.png)



#### 1.2 分析数据包

现在开始分析捕获的数据包：

![1633266398298](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633266398298.png)

可以看到捕获的数据包具有 Internet 协议栈的 5 层标头

* 第 1 层：物理层
* 第 2 层：数据链路层，在这个数据包中，
* 第 3 层：网络层，在这个数据包中，IP 被用作网络层协议
* 第 4 层：传输层，在这个数据包中，TCP 作为传输层协议
* 第 5 层：应用层，在这个数据包中，HTTP 作为应用层协议

###### 1.2.1 应用层数据包分析

首先展开第 5 层 “Hypertext Transfer Protocal”：

![1633267501970](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633267501970.png)

在这里，可以看到 Host 为 xueshu.baidu.com。HTTP 标头的 User-Agent 字段显示浏览器的详细信息。这里我启动的是 Microsoft Edge。

###### 1.2.2 nslookup 工具

可以看出第三层网络层协议上的目标 IP （Dst: 183.232.231.174）应该是分配给托管网络服务器的 baidu 服务器的 IP 地址之一。这里使用 “nslookup” 命令（“nslookup” 是一个命令行工具，用于查询域名对应的 IP 地址）进行进行验证：

首先打开命令行，输入 “nslooup” ，进入工具的交互界面，在这里我们可以通过输入域名查询 IP 地址：

![1633267836147](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633267836147.png)

我们输入 “xueshu.baidu.com”，这是通过 Wireshark 抓包得来的 HTTP 域名：

![1633267943633](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633267943633.png)

验证完毕。



###### 1.2.3 TCP 分析

接下来分析传输层数据包 “Transmission Control Protocol” ，该层
