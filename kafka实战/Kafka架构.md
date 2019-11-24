# Kafka架构

#### 系统结构图
* Producer
* Broker
* Consumer
* Zookeeper
* ![](media/15438859299782/15438861838653.jpg)

#### Topic&Partition
* 一个Topic包含一个或者多个Partition
* 每个消息都只属于一个topic
* produer和consumer都要指定topic

#### Partition
* 一个Partion只分布于一个Broker
* 一个Partion对应一个文件夹
* 一个Partion包含多个Segment
* 一个Segment对应一个文件
* Segment由一个个不可变记录组成
* 记录只会append到Segment中，不会被单独删除或者修改
* 清除过期日志时，直接删除一个或者多个Segment
* 创建新topic

```
 ./kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 3 --topic docker-log
```


#### Sync Produer
* 低延迟
* 低吞吐率
* 无数据丢失

#### Aync Produer
* 高延迟
* 高吞吐率
* 可能有数据丢失

#### kafka设计目的
* 提供一套 API 实现生产者和消费者；
* 降低网络传输和磁盘存储开销；
* 实现高伸缩性架构。