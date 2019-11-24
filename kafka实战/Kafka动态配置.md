# Kafka动态配置
#### Dynamic Update Mode 
* read-only
* pre-broker
* cluster-wide

#### 使用场景
* 动态调整 Broker 端各种线程池大小，实时应对突发流量。
* 动态调整 Broker 端连接信息或安全配置信息。
* 动态更新 SSL Keystore 有效期。
* 动态调整 Broker 端 Compact 操作性能。
* 实时变更 JMX 指标收集器 (JMX Metrics Reporter)。

#### 参数
* log.retention.ms 日志停留时长
* num.io.threads 和 num.network.threads。
* SSL参数ssl.keystore.type、ssl.keystore.location、ssl.keystore.password 和 ssl.key.password
* num.replica.fetchers 增加该参数值，确保有充足的线程可以执行 Follower 副本向 Leader 副本的拉取