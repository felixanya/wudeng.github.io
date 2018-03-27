# kafka

* Producer API
* Consumer API
* Streams API
* Connector API

```
## 启动zookeeper
./bin/zookeeper-server-start.sh config/zookeeper.properties
## 启动kafka
./bin/kafka-server-start.sh config/server.properties
## 创建主题
./bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
## 列出主题
./bin/kafka-topics.sh --list --zookeeper localhost:2181

## 生产者
./bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test

## 消费者
./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```


9092

分布式消息系统。


为什么要用消息系统

* 解耦
* 冗余：比如后台给多个服务器发邮件，可能发邮件的时候刚好某些服务器出现问题了，比如重启等等没有收到请求。这样部分邮件发送成功，部分失败，如果使用消息队列则避免出现这种情况。
* 扩展性
* 灵活性&峰值处理能力
* 可恢复性
* 送达保证
* 顺序保证
* 缓冲
* 理解数据流
* 异步通信


producer: an application that sends message to kafka
message：
consumer: reads data from kafka

cluster, zookeeper

topic,
partition,
offset

consumer group


distributed comit log

data warehouse
etl: extract, transform, load
cdc: change data capture

data pipelines
big data ingest

key value, timestamp
immutable
persisted
