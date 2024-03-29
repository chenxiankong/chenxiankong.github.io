# My First Post




# 前置知识

查看隔离级别：select @@transaction_isolation;

脏读：读到了不存在的数据

不可重复读：一次事务内对同一记录的读取结果可能不同（update）

可重复读（一致性读）：一次事务内对一个记录的读取结果相同(update)

幻读：一次事务内对一批数据的读取结果不同(insert、delete)

# MVCC简介

Multiversion Concurrency Control 多版本并发控制

原理：通过数据行的多个版本管理来实现数据库的并发控制，简单来说就是保存数据的历史版本。可以通过比较版本号决定数据是否显示出来。读取数据的时候不需要加锁可以保证事务的隔离效果。

postgres在行上实现mvcc，Mysql innodb 在undolog中实现

MVCC仅在RR RC隔离级别下工作,其他两个隔离级别够和MVCC不兼容, 因为 RU总是读取最新的数据行, 而不是符合当前事务版本的数据行。而 `SERIALIZABLE` 则会对所有读取的行都加锁

# Rel

https://segmentfault.com/a/1190000012650596

mysql8.0官方文档：https://dev.mysql.com/doc/refman/8.0/en/i

# end

新插入数据时，并不涉及undo log的读取，举个例子，以RR级别为例，假如事务A执行了select操作，同时事务B执行了insert操作（两个操作的数据范围相近），那么A再尝试执行同样的insert时，会因为B已经插了数据而导致A的操作的失败，但是对于A来说，无法察觉这行数据的存在，所以A产生了幻读。并且，在RR级别下，幻读的问题是已经解决了的，通过对数据加行锁/间隙锁。可以参考。

#### 一致的非锁定读取

如果事务 [隔离级别](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_isolation_level)为 `REPEATABLE READ`（默认级别），则同一事务中的所有一致性读取都会读取该事务中第一个此类读取建立的快照。您可以通过提交当前事务然后发出新查询来为您的查询获取更新的快照。

使用`READ COMMITTED`隔离级别，事务中的每个一致性读取设置并读取自己的新快照。

一致的读取是在默认模式 `InnoDB`进程 `SELECT`中的语句 `READ COMMITTED`和 `REPEATABLE READ`隔离级别。一致读取不会在它访问的表上设置任何锁，因此其他会话可以在对表执行一致读取的同时自由修改这些表。

假设您在默认`REPEATABLE READ`隔离级别下运行 。当您发出一致读取（即普通 `SELECT`语句）时， `InnoDB`为您的事务提供一个时间点，您的查询根据该时间点查看数据库。如果另一个事务在分配您的时间点后删除一行并提交，您不会看到该行已被删除。插入和更新的处理方式类似。

数据库状态的快照适用 `SELECT`于事务中的语句，不一定适用于 [DML](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_dml)语句。如果您插入或修改某些行然后提交该事务，则从另一个并发 事务发出的 `DELETE`or `UPDATE`语句`REPEATABLE READ`可能会影响那些刚刚提交的行，即使会话无法查询它们。如果一个事务确实更新或删除了由不同事务提交的行，则这些更改对当前事务是可见的

您可以通过提交您的事务然后执行另一个`SELECT`或 来提前您的时间点`START TRANSACTION WITH CONSISTENT SNAPSHOT`。

这称为多版本并发控制。

https://dev.mysql.com/doc/refman/8.0/en/innodb-consistent-read.html

一致读取不适用于某些 DDL 语句：

- 一致性读取不起作用`DROP TABLE`，因为 MySQL 无法使用已删除并`InnoDB`破坏该表的表。
- 一致读取不适 `ALTER TABLE`用于制作原始表的临时副本并在构建临时副本时删除原始表的操作。当您在事务中重新发出一致读取时，新表中的行不可见，因为在获取事务快照时这些行不存在。在这种情况下，事务返回一个错误： `ER_TABLE_DEF_CHANGED`， “表的定义发生了变化，请重试交易”。
- 该类型读为like子句选择不同 `INSERT INTO ... SELECT`，`UPDATE ... (SELECT)`和 `CREATE TABLE ... SELECT`没有指定`FOR UPDATE`或`FOR SHARE`：
- 默认情况下，`InnoDB`对这些语句使用更强的锁，这 `SELECT`部分的作用类似于 `READ COMMITTED`，其中每个一致的读取，即使在同一个事务中，也会设置和读取自己的新快照。
- 要在这种情况下执行非锁定读取，请将事务的隔离级别设置为 `READ UNCOMMITTED`或 `READ COMMITTED`避免对从选定表读取的行设置锁定。

