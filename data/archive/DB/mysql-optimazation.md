# Mysql性能优化

* 数据的存储格式：堆组织表 vs 聚集索引
* 并发控制协议：MVCC vs Lock-Based CC
* Two Phase Locking
* Isolation Level
* 执行计划，主键扫描，唯一键扫描，范围扫描，全表扫描
* Index Condition Pushdown, Semi-Consistent Read

## 执行优化


## 聚集索引 clustered index

Innodb数据存储方式，既存储索引，也存储行值，数据存储在索引的叶子页。每个表有且只有一个聚集索引。

* 如果定义了主键，那么主键就作为聚集索引。
* 如果没有主键，那么该表的第一个唯一非空索引作为聚集索引。
* 如果没有主键也没有合适的唯一索引，那么innodb内部会生成一个隐藏的主键作为聚集索引。这个主键是一个6字节的列，随着数据的插入自增。

innodb中，次级索引存储主键的值。次级索引的叶子节点并不存储行数据的物理地址，而是存储该行的主键值。主键扮演了行数据的指针。

因为物理存储通过主键，索引主键数据查找效率很高。区间查找也很快速。


* Index Key
* Index Filter
* Table Filter

## 运维优化

show grants for username;


show [global | session] variables [like pattern];

max_connections
    本地服务器上默认值100

## 结构优化

## 常用SQL
* drop table if exists `table_name`;

* https://dev.mysql.com/doc/refman/5.7/en/mysql-indexes.html
