## 事务

特性（ACID）

1. 原子性
2. 一致性
3. 隔离性
4. 持久性


并发事务带来的问题

1. 脏读
2. 丢失修改
3. 不可重复读（修改）
4. 幻读（新增或者删除）

SQL标准定义的事务隔离级别（由低到高）

1. READ-UNCOMMITTED（读取未提交）
2. READ-COMMITTED（读取已提交）
3. REPEATABLE-READ（可重复读）
4. SERIALIZABLE（可串行化）

mysql使用可重复读，并使用next-key lock算法来避免幻读发生

## 索引

数据结构：B+树

## 存储引擎

myisam和innodb

1. 行级锁：前者只有表级锁，后者支持行级锁和表级锁，默认行级锁
2. 事务和崩溃后的安全恢复:前者不支持
3. 外键：前者不支持
4. MVCC：前者不支持

## 锁

1. 悲观锁：每次拿数据都加锁
    1. 适用：多写
2. 乐观锁：总是假设最好的情况，每次拿数据都认为别人不会修改
	1. 适用：多读
	1. 实现
        1. MVCC：版本号机制。一般是在数据表中加上一个数据版本号version字段，表示数据被修改的次数，当数据被修改时，version值会加一。当线程A要更新数据值时，在读取数据的同时也会读取version值，在提交更新时，若刚才读取到的version值为当前数据库中的version值相等时才更新，否则重试更新操作，直到更新成功。
        2. CAS：compare and swap，一种著名的无锁算法。
	1. 问题
		1. ABA问题
		2. 循环时间长开销大
		3.  只能保证一个共享变量的原子操作

锁算法

1. Record local
2. Gap lock
3. Next-key lock（innodb适用可重复读事务级别+next-key lock解决幻读）

## 大表优化

1. 限定数据的范围（where limit..）
2. 读写分离（主写、从读）
3. 垂直分区（按列拆分）
    1. 优点：可以使得列数据变小，在查询时减少读取的Block数，减少I/O次数。此外，垂直分区可以简化表的结构，易于维护。
    2. 缺点：主键会出现冗余，需要管理冗余列，并会引起Join操作，可以通过在应用层进行Join来解决。此外，垂直分区会让事务变得更加复杂。
4. 水平分区（按行拆分）：分库
    1. 客户端代理：分片逻辑在应用端，封装在jar包中，通过修改或者封装JDBC层来实现。 当当网的 Sharding-JDBC 、阿里的TDDL是两种比较常用的实现。
    2. 中间件代理： 在应用和数据中间加了一个代理层。分片逻辑统一维护在中间件服务中。 我们现在谈的 Mycat 、360的Atlas、网易的DDB等等都是这种架构的实现。

[一条SQL执行很慢怎么分析](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485185&idx=1&sn=66ef08b4ab6af5757792223a83fc0d45&chksm=cea248caf9d5c1dc72ec8a281ec16aa3ec3e8066dbb252e27362438a26c33fbe842b0e0adf47&token=79317275&lang=zh_CN#rd)

1. 大多数情况下正常，偶尔很慢
    1. 数据库在刷新脏页（flush），例如redo log 写满了需要同步到磁盘
    2. 执行的时候遇到锁，如表级锁、行级锁（show processlist 查看当前状态）
2. 一直很慢
    1. 没有用上索引：例如该字段没有索引；由于对字段进行运算、函数操作导致无法用索引
    2. 数据库选错了索引

[一条SQL语句在MySQL中如何执行的](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485097&idx=1&sn=84c89da477b1338bdf3e9fcd65514ac1&chksm=cea24962f9d5c074d8d3ff1ab04ee8f0d6486e3d015cfd783503685986485c11738ccb542ba7&token=79317275&lang=zh_CN#rd)
