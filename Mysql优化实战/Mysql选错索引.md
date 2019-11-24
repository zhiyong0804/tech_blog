# Mysql选错索引
#### mysql选择索引依据
* 扫描行数
* 是否使用临时表
* 是否排序

#### Mysql获取索引基数
* 采样统计的时候，InnoDB 默认会选择 N 个数据页，统计这些页面上的不同值，得到一个平均值，然后乘以这个索引的页面数，就得到了这个索引的基数。
* 而数据表是会持续更新的，索引统计信息也不会固定不变。所以，当变更的数据行数超过 1/M 的时候，会自动触发重新做一次索引统计。
* 设置为 on 的时候，表示统计信息会持久化存储。这时，默认的 N 是 20，M 是 10。
* 设置为 off 的时候，表示统计信息只存储在内存中。这时，默认的 N 是 8，M 是 16。


#### 索引选择不准确矫正
* 索引统计信息不准确导致的问题，你可以用 analyze table 来解决。
* 优化器误判的情况，你可以在应用端用force index来强行指定索引
* 也可以通过修改语句来引导优化器
* 还可以通过增加或者删除索引来绕过这个问题

```
 CREATE TABLE `t` (
  `id` int(11) NOT NULL,
  `a` int(11) DEFAULT NULL,
  `b` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `a` (`a`),
  KEY `b` (`b`)
) ENGINE=InnoDB
```

#### 开启慢查询日志
```
  show variables like 'log_%';
  set sql_long_query=0;
  set global long_query_time=0;
  show variables like "%slow%";
  set slow_query_log='ON';
  show variables like "%slow%";
```
#### 查询优化
```
mysql> select * from t where a between 10000 and 20000 and b between 50000 and 100000 order by b limit 1;
Empty set (0.05 sec)

mysql> select * from t force index(a) where a between 10000 and 20000 and b between 50000 and 100000 order by b limit 1;
Empty set (0.02 sec)
```
#### 清空查询缓存
```
 reset query cache;
```

#### 选择错误索引原因
* 每次从索引 a 上拿到一个值，都要回到主键索引上查出整行数据，这个代价优化器也要算进去的。如果选择扫描 10 万行，是直接在主键索引上扫描的，没有额外的代价。优化器会估算这两个选择的代价，从结果看来，优化器认为直接扫描主键索引更快。当然，从执行时间看来，这个选择并不是最优的。

#### 优化错误索引
* force index
* 修改语句，引导mysql使用我们期望的索引
* 新建合适的索引或者删掉误用的索引
* 使用analze table 来解决



