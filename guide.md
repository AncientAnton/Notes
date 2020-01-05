# 1. 前言

复习大纲

# 2. 操作系统

## 2.1 基础

- ★★★  进程与线程的本质区别、以及各自的使用场景。
- ★☆☆ 进程状态。
- ★★★ 进程调度算法的特点以及使用场景。
- ★☆☆ 线程实现的方式。
- ★★☆ 协程的作用。
- ★★☆ 常见进程同步问题。
- ★★★ 进程通信方法的特点以及使用场景。
- ★★★ 死锁必要条件、解决死锁策略，能写出和分析死锁的代码，能说明在数据库管理系统或者 Java 中如何解决死锁。
- ★★★ 虚拟内存的作用，分页系统实现虚拟内存原理。
- ★★★ 页面置换算法的原理，特别是 LRU 的实现原理，最好能手写，再说明它在 Redis 等作为缓存置换算法。
- ★★★ 比较分页与分段的区别。
- ★★★ 分析静态链接的不足，以及动态链接的特点。

## 2.2 Linux

- ★★☆ 文件系统的原理，特别是 inode 和 block。数据恢复原理。
- ★★★ 硬链接与软链接的区别。
- ★★☆ 能够使用常用的命令，比如 cat 文件内容查看、find 搜索文件，以及 cut、sort 等管线命令。了解 grep 和 awk 的作用。
- ★★★ 僵尸进程与孤儿进程的区别，从 SIGCHLD 分析产生僵尸进程的原因。


# 3. 网络

## 3.1 基础

- ★★★ 各层协议的作用，以及 TCP/IP 协议的特点。
- ★★☆ 以太网的特点，以及帧结构。
- ★★☆ 集线器、交换机、路由器的作用，以及所属的网络层。
- ★★☆ IP 数据数据报常见字段的作用。
- ★☆☆ ARP 协议的作用，以及维护 ARP 缓存的过程。
- ★★☆ ICMP 报文种类以及作用；和 IP 数据报的关系；Ping 和 Traceroute 的具体原理。
- ★★★ UDP 与 TCP 比较，分析上层协议应该使用 UDP 还是 TCP。
- ★★★ 理解三次握手以及四次挥手具体过程，三次握手的原因、四次挥手原因、TIME_WAIT 的作用。
- ★★★ 可靠传输原理，并设计可靠 UDP 协议。
- ★★☆ TCP 拥塞控制的作用，理解具体原理。
- ★★☆ DNS 的端口号；TCP 还是 UDP；作为缓存、负载均衡。

## 3.2 HTTP

- ★★★ GET 与 POST 比较：作用、参数、安全性、幂等性、可缓存。
- ★★☆ HTTP 状态码。
- ★★★ Cookie 作用、安全性问题、和 Session 的比较。
- ★★☆ 缓存 的Cache-Control 字段，特别是 Expires 和 max-age 的区别。ETag 验证原理。
- ★★★ 长连接与短连接原理以及使用场景，流水线。
- ★★★ HTTP 存在的安全性问题，以及 HTTPs 的加密、认证和完整性保护作用。
- ★★☆ HTTP/1.x 的缺陷，以及 HTTP/2 的特点。
- ★★★ HTTP/1.1 的特性。
- ★★☆ HTTP 与 FTP 的比较。

## 3.3 Socket

- ★★☆ 五种 IO 模型的特点以及比较。
- ★★★ select、poll、epoll 的原理、比较、以及使用场景；epoll 的水平触发与边缘触发。

# 4. 数据库

## 4.1 SQL

- ★★☆ 手写 SQL 语句，特别是连接查询与分组查询。
- ★★☆ 连接查询与子查询的比较。
- ★★☆ drop、delete、truncate 比较。
- ★★☆ 视图的作用，以及何时能更新视图。
- ★☆☆ 理解存储过程、触发器等作用。

## 4.2 系统原理

- ★★★ ACID 的作用以及实现原理。
- ★★★ 四大隔离级别，以及不可重复读和幻影读的出现原因。
- ★★☆ 封锁的类型以及粒度，两段锁协议，隐式和显示锁定。
- ★★★ 乐观锁与悲观锁。
- ★★★ MVCC 原理，当前读以及快照读，Next-Key Locks 解决幻影读。
- ★★☆ 范式理论。
- ★★★ SQL 与 NoSQL 的比较。

## 4.3 MySQL

- ★★★ 数据类型
- ★★★ B+ Tree 原理，与其它查找树的比较。
- ★★★ MySQL 索引以及优化。
- ★★★ 查询优化。
- ★★★ InnoDB 与 MyISAM 比较。
- ★★☆ 水平切分与垂直切分（一致性哈希）。
- ★★☆ 主从复制原理、作用、实现。
- ★☆☆ redo、undo、binlog 日志的作用。

## 4.4 Redis

- ★★☆ 字典和跳跃表原理分析。
- ★★★ 使用场景。
- ★★★ 与 Memchached 的比较。
- ★☆☆ 数据淘汰机制。
- ★★☆ RDB 和 AOF 持久化机制。
- ★★☆ 事件驱动模型。
- ★☆☆ 主从复制原理。
- ★★★ 集群与分布式。
- ★★☆ 事务原理。
- ★★★ 线程安全问题。
- ★★★ 缓存击穿 
- ★★★ 缓存雪崩 
- ★★☆ 监控与性能分析方法

## 4.5 Oracle

- ★★★ 体系结构
- ★★☆ 各类文件的作用
- ★★☆ 内存结构
- ★★☆ 各类锁
- ★★☆ redo、undo
- ★★☆ 段、水位线
- ★★★ 索引
- ★★☆ 索引分区

# 5. 面向对象

## 5.1 思想

- ★★★ 面向对象三大特性
- ★☆☆ 设计原则

## 5.2 设计模式

- ★★☆ 设计模式的作用。
- ★★★ 手写单例模式，特别是双重检验锁以及静态内部类。
- ★★★ 手写工厂模式。
- ★★★ 理解 MVC，结合 SpringMVC 回答。
- ★★★ 理解代理模式，结合 Spring 中的 AOP 回答。
- ★★★ 分析 JDK 中常用的设计模式，例如装饰者模式、适配器模式、迭代器模式等。

<br>

# 6. 分布式理论

- ★★★ 分布式理论（CAP、BASE）
- ★★★ 分布式事务（XA、2PC、3PC、TCC、XTS、消息最终一致、GTS、Fescar）
- ★★★ 分布式锁（Redis、Zookeeper、Etcd）
- ★★★ 分布式算法（Paxos算法、Raft算法）
- ★★★ 分布式ID生成算法（snowflakes）


# 7. 消息队列

*RocketMQ*

- ★★★ Push/Pull模式对比
- ★★★ 使用场景
- ★★★ 主要组件
- ★★★ 消息发送的过程
- ★★★ 消息消费的过程
- ★★★ 持久化机制
- ★★☆ 如何避免消息丢失
- ★★★ 消息堆积处理
- ★★★ 消息过期失效处理
- ★★★ 幂等性
- ★★★ 顺序消费的实现
- ★★☆ 消息广播
- ★★☆ 集群中节点的角色与作用
- ★★☆ 节点崩溃了如何恢复？有什么影响？

# 8. Java

- ★★★ 基础数据类型、基础语法、关键字
- ★★★ 基础库原理（集合、IO、String）
- ★★★ 并发编程（线程、线程池、并发集合、锁、AQS）
- ★★★ 反射与代理
- ★★★ 常见异常与底层机制
- ★★★ 对象克隆与对象拷贝
- ★★★ Stream、Lambda、FunctionInterface
- ★★☆ JDK9/10/11/12/13新特性

# 9. Jvm

- ★★★ 类加载流程（如何修改字节码）
- ★★★ ClassLoader常见用法
- ★★★ JVM内存布局
- ★★★ JVM内存模型（内存屏障、虚拟机指令、重排序）
- ★★★ JVM参数调优
- ★★★ 常用垃圾收集器及特点
- ★★★ GC算法实现对比
- ★★☆ 常用命令（jstack jhat jmap）
- ★★☆ 日志（如何分析thread log、gc log、heap log）
- ★☆☆ Arthas源码分析

# 10. Spring框架

- ★★★ Spring-Beans（初始化、生命周期、处理循环依赖等）
- ★★★ Spring-Aop（实现细节）
- ★★☆ Spring-Tx（事务实现的细节，应用异常导致事务失败？）
- ★★★ Spring-Mvc（启动过程，主要扩展点）
- ★★★ Spring-Boot（启动过程、AutoConfigure）
- ★★☆ Spring-Cloud（分布式相关组件大概原理）


参考 [一文帮你理清面试知识点](https://github.com/CyC2018/Backend-Interview-Guide/blob/master/doc/%E4%B8%80%E6%96%87%E5%B8%AE%E4%BD%A0%E7%90%86%E6%B8%85%E9%9D%A2%E8%AF%95%E7%9F%A5%E8%AF%86%E7%82%B9.md)