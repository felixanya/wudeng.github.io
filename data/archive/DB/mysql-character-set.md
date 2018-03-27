# Mysql字符集和字符排序

4 levels:
* server
    * character-set-server
    * collation-server
    * 如果创建数据库的时候没有指定字符集，则用服务器字符集作为默认的数据库字符集
* database
    * `use db_name;`
    `select @@character_set_database, @@collation_database;`
* table
* column

CHARACTER SET == CHARSET
COLLATION

* string
    * introducer: `_charset_name`
`[_charset_name]'string'[COLLATE collation_name]`
`SELECT _utf8'abc' COLLATE utf8_danish_ci;`

* data storage
* communication

`set names chaset_name;`
等价于
```
SET character_set_client = charset_name;
SET character_set_results = charset_name;
SET character_set_connection = charset_name;
```

alias dba='mysql -uroot -proot --default-character-set="utf8"'

charset utf8;


COLLATION

| Suffix | Meaning            |
|--------|--------------------|
| _ai    | accent insensitive |
| _as    | accent sensitive   |
| _ci    | case insensitive   |
| _cs    | case sensitive     |
| _bin   | Binary             |


show variables like 'character%';
show variables like 'collation%';

utf8
utf8mb4
