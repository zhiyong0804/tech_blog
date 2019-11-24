# Open vSwitch
#### ovs安装
* https://www.jianshu.com/p/658332deac99

#### docker实验openvswitch
* https://segmentfault.com/a/1190000011406037

#### 云中网络Qos
* 云中的流量控制主要通过队列进行的，队列分为两大类：无类别队列规则和基于类别的队列规则。
* 在云中网络 Openvswitch 中，主要使用的是分层令牌桶规则（HTB），将总的带宽在一棵树上按照配置的比例进行分配，并且在一个分支不用的时候，可以借给另外的分支，从而增强带宽利用率