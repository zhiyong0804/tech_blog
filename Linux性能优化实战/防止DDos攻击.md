# 防止DDos攻击
#### 类型
* 耗尽带宽
* 耗尽操作系统的资源
* 消耗应用程序的运行资源

#### 优化
*  通过iptables限制syn并发数
*  限制单个IP在60s新建立的连接数

##### 修改tcp参数
* net.ipv4.tcp_syncookies = 1
* net.ipv4.tcp_synack_retries = 1
* net.ipv4.tcp_max_syn_backlog = 1024

##### 基于XDP和DPDK构建DDOS方案，跳过内核协议栈，来识别并丢弃DDos报文

#### 流量清洗设备和网络防火墙

#### 查看攻击源头的Ddos地址
* sar -n  DEV 4
* netstat  | wc -l
* 
