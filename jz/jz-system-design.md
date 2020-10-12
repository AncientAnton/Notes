# 基础

* DDIA-概览

# 秒杀系统和订票系统

* 高并发场景下引发的常见问题
* 了解数据一致性
* 什么是动静分离
* 读写分离如何实现
* 如何防止超卖

# 用户系统

* NoSQL 与 SQL 数据库的优劣比较与选取标准
* 如何进行数据库分片 Sharding

# 网站系统

* 网站系统的基本构成
* API 设计问题
* 什么是 RestAPI
* 实战真题
  - What happend if you visit www.google.com?
  - How to design tiny url?
  - 如何设计 News Feed API
  - 如何设计 mention 功能
  - 如何做翻页 Pagination
* 关键词：Web, Consistent Hashing, Memcached, Tiny url.

# 分布式文件系统 GFS

* 以 GFS 为例系统学习分布式文件系统，了解如下内容：
* Master Slave 的设计模式
* 分布式文件系统的读写流程
* 怎么处理分布式系统中的failure 和recovery 的问题.
* 如何做replica, check sum 检查
* 了解consistent hash和sharding的实际应用

# 文档协同编辑系统设计

* websocket 在协同编辑中的应用
* 了解什么是协同编辑
* 协同编辑的几种实现方案
* 如何解决编辑冲突问题
* 了解 OT（ Operational Transformation）原理

# 分布式数据库 Big Table

> TiDB文档

* 通过设计分布式数据库系统Bigtable了解如下内容：
* Big Table 的原理与实现
* 了解NoSQL Database如何进行读写操作的,以及相应的优化
* 了解如何建立index
* 学习Bloom Filter的实现原理

# 聊天系统 IM System

* 聊天系统中的 Pull vs Push
* 讲解一种特殊的 Service - Realtime Service
* 用 channel 优化群聊
* 如何限制多机登录
* 用户在线状态的获取与查询 Online Status

# 视频流系统设计

* 视频切分和断点续传如何实现
* 如何在架构设计中节省带宽
* 小文件存储之视频切片与缩略图存储
* 了解视频预加载
* 了解 CDN 的基本原理

# 基于地理位置的信息系统
* 系统学习LBS相关系统设计的核心要点：
* 地理位置信息存储与查询常用算法之 Geohash
* 如何设计 Uber
* 关键点：学会设计 Uber 以后可以轻松解决设计 Facebook Nearby 和 Yelp
# 分布式计算 Map Reduce

* 学习Map Reduce 的应用与原理
* 了解如何多台机器并行解决算法问题
* 掌握Map和Reduce的原理
* 通过三个题目掌握MapReduce算法实现：
* WordCount
* InvertedIndex
* Anagram

# 容器技术（K8S，Docker）与系统设计结合

* 了解什么是Docker
* 什么是镜像(image)和容器(container)
* 了解容器编排技术
* 微服务和容器的关系
* 什么是蓝绿发布
* 工程实践微服务和K8S的结合

# 爬虫系统与搜索建议系统

* 通过对爬虫系统设计 (Web Crawler) 与 搜索建议系统设计 (Google Suggestion) 了解如下内容：
* 多线程
* 生产者消费者模型
* 爬虫系统的演化：单线程，多线程，分布式
* Trie 结构的原理及应用
* 如何在系统设计中使用 Trie

参考：[system-design-ladder](https://github.com/Crazyconv/jiuzhang/tree/master/system-design-ladder)