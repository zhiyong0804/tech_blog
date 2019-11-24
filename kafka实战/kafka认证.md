# kafka认证

#### 认证机制
* Broker和客户端的双路认证
* SASL

#### 对比
* SASL/SCRAM支持用户的动态删减

#### SCRAM
* 创建broker 

```
  KafkaServer {
  org.apache.kafka.common.security.scram.ScramLoginModule required
  username="admin"
  password="admin";
};-->kafka_broker_jaas.conf
```
* 启动broker

```
export KAFKA_OPTS=-Djava.security.auth.login.config=/root/kafka_2.12-2.2.0/config/kafka_broker_jaas.conf && kafka-server-start.sh server1.properties
```
* 启动生产者

```
producer.conf
security.protocol=SASL_PLAINTEXT
sasl.mechanism=SCRAM-SHA-256
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="writer" password="writer"; -->producer.conf
kafka-console-producer.sh --broker-list master:9092,node1:9093 --topic test_ssl  --producer.config producer.conf
```
* 启动消费者

```
consumer.conf
security.protocol=SASL_PLAINTEXT
sasl.mechanism=SCRAM-SHA-256
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="reader" password="reader"; -->consumer.conf
kafka-console-consumer.sh --bootstrap-server master:9092,node1:9093 --topic test --from-beginning --consumer.config consumer.conf
```
* 删除用户

```
kafka-configs.sh --zookeeper localhost:2181 --alter --delete-config 'SCRAM-SHA-256' --entity-type users --entity-name writer
kafka-configs.sh --zookeeper localhost:2181 --alter --delete-config 'SCRAM-SHA-512' --entity-type users --entity-name writer
```