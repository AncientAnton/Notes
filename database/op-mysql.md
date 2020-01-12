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

## 1.4 多版本并发控制

大部分数据库实现了MVVC机制，而不是简单的行级锁。

- 每行记录都会有两个版本号：创建版本号与删除版本号
- 每开始一个事务，系统版本号都会自动递增
- 事务开始时的系统版本号，作为当前事务的版本号


* SELECT
    * 查找创建版本号小于当前事务版本的行 （保证行是之前就存在、或者由当前事务插入或修改）
    * 行的删除版本号未定义，或者大于当前事务版本号
* INSERT
    * 插入的每一行，设置当前系统版本号为创建版本号
* DELETE
    * 删除的每一行，设置当前系统版本号为删除版本号
* UPDATE
    * 插入一行新记录，保存当前版本号为创建版本号
    * 当前系统版本号，设置为原来行的删除版本号

## 1.5 MySQL的存储引擎

### 1.5.1 InnoDB 存储引擎

* 表基于聚簇索引创建
* 默认级别 REPEATABLE READ
* MVCC + 间隙锁(防止幻读)

### 1.5.2 MyISAM 存储引擎

* 全文索引、压缩、空间函数（GIS）
* 不支持事务和行级锁
* 崩溃无法安全恢复
* 可设置延迟更新主键、支持压缩表

### 1.5.3 其它存储引擎

* Archive - 只支持INSERT和SELECT，适用高速插入和压缩
* Blackhole - 丢弃所有插入的数据，但是会记录日志
* CSV - 将CSV文件作为表处理，可以快速导入，适用与数据交换
* Federated - 访问其它MySQL服务器的代理
* Memory - 全部放在内存中，支持哈希索引
* Merge - 被放弃
* NDB集群 - 后续讨论
* OLTP类、列类、第三方社区类

### 1.5.4 修改表的存储引擎

* ALTER TABLE tbl ENGINE = InnoDB;
    * 占用系统的IO
    * 原表上会加锁
* 导入再导出
    * mysqldump导出后
    * 修改CREATE TABLE语句
    * 重新建表
* INSERT INTO new_tbl SELECT * from old_tbl;

# 2. MySQL基准测试

## 2.1 测试指标

* 吞吐量
* 响应时间
* 并发性
* 可扩展性

## 2.2 测试方法

* 确定测试计划（不同环境配置）
* 获取系统的性能指标
* 每次改变的参数尽可能少，记录准确的结果
* 对结果进行分析对比

## 2.3 测试工具

* wrk
* ab
* http_load
* JMeter
* mysqlslap
* sql-bench
* BENCHMARK函数

# 3. 服务器性能优化

* 慢查询日志
* show status
* show gloabl status
* show processlist

# 4. Schema与数据类型优化

## 4.1 数据类型的选择

* 最小数据类型
* 简单就好
    * 整形比字符操作代价低，由于字符集、校验规则、读取效率等
    * 内建类型存储日期和时间，整形存放IP
* 尽量避免NULL
    * 可为NULL的字段被索引时，需要额外的一个字节
    * MyISAM中索引大小不固定

### 4.1.1 整数类型

* TINYINT
    * 1个字节
* SMALLINT
    * 2个字节
* MEDIUMINT
    * 3个字节
* INT / INTEGER
    * 4个字节
* BIGINT
    * 8个字节

*1. 加上UNSIGNED 可以取消符号位，只允许正数，扩大范围*
*2. INT(1)与INT(11)范围一致，只是展示宽度不一致*

### 4.1.2 实数类型

* FLOAT
    * 4个字节
* DOUBLE
    * 8个字节
* DECIMAL
    * DECIMAL(18, 9) 小数点两边各存储9个数字
    * 最多65位数

### 4.1.3 字符串类型

* CHAR
    * 固定长度的字符串（最多255个字符）
* VARCHAR
    * 可变长度的字符串（最多65,535个字节）
    * 需要1 / 2 个额外字节记录长度
    * 若设置ROW_FORMAT=FIXED 则使用固定长度
    * 最大长度超出太多、列更新少、使用了UTF-8字符集（字符的字节数不一样）
* BLOB
    * 二进制数据，TINYBLOB, BLOB, MEDIUMBLOB, LONGBLOB
    * 字节数 2^8 2^16 2^32 2^64
* TEXT
    * 文本数据，有字符和排序规则，TINYTEXT, TEXT, MEDIUMTEXT, , LONGTEXT
    * 字节数 2^8 2^16 2^32 2^64
* BINARY
    * 类似CHAR
* VARBINARY
    * 类似VARCHAR
* ENUM
    * 以整形存储并排序
    * `NULL` 对应 `NULL`, `''` 对应 `0`
    * 其余对应 `1 ~ N`
```
CREATE TABLE shirts (
    name VARCHAR(40),
    size ENUM('x-small', 'small', 'medium', 'large', 'x-large')
);
INSERT INTO shirts (name, size) VALUES ('dress shirt','large'), ('t-shirt','medium'),
  ('polo shirt','small');
SELECT name, size FROM shirts WHERE size = 'medium';
+---------+--------+
| name    | size   |
+---------+--------+
| t-shirt | medium |
+---------+--------+
UPDATE shirts SET size = 'small' WHERE size = 'large';
COMMIT;
```

`SET max_sort_length = 2000` 用于设置字段索引的长度，默认1024

### 4.1.4 日期与时间类型

* DATE
    * `YYYY-MM-DD` 
    * `1000-01-01` ~ `9999-12-31`
* DATETIME
    * `YYYY-MM-DD hh:mm:ss` 
    * `1000-01-01 00:00:00` ~ `9999-12-31 23:59:59`
    * 精度为秒，8个字节，存储方式为`YYYYMMDDhhmmss`的证书
* TIMESTAMP
    * UNIX时间戳类似，存储的是秒数
    * FROM_UNIXTIME() 函数 与 UNIX_TIMESTAMP() 函数
    * `1970-01-01 00:00:01` UTC ~ `2038-01-19 03:14:07` UTC
    * INSERT 没有指定具体值时，会自动设置为当前时间
    * UPDATE  没有指定具体值时。会自动设置第一个列为当前时间
    * 默认NOT NULL，与其它类型不一致
* TIME
    * `-838:59:59` ~ `838:59:59`
* YEAR
    * YEAR(2)
        *  `0` ~ `99`
        * `0` ~ `69` = `2000` ~ `2069`
        * `70` ~ `99` = `1970` ~ `1999`
    * YEAR(4)
        * `1901` ~ `2155`

### 4.1.5 位数据类型

* BIT
    * BIT(M) 最多64位
* SET
    * 逗号分割的多个字符串
    * 可以包含SET中的多个值
    * 存储方式类似：0001 0010 0100 1000
    * 支持 `&` `FIND_IN_SET` `LIKE`查询
```
CREATE TABLE myset (col SET('a', 'b', 'c', 'd'));
INSERT INTO myset (col) VALUES  ('a,d'), ('d,a'), ('a,d,a'), ('a,d,d'), ('d,a,d');
SELECT * FROM tbl_name WHERE FIND_IN_SET('value',set_col)>0;
SELECT * FROM tbl_name WHERE set_col LIKE '%value%';
```

### 4.1.6 选择ID

* 字符串-避免字符串-占用空间太大
* UUID-使用时需要去掉`-`
* 整形-自增整形是最好用的

```
Snowflakes算法生成64位整形ID
{1位}{41位}{5位}{5位}{12位}
1位不用。固定是0
41位用来记录时间戳（毫秒），大概69年。
5位workerGroupId
5位workerId
12位序列号，0~4096
```

### 4.1.7 IP地址

* 应该使用32位的无符号的整数存储
* `INET_ATON()`函数用于转换整数为IP字符串
* `INET_NTOA()`函数用于转换IP字符串为整数

# 5. 创建高性能的索引
