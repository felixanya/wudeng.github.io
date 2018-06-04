# mongodb

基于分布式文件存储的数据库。NoSQL (Not Only SQL)

CAP理论：一个分布式系统不可能同时很好的满足一致性，可用性和分区容错性这三个需求，最多只能同时较好的满足两个。
* 一致性 Consisstency
* 可用性 Availability
* 分隔容忍 Partition tolerance

BASE：Basically Available, Soft-state, Eventually Consistent。

## 安装

Ubuntu 16.04:
```
sudo apt install mongodb

mongo
```

27017

## server

```
mongod --dbpath c:\data\db
```

```
/usr/bin/mongod --config /etc/mongodb.conf
```

# client

mongo.exe

show dbs 显示所有数据库
db 显示当前数据库
help 帮助

2+2



database/collection(table)/document(row)/field(column)/index/primary key



* http://www.runoob.com/mongodb/mongodb-tutorial.html
