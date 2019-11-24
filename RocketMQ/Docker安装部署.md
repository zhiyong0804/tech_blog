# Docker安装部署

```
docker run \
--name node1-namesrv \
-p 9876:9876 \
 -d rocketmqinc/rocketmq sh mqnamesrv

docker run \
--name node1-broker-master \
-p 10911:10911 \
-p 10909:10909 \
-v /root/download/rocketmq/logs/broker-master:/root/logs \
-v /root/download/rocketmq/store/broker-master:/root/store \
-v /root/download/rocketmq/conf/2m-2s-sync/broker-master:/opt/rocketmq/conf \
-e "NAMESRV_ADDR=$master:9876;$node1:9876" \
-d rocketmqinc/rocketmq sh mqbroker -c /opt/rocketmq/conf/broker.conf

docker run \
--name node1-broker-slave \
-p 11011:10911 \
-p 11009:10909 \
-v /root/download/rocketmq/logs/broker-slave:/root/logs \
-v /root/download/rocketmq/store/broker-slave:/root/store \
-v /root/download/rocketmq/conf/2m-2s-sync/broker-slave:/opt/rocketmq/conf \
-e "NAMESRV_ADDR=$master:9876;$node1:9876" \
-d rocketmqinc/rocketmq sh mqbroker -c /opt/rocketmq/conf/broker.conf


docker run \
--name rocketmq-console \
-p 8081:8080 \
-v /root/download/rocketmq/logs/rocketmq-console:/root/logs \
-v /tmp/rocketmq-console:/tmp \
-e "NAMESRV_ADDR=$master:9876;$node1:9876" \
-d  styletang/rocketmq-console-ng

 docker run \
--name node2-namesrv \
-p 9876:9876 \
 -d rocketmqinc/rocketmq sh mqnamesrv

docker run \
--name node2-broker-master \
-p 10911:10911 \
-p 10909:10909 \
-v /root/download/rocketmq/logs/broker-master:/root/logs \
-v /root/download/rocketmq/store/broker-master:/root/store \
-v /root/download/rocketmq/conf/2m-2s-sync/broker-master:/opt/rocketmq/conf \
-e "NAMESRV_ADDR=$master:9876;$node1:9876" \
-d rocketmqinc/rocketmq sh mqbroker -c /opt/rocketmq/conf/broker.conf


docker run \
--name node2-broker-slave \
-p 11011:10911 \
-p 11009:10909 \
-v /root/download/rocketmq/logs/broker-slave:/root/logs \
-v /root/download/rocketmq/store/broker-slave:/root/store \
-v /root/download/rocketmq/conf/2m-2s-sync/broker-slave:/opt/rocketmq/conf \
-e "NAMESRV_ADDR=$master:9876;$node1:9876" \
-d rocketmqinc/rocketmq sh mqbroker -c /opt/rocketmq/conf/broker.conf

```