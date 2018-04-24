mysql


## 分区
创建分区的字段必须是主键/唯一或主键/唯一的一部分。

分区算法
* list
* range
* hash
* key


删除分区：
key/hash 不会造成数据丢失。
range/list 会造成数据丢失。

alter table
* coalesce partition N
* add partition partitions N
* partition Name values in (n1,n2)


## 数据碎片

MyISAM:
optimize table Name;

InnoDB:
alter table Name engine=InnoDB;

* https://www.kancloud.cn/jdxia/mysql/
