## ELK




## Beats
数据传输
    FileBeat
    

## Logstash
9301
JRuby语言编写。Jordan Sissel 2.1.1
5000

Shipper - 发送日志数据(agent)
Broker - 收集数据，缺省内置Redis
Indexer - 数据写入

logstash-forwarder (lumberjack)

input
    lumberjack
        port
        type
        ssl_certificate
        ssl_key
filter
    grok
        match
            message
    date
        match
            timestamp
output
    elasticsearch
        hosts
        host
    stdout
        codec => rubydebug
    redis
        host
        data_type
        key

## Elasticsearch
实时
分布式
搜索分析引擎，开源，Lucene

全文检索
结构化搜索
分析

面向文档


启动：
cd elasticsearch-<version>
./bin/elasticsearch 
    -d 守护进程
    9200 RESTful API: curl -i -X<VERB> '<PROTOCOL>://<HOST>:<PORT>/<PATH>?<QUERY_STRING>' -d '<BODY>'
        _cluster/stats
        _nodes/stats/jvm
        _count
    9300 集群

集群
    elasticsearch.yml
    cluster.name

Java API 9300
    Node Client
    Transport Client


`curl http://localhost:9200/?pretty`
`curl http://localhost:9200/_search?pretty`

>= Java 7

192.168.50.10:9200
{
    name: "6A-ZHRO",
    cluster_name: "docker-cluster",
    cluster_uuid: "Da9Rhcg9Rm2wv5uUfS21bA",
    version: {
        number: "6.0.0",
        build_hash: "8f0685b",
        build_date: "2017-11-10T18:41:22.859Z",
        build_snapshot: false,
        lucene_version: "7.0.1",
        minimum_wire_compatibility_version: "5.6.0",
        minimum_index_compatibility_version: "5.0.0"
    },
    tagline: "You Know, for Search"
}

## kibana

Sense 交互式控制台
./bin/kibana plugin --install elastic/sense

./bin/kibana

`http://localhost:5601/app/sense`

5601
4.3.0
JavaScript语言编写，
http://192.168.50.10:5601
kibana.yml

index pattern
    identify the elasticsearch to run and analytics against
    configue fields
        logstash-*

curl -XPUT -D- 'http://localhost:9200/.kibana/doc/index-pattern:docker-elk' \
    -H 'Content-Type: application/json' \
    -d '{"type": "index-pattern", "index-pattern": {"title": "logstash-*", "timeFieldName": "@timestamp"}}'   
    

nc localhost 5000 < /path/to/logfile.log
也可以直接输入



* https://www.ibm.com/developerworks/cn/opensource/os-cn-elk/
