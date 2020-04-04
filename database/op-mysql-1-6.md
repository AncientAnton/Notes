# 高性能MySQL

High Performance MySQL，3rd，ISBN: 9787121198854 第一章 ~ 第六章

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
    * 文本数据，有字符和排序规则，TINYTEXT, TEXT, MEDIUMTEXT, LONGTEXT
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

## 5.1 索引基础

### 5.1.1 B-Tree索引

支持的匹配方式

* 全值匹配
* 匹配最左前缀
* 匹配列前缀
* 匹配范围值
* 精确匹配某一列并范围匹配另一列
* 只访问索引的查询（无需访问数据行）

匹配的限制

* 索引只能从左往右匹配
* 不能跳过索引中的列
* 查询中有某个列的范围查询，则此列右边的所有列无法使用索引优化查询

### 5.1.2 哈希索引

Memory引擎显式支持哈希表

```
CREATE TABLE testhash {
    fname VARCHAR(50) not null,
    lname VARCHAR(50) not null,
    KEY USING HASH(fname)
} ENGINE = MEMORY;
```

* 索引紧凑
* 只包含哈希值与行指针
* 无法排序
* 不支持部分哈希列匹配查询，HASH(A, B)索引只用A列无法查找
* 只支持等值查询，不支持范围查询
* 哈希冲突的限制

- InnoDB引擎的特殊功能：自适应哈希索引
- 频繁使用的索引值，在B-Tree索引上建立额外的哈希索引
- InnoDB中使用额外的哈希列，用于快速查询。例如链接地址URL使用CRC32转为URL_CRC32

### 5.1.3 其它索引

* R-TREE空间数据索引
* 全文索引
* 分形树索引（fractal tree index）

## 5.2 索引的优点

1. 减少扫描数据量
2. 避免排序与临时表
3. 随机IO变为顺序IO

## 5.3 高性能的索引策略

### 5.3.1 使用独立的列

简化WHERE条件，始终将索引列单独放在比较符的一侧

```
SELECT id FROM tbl WHERE id + 1 = 5

优化为

SELECT id FROM tbl WHERE id = 4
```

### 5.3.2 前缀索引和索引选择性

对于BLOB、TEXT或者很长的VARCHAR类型列，MySQL不允许索引列的完整长度

```
ALTER TABLE tbl ADD KEY (city(7));
```

* 前缀索引无法ORDER BY 与 GROUP BY
* 前缀索引无法做覆盖扫描

### 5.3.3 多列索引

使用EXPLAIN检查是否有索引合并的情况

### 5.3.4 选择合适的索引列顺序

尽量把选择性高的列放在前面，

### 5.3.5 聚簇索引

* 主键索引
    * 叶子节点存储数据行、主键值、事务ID、回滚指针
    * 相关数据放在一起
    * 访问更快
    * 覆盖索引扫描的查询可以直接使用叶节点中的主键值
* 二级索引
    * 叶子节点存储数据行的主键值

### 5.3.6 覆盖索引

索引包含/覆盖了查询需要的所有字段

### 5.3.7 使用索引扫描做排序

* 索引列的顺序与ORDER BY子句中的列顺序一致
* ORDER BY中所有列的顺序一致
* 满足索引的最左前缀要求（或者前导列指定为常量）

### 5.3.8 压缩索引

MyISAM使用前缀压缩，减少索引的大小。

perform performance -> perform 7,ance

### 5.3.9 冗余和重复索引

* MySQL运行在相同的列上创建多个索引
* 对于重复索引需要删除
* 对于冗余索引(A, B) (B, A)这种需要考虑具体情况

### 5.3.10 未使用的索引

检查索引的使用频率，删除未使用的索引，提升写入删除效率

### 5.3.11 索引和锁

InnoDB在主键索引使用排他锁，在二级索引使用共享锁

## 5.4 索引学习

* 支持多种过滤条件
* 避免多个范围条件
* 优化排序

## 5.5 维护索引和表

* 检查修复表
    * CHECK TABLE
    * REPAIR TABLE
* 查看索引的基准信息
    * SHOW INDEX FROM
* 优化表（行碎片、行间碎片、剩余空间碎片）
    * OPTIMIZE TABLE

# 第6章 查询性能优化

## 6.1 慢查询的原因

* 网络IO
* CPU处理
* 统计信息
* 执行计划
* 锁等待
* 针对整个查询的生命周期

## 6.2 优化数据访问

### 6.2.1 请求过多数据

* 查询不需要的记录
* 多表关联返回全部列
* 取出全部列
* 重复查询相同数据

### 6.2.2 扫描额外记录

* 响应时间
* 扫描的行数与返回的行数
* 扫描的行数与访问的类型
    * 全表扫描
    * 索引扫描
    * 范围扫描
    * 唯一索引扫描
    * 常数引用
    * ...

## 6.3 重构查询方式

* 切分查询
* 分解关联查询
    * 提高缓存命中率
    * 减少锁竞争
    * 提高查询效率
    * 减少冗余记录查询
    * 在应用层关联，后续方便数据库拆分

## 6.4 查询执行的基础

MySQL的请求处理流程

1. 判断查询缓存是否命中
2. 解析器生成解析树
3. 预处理器进行处理
4. 查询优化器生成执行计划
5. 执行引擎调用存储引擎的API完成执行计划
6. 返回结果

### 6.4.1 查询的状态变更

> SHOW FULL PROCESSLIST

* Sleep
    * 线程正在等待客户端发送新的请求
* Query
    * 线程正在执行查询或正在将结果发送给客户端
* Locked
    * 线程正在等待表锁
* Analyzing and statistics
    * 线程正在收集存储引擎的统计信息，并生成查询计划
* Copying to tmp table [on disk]
    * 线程正在执行查询，并将结果复制到临时表中（一般是GROPY BY / UNION / 文件排序操作）
* Sorting result
    * 线程正在排序结果
* Sending Data
    * 多个状态之间传送数据 / 生成结果集 / 向客户端发送数据

### 6.4.2 查询缓存

如果查询缓存开关打开，会优先检查是否命中缓存（通过一个大小写敏感的哈希查找实现）。
如果命中缓存，则检查当前用户是否有查询权限，有则直接返回结果。

### 6.4.3 查询优化处理

解析SQL -> 预处理 -> 优化SQL执行计划

1. 语法解析器和预处理

这一步包括解析SQL语法，生成解析树，预处理检查是否具有相关权限

2. 查询优化器

> SHOW STATUS LIKE 'last_query_cost';

通过成本计算公式，计算出执行计划的成本，考虑的因素有：

* 每个表/索引的页面数
* 索引的基数
* 索引和数据行的情况
* 索引分布的情况等等

2.1. 静态优化策略

比如简单的代数变换，类似【编译时优化】

2.2. 动态优化策略

根据执行上下文进行优化，类似【运行时优化】

2.3. 一些优化类型

* 重新定义关联表的顺序
* 将外连接Outer Join转化为内连接Inner Join
* 等价变换查询条件
* 优化COUNT() MAX() MIN()
    * 例如直接读取第一个记录、最后一个记录等
* 预估并转化为常数表达式
    * 一些语句的值可以被提前计算出
* 覆盖索引扫描
    * 索引中的列包括所有查询需要的列，不需要扫描数据行
* 子查询优化
* 提前终止查询
* 等值传播
* 列表IN()的比较
    * 将IN()列表中数据进行排序，然后通过二分查找确定
    * 转化为O(n)复杂度

3. 数据和索引的统计信息

存储引擎负责维护统计信息

4. 执行关联查询

使用嵌套循环查询来进行相关查询

5. 执行计划

一棵左侧深度优先的数

6. 关联查询优化器

包括2.3所述的一些优化，不断改进

7. 排序优化

MySQL统一称为文件排序(filesort)，优先使用内存，不够时使用磁盘进行缓冲

* 两次传输排序（旧版本）
    * 读取行指针和需要排序的字段，对其进行排序
    * 再根据排序结果，读取所需要的数据行
* 单次传输排序（新版本）
    * 一次读取查询所需要的所有列
    * 再根据给定的列进行排序

### 6.4.4 查询执行引擎

* 每个表有对应的Handler实例
* 按照执行计划执行

### 6.4.5 返回结果

处理完最后一个表，开始生成第一条结果并逐步返回

## 6.5 查询优化器的局限性

### 6.5.1 关联子查询

对于IN()查询的优化，不是先执行IN()中的子查询，而是将外层的表压到子查询中

### 6.5.2 UNION的限制

无法将限制条件从外层“下推”的内层，最好对内层查询额外加上限制

### 6.5.3 索引合并优化

WHERE子句包括多个复杂条件时，可以通过单个表的多个索引以合并/交叉过滤的方式定位需要查找的行

### 6.5.4 等值传递

比如将IN()列表查询传递到另一个关联表（当使用WHERE/ON/USING子句时），但是如果IN()列表比较大，
则会导致各个查询的复杂度提高。

### 6.5.5 并行查询

MySQL目前无法进行并行查询 （此书基于MySQL5.6）

### 6.5.6 哈希关联

不支持哈希关联，目前所有查询都是嵌套循环关联

### 6.5.7 松散索引扫描

假设有索引`(a, b)`，以及如下的语句

`select ... from tbl where b between 2 and 3`

此时因为未指定字段a，所以无法使用索引`(a, b)`。

如果可以按照以下流程

* 扫描`a`列第一个值对应的b范围
* 再往下扫描`a`列第二个值对应的b范围
* ...

可以通过给列`a`加上可能的常数值的方式跳过限制

> 相当于Oracle中的跳跃索引扫描(skip index scan)

### 6.5.8 最大值和最小值优化

比如对于主键`actor_id`有如下的查询

` select min(actor_id) from test.actor where first_name = 'Jhon' `

此时由于`first_name`列上不存在索引，所以MySQL会走全表扫描。

但其实因为`actor_id`是主键，此时只需要按照主键顺序往下扫描，第一个符合条件的就是最小值。

可以改写为下面的SQL，使得扫描记录表少，当前这样SQL会不太直观。

` select actor_id from test.actor using index(primary) where first_name = 'Jhon' limit 1 `

### 6.5.9 在同一张表上查询和更新

MySQL不允许这个操作。可以通过生成临时表来绕过限制，比如下面的语句

```
update tbl
    inner join (
        select type, count(*) as cnt
        from tbl
        group by type
    ) as der using(type)
set tbl.cnt = der.cnt;
```

## 6.6 查询优化器的提示（hint）

通过限制加入 hint 关键字，来指定控制执行计划

* HIGH_PRIORITY
* LOW_PRIORITY
* DELAYED
* STRAIGHT_JOIN
* SQL_SMALL_RESULT
* SQL_BIG_RESULT
* SQL_BUFF_RESULT
* SQL_CACHE
* SQL_NO_CACHE
* ...

## 6.7 优化特定类型的查询

### 6.7.1 优化COUNT()查询

COUNT(1) COUNT(*) 统计结果集的行数，InnoDB处理一致
COUNT(col) 统计该列有值（不为NULL）的数量

MySQL 5.7.18以下 SELECT COUNT(*) 是扫描聚簇索引
MySQL 5.7.18，则优先扫描最小的二级索引（如果有的话）

参考[https://dev.mysql.com/doc/refman/5.7/en/group-by-functions.html#function_count](https://dev.mysql.com/doc/refman/5.7/en/group-by-functions.html#function_count)

### 6.7.2 优化关联查询

* 确保 `USING / ON` 子句上的列有索引
* 需要考虑关联的顺序来建立索引
* 确保 `GROUP BY / ORDER BY` 中的表达式只涉及到一个表中的列
* 升级MySQL版本需要注意SQL的执行计划是否有变

### 6.7.3 优化子查询

MySQL5.6以下尽量使用关联查询代替子查询

### 6.7.4 优化GROUP BY / DISTINCT

* 尽量使用索引上的列

### 6.7.5 优化LIMIT分页

LIMIT可能导致扫描大量不需要的行再丢弃

* 使用“延迟关联”先找出小的结果集
* 使用“书签”，限定翻页的范围

### 6.7.6 优化SQL_CALC_FOUND_ROWS

没有太大用，可以使用比页记录数多获取一条的方式来确定是否有下一页

### 6.7.7 优化UNION查询

手动将外层查询条件下推写到子查询中，减少记录数

### 6.7.8 静态查询分析

使用工具进行静态分析

### 6.7.9 使用用户自定义变量

* 无法使用查询缓存
* 不能在需要常量或标识符使用，比如表名、列名、LIMIT
* 变量的生命周期是一个连接中
* 持久化连接中可能会有BUG
* 变量名大小写敏感
* 不能显式声明类型，最好在初始化时确定
* 优化器可能优化掉变量
* 赋值顺序与时间点不固定，取决于优化器
* 赋值符号 `:=` 的优先级比较低，最好使用明确的括号
* 使用未定义变量不会有语法错误，容易犯错

类似`set @rownum := 0`