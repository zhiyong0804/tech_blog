# kafka工具

#### 生产者和消费者性能测试
* kafka-producer-perf-test.sh --topic test1 --num-records 10000000 --throughput -1 --record-size 1024 --producer-props bootstrap.servers=master:9092 acks=-1 linger.ms=2000 compression.type=lz4
* kafka-consumer-perf-test.sh --broker-list master:9092 --messages 10000000 --topic test1

#### 查看消息主题的总数
* kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list master:9092 --time -2 --topic test1
* kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list master:9092 --time -1 --topic test1

#### 消息文件数据
* 