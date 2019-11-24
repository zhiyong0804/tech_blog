# 高水位和Leader epoch
#### 高水位作用
* 定义消息可见性，即用来标识分区下的哪些消息是可以被消费者消费的。
* 帮助 Kafka 完成副本同步
* ![c2243d5887f0ca7a20a524914b85a8dd](media/15649289939803/c2243d5887f0ca7a20a524914b85a8dd.png)
* 高水位和 LEO 是副本对象的两个重要属性。Kafka 所有副本都有对应的高水位和 LEO 值，而不仅仅是 Leader 副本

#### 高水位更新机制
* leader的broker保存远程副本，帮助leader副本 确定高水位
* leader的LEO通过生产者消息写入磁盘更新
* folloer副本通过拉取消息，更新磁盘
* follower副本高水位取LEO和leader发来的高水位最小值确定高水位
* leader副本高水位取leader副本和所有同步的leo的最小值
* leader远程副本leo通过follower拉取消息上报的位移算

#### 副本同步判断
* 该远程 Follower 副本在 ISR 中。
* 该远程 Follower 副本 LEO 值落后于 Leader 副本 LEO 值的时间，不超过 Broker 端参数 replica.lag.time.max.ms 的值
* 先更新leo，然后更新高水位

#### Leader Epoch
* Epoch。一个单调增加的版本号。每当副本领导权发生变更时，都会增加该版本号。小版本号的 Leader 被认为是过期 Leader，不能再行使 Leader 权力。
* 起始位移（Start Offset）。Leader 副本在该 Epoch 值上写入的首条消息的位移。