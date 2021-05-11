---
title: "MySQL事务与SQL优化"
date: 2021-03-01T19:43:16+08:00
categories: "MySQL"
description: "主要内容MySQL事务的语法、特性、并发问题、隔离级别、锁与SQL优化，"
tags : ["MySQL"]
---

### 一、事务的语法

非常简单

```sql
start tansaction; -- 事务开始，也可以换成begin;
... -- 一系列SQL语句完成一个操作
commit; -- 提交当前的修改
rollback; -- 撤销修改
``` 

### 二、事务四大特性

事务重要的特性，原子性，一致性，隔离性，持久性。

*   原子性(Atomicity)
事务的进行不可分割，是一个原子性的操作序列单元。事务中的操作要么全部执行成功，要么全部执行失败。如果事务中间环节出错，回滚事务开始的状态。
*   一致性(Consistency)
事务的执行不能破坏数据库数据的完整性和一致性。
一个事务执行前后，数据库必须处于一致性的状态，也就是数据在操作无误、逻辑无误的情况下，期望结果却与实际结果不一致，也就是数据错乱了。比如：把一个数据原为1000，减掉500，提交后查询数据不是500，说明事务受到了干涉，使事务不一致性。事务应该保持一致性。
*   隔离性(Isolation)
在并发环境下，并发的事务互相隔离。不同事务处理相同的数据都有自己完整的数据空间。
*   持久性(Duration)
事务一旦提交，就数据库的数据就永久保存下来。即使发生服务器宕机或崩溃等故障。数据库重启，就能恢复事务结束后的状态。

### 三、事务的三大并发问题

事务面临的三大并发问题，脏读，不可重复度，幻读。

*   脏读
读到了未提交的数据。比如事务A和事务B，A读取B更新的数据，B回滚撤销更新，A读到了脏数据。
*   不可重复读
同一个事务中同条命令得到不同的查询结果集。比如事务A和事务B，A多次读取数据，B在A读取过程中，更新数据并提交，导致A多次读取同一处数据，结果不一致。
*   幻读
重复查询过程中，数据量发生改变，可能增加，也可能较少，不是数据的内容增减。

### 四、事务隔离级别

数据库的事务存在一些隔离级别来解决一些并发问题，不同问题应该选择合适的隔离级别。

<table>
<thead>
<tr>
<th style="text-align:center">隔离级别</th>
<th style="text-align:center">脏读</th>
<th style="text-align:center">不可重复读</th>
<th style="text-align:center">幻读</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:center">读未提交(READ_UNCOMMITTED)</td>
<td style="text-align:center">✓</td>
<td style="text-align:center">✓</td>
<td style="text-align:center">✓</td>
</tr>
<tr>
<td style="text-align:center">读已提交(READ_COMMITTED)</td>
<td style="text-align:center">✗</td>
<td style="text-align:center">✓</td>
<td style="text-align:center">✓</td>
</tr>
<tr>
<td style="text-align:center">可重复读(REPEATABLE_READ)</td>
<td style="text-align:center">✗</td>
<td style="text-align:center">✗</td>
<td style="text-align:center">？</td>
</tr>
<tr>
<td style="text-align:center">顺序读(SERIALIZABLE)</td>
<td style="text-align:center">✗</td>
<td style="text-align:center">✗</td>
<td style="text-align:center">✗</td>
</tr>
</tbody>
</table>

从上往下，隔离级别越高，并发安全越高，但并发性越差。一般数据默认级别为读已提交或可重复读。

查询当前会话的事务隔离级别

```java
select @@tx_isolation;
select @@transaction_isolation; -- mysql8的命令

```

设置当前会话的事务隔离级别

```sql
set session transaction isolation level read uncommited;
set session transaction isolation level read commited;
set session transaction isolation level repeatable read;
set session transaction isolation level serializable;
```


一一介绍隔离级别

*   读未提交
事务A正处理某一数据，对其更新但未提交，事务未完成；事务B能够访问到更新后的数据，也就是会出现脏读。这个隔离级别适合只读的事务。
*   读已提交
不同事务只能读到已提交的数据。不会出现脏读，事务重复读取数据的过程中，其他事务提交了数据修改，那么重复读会出现结果前后不一致。无法解决不可重复读的问题。
*   可重复读
保证事务处理，多次读取同一处数据，同一数据的值与事务开始一致。解决脏读和不可重复读，但可能出现幻读。
*   顺序读
事务排队处理，不能并发，就不会出现并发问题，安全但效率低。

### 五、不同隔离级别锁

*   读未提交
有行级锁，无间隙锁，可读未提交数据
*   读提交
有行级锁，无间隙锁，读不到未提交的数据
*   可重复度
有行级锁，有间隙锁，每次读取的数据一致，可能有幻读
*   序列化
有行级锁，也有间隙锁，读表的时候上锁。

行级锁：把一个表的行上锁，只能有一个事务处理这个行。

间隙锁：一个在索引记录之间的间隙上的锁。保证某个间隙内的数据在锁定情况下不会发生任何变化。

### 六、隐式提交

DDL、DML、DCL、DQL都是隐式提交，在执行这些语句就相当于执行了commit，其实可以理解未数据库自动提交了。

### 七、数据库SQL优化

1.对查询进行优化，要尽量避免全表扫描，首先应考虑在 where 及 order by 涉及的列上建立索引

2.应尽量避免在 where 子句中对字段进行 null 值判断，否则将导致引擎放弃使用索引而进行全表扫

描，如：

```sql
select id from t where num is null;
```

最好不要给数据库留NULL，尽可能的使用 NOT NULL填充数据库.

备注、描述、评论之类的可以设置为 NULL，其他的，最好不要使用NULL。

3.应尽量避免在 where 子句中使用 != 或 &lt;&gt; 操作符，否则引擎将放弃使用索引而进行全表扫描。

4.应尽量避免在 where 子句中使用 or 来连接条件，如果一个字段有索引，一个字段没有索引，将导致

引擎放弃使用索引而进行全表扫描，如：

可以这样查询：

```sql
select id from t where num = 10 
union all 
select id from t where Name = 'admin';
```

5.in 和 not in 也要慎用，否则会导致全表扫描，如：

```sql
select id from t where num in(1,2,3);
```

对于连续的数值，能用 between 就不要用 in 了：

```sql
select id from t where num between 1 and 3;
```

很多时候用 exists 代替 in 是一个好的选择。

6.select语句避免使用*号。