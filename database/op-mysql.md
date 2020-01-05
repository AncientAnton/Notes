# 高性能MySQL

High Performance MySQL，3rd，ISBN: 9787121198854

# 第1章 MySQL架构与历史

## 1.1 MySQL逻辑架构

1.连接/线程处理

2.查询缓存

3.解析器

4.优化器（hint、explain）

5.存储引擎

## 1.2 并发控制

1. 锁

- 共享锁（share lock）= 读锁（read lock）
- 独享/排他锁（exclusive lock）= 写锁（write lock）

2. 锁粒度

- 表级锁（table lock）
- 行级锁（row lock）

3. 事务

事务ACID特性

- 原子性（Atomic）
一个事务中的操作要么全部成功，要么全部失败，事务视为一个不可分割的最小工作单元。
- 一致性（Consistency）
数据库总是从一个一致性的状态转换到另一个一致性的状态，操作前后，数据保持完整。
- 隔离性（Isolation）
一个事务的数据修改在最终提交前，对其它事务时不可见的。
- 持久性（Durability）
一旦事务提交，修改保存到数据库不会丢失。

## 1.3 事务

1. 隔离级别

- 未提交读（Read Uncommitted）
一个事务的修改没有提交时，对其它事务也可见。事务可以读取未提交的数据，也称为脏读（Dirty Read），比较少用。
- 提交读（Read Committed）
一个事务开始时，只能“看见”已经提交的事务做的修改。也称为不可重复读（Norepeatable Read），因为两次执行相同的查询，可能会得到不同的结果。
- 可重复读（Repeatable Read）
解决了不可重复读的问题，但是可能出现幻读（Phantom Read）的情况。一个事务（T1）读取了几行数据，接着另一个并发事务（T2）插入了一些数据时。在随后的查询中，第一个事务（T1）就会发现多了一些原本不存在的记录。InnoDB通过多版本并发控制（MVVC）解决幻读的问题。
- 串行（Serializable）
强制事务串行执行，读取的每一行都会加上锁，避免幻读（Phantom Read）的问题。

隔离级别|脏读可能性|不可重复读可能性|幻读可能性|加锁读
-------|---------|---------------|---------|-----
Read Uncommitted|Yes|Yes|Yes|No
Read Committed|No|Yes|Yes|No
Repeatable Read|No|No|Yes|No
Serializable|No|No|No|Yes

2. 死锁

- 两个或多个事务在同一资源上互相占用，并且请求对方所占用的资源。
- 当多个事务试图以不同的顺序锁定资源，或同时锁定一个资源，就会产生死锁。

MySQL实现了各种死锁侦测与死锁超时机制，死锁发生后，必须部分或者完全回滚一个事务。

3. 事务日志

将修改行为记录到磁盘上的事务日志中，然后将日志持久化到存储引擎。

4. MySQL中的事务

- 自动提交
默认自动提交，通过`SET AUTOCOMMIT = 0|ON|1|OFF`设置
- 事务级别
通过 `SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED|READ COMMITED|REPEATABLE|SERIALIZABLE`设置
- 隐式锁定
InnoDB采取两段式锁定协议（two-phase locking protocol），执行过程中，随时可以执行锁定，锁在COMMIT或者ROLLBACK时全部释放。InnoDB根据隔离级别自动加锁。
- 显式锁定
也可以通过下面的语句进行显式的锁定
```
SELECT ... LOCK IN SHARE MODE
SELECT ... FOR UPDATE
```

## 多版本并发控制

大部分数据库实现了MVVC机制，而不是简单的行级锁。

- 每行记录都会有两个版本号：创建版本号与删除版本号
- 每开始一个事务，系统版本号都会自动递增
- 事务开始时的版本号，


SELECT

## MySQL的存储引擎